# Prevent Purchasing Already Purchased Music Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Prevent buyers from adding already purchased music tracks back into their cart on both the backend API and the frontend UI, and update the SRS documentation.

**Architecture:** Inject `PurchasedLibraryRepository` into `CartServiceImpl` to reject addition of already owned tracks. On the frontend, utilize the `ownedTrackIds` state from `useMusicPlayerStore` to disable the "Add to Cart" button in the detail view.

**Tech Stack:** Node.js, Spring Boot (Java), JUnit 5, Mockito, React/Next.js (TypeScript)

---

### Task 1: Documentation Update

**Files:**
- Modify: [SRS-Cart.md](file:///c:/Users/USER-PC/OneDrive/Desktop/AI-Music-System/obsidian-vault/02-SRS/SRS-Cart.md)

- [ ] **Step 1: Document the new edge case and acceptance criteria**
  Update the "Edge Cases" and "Acceptance Criteria" sections to document the already purchased restriction.
  
  ```markdown
  # Edge Cases
  ...
  - Adding a music that has already been purchased by the buyer -> 409 Conflict.
  
  # Acceptance Criteria
  ...
  - [ ] Adding an already purchased music returns 409 Conflict.
  ```

- [ ] **Step 2: Commit documentation changes**
  ```bash
  git add obsidian-vault/02-SRS/SRS-Cart.md
  git commit -m "docs(cart): add edge case for adding already purchased music to cart"
  ```

---

### Task 2: Backend - Add Failing Test (RED Phase)

**Files:**
- Modify: [CartServiceTest.java](file:///c:/Users/USER-PC/OneDrive/Desktop/AI-Music-System/workspace/e-commerce_music_be/src/test/java/com/tien/ecommerce_music_be/service/CartServiceTest.java)

- [ ] **Step 1: Inject Mock PurchasedLibraryRepository and add failing test**
  Add mock dependency `PurchasedLibraryRepository` at the top of the test class, and add the `addToCart_alreadyPurchasedThrowsConflict` test.
  
  ```java
  // In CartServiceTest.java:
  @Mock
  private PurchasedLibraryRepository purchasedLibraryRepository;
  
  @Test
  void addToCart_alreadyPurchasedThrowsConflict() {
      when(userRepository.findByIdAndDeletedAtIsNull(10L)).thenReturn(Optional.of(buyer));
      Music music = new Music();
      music.setId(4L);
      music.setStatus(MusicStatus.PUBLISHED);
      when(musicRepository.findByIdAndDeletedAtIsNull(4L)).thenReturn(Optional.of(music));
      when(purchasedLibraryRepository.existsByBuyerIdAndMusicId(10L, 4L)).thenReturn(true);

      ConflictException ex = assertThrows(ConflictException.class, () -> cartService.addToCart(4L));
      assertEquals("You have already purchased this music", ex.getMessage());
  }
  ```

- [ ] **Step 2: Run tests to verify compilation/test failure**
  Run: `./gradlew test --tests com.tien.ecommerce_music_be.service.CartServiceTest` in the `workspace/e-commerce_music_be` directory.
  Expected: Compilation failure because `CartServiceImpl` constructor does not accept `PurchasedLibraryRepository` yet.

---

### Task 3: Backend - Implement Validation (GREEN Phase)

**Files:**
- Modify: [CartServiceImpl.java](file:///c:/Users/USER-PC/OneDrive/Desktop/AI-Music-System/workspace/e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/service/CartServiceImpl.java)

- [ ] **Step 1: Update constructor and inject PurchasedLibraryRepository**
  Inject `PurchasedLibraryRepository` field and add it as a constructor parameter.

  ```java
  // In CartServiceImpl.java:
  private final CartItemRepository cartItemRepository;
  private final UserRepository userRepository;
  private final MusicRepository musicRepository;
  private final PurchasedLibraryRepository purchasedLibraryRepository;

  public CartServiceImpl(CartItemRepository cartItemRepository, 
                         UserRepository userRepository, 
                         MusicRepository musicRepository,
                         PurchasedLibraryRepository purchasedLibraryRepository) {
      this.cartItemRepository = cartItemRepository;
      this.userRepository = userRepository;
      this.musicRepository = musicRepository;
      this.purchasedLibraryRepository = purchasedLibraryRepository;
  }
  ```

- [ ] **Step 2: Add validation logic in addToCart**
  Add the validation check before inserting a new cart item:

  ```java
  // In addToCart(Long musicId):
  if (purchasedLibraryRepository.existsByBuyerIdAndMusicId(buyer.getId(), musicId)) {
      throw new ConflictException("You have already purchased this music");
  }
  ```

- [ ] **Step 3: Run tests to verify they pass**
  Run: `./gradlew test --tests com.tien.ecommerce_music_be.service.CartServiceTest`
  Expected: All tests pass.

- [ ] **Step 4: Commit backend changes**
  ```bash
  git add workspace/e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/service/CartServiceImpl.java workspace/e-commerce_music_be/src/test/java/com/tien/ecommerce_music_be/service/CartServiceTest.java
  git commit -m "feat(cart): prevent adding already purchased music to cart"
  ```

---

### Task 4: Frontend - Disable "Add to Cart" for Purchased Music

**Files:**
- Modify: [music-detail-view.tsx](file:///c:/Users/USER-PC/OneDrive/Desktop/AI-Music-System/workspace/e-commerce_music_fe/src/features/music/components/music-detail-view.tsx)

- [ ] **Step 1: Update AddToCartButton to take isBuyerOwnedTrack prop or check ownership**
  Modify `AddToCartButton` to receive the `isBuyerOwnedTrack` flag and disable/update the text accordingly.

  ```tsx
  // Modify AddToCartButton signature and usages:
  function AddToCartButton({ musicId, isBuyerOwnedTrack }: { musicId: string; isBuyerOwnedTrack: boolean }) {
      ...
      const already = items.includes(musicId) || isBuyerOwnedTrack;
      ...
      // Render text conditional on ownership:
      {isAdding ? (
        <span className="flex items-center gap-2">Adding...</span>
      ) : success ? (
        <span className="flex items-center gap-2">
          <Check className="size-4 text-emerald-300" /> Added
        </span>
      ) : isBuyerOwnedTrack ? (
        <span>Already Purchased</span>
      ) : already ? (
        <span>Already Added</span>
      ) : (
        <span>Add To Cart</span>
      )}
  ```

- [ ] **Step 2: Update AddToCartButton instantiation in MusicPurchasePanel**
  Pass the ownership state down to the button. In `MusicDetailPageView`, we determine `isBuyerOwnedTrack`:
  
  ```tsx
  // In MusicDetailPageView:
  const isBuyerOwnedTrack = ownedTrackIds.includes(Number(initialData.id))
  
  // Pass isBuyerOwnedTrack to MusicPurchasePanel:
  <MusicPurchasePanel price={initialData.price} musicId={String(initialData.id)} isBuyerOwnedTrack={isBuyerOwnedTrack} />

  // Update MusicPurchasePanel to pass it to AddToCartButton:
  function MusicPurchasePanel({ price, musicId, isBuyerOwnedTrack }: { price?: number | string | null; musicId: string; isBuyerOwnedTrack: boolean }) {
      ...
      <AddToCartButton musicId={musicId} isBuyerOwnedTrack={isBuyerOwnedTrack} />
  ```

- [ ] **Step 3: Verify frontend build**
  Run: `npm run build` in the `workspace/e-commerce_music_fe` directory to make sure compilation succeeds.
  Expected: PASS

- [ ] **Step 4: Commit frontend changes**
  ```bash
  git add workspace/e-commerce_music_fe/src/features/music/components/music-detail-view.tsx
  git commit -m "feat(fe): disable add to cart button and show already purchased on music detail page"
  ```
