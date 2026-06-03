---
type: spec
project: ecommerce-music
module: cart
status: pending-approval
created: 2026-06-03
---

# Problem
Currently, users can add music tracks that they have already purchased back into their cart. Additionally, when viewing a track's detail page, the UI still displays the "Add to Cart" button instead of indicating that the track has already been purchased.

# Goal
Prevent users from buying or adding already purchased music to their cart on both the backend API and the frontend UI. Update the related SRS documentation to match the new business rule.

# Scope
- **Backend:** 
  - Add validation in `CartService.addToCart` to block adding a music track that has already been purchased by the current buyer.
  - Return a `409 Conflict` status code and a descriptive error message when this rule is violated.
- **Frontend:**
  - Update `AddToCartButton` and `MusicPurchasePanel` in the music detail view to disable the button and display "Already Purchased" if the buyer has already bought the track.
- **Documentation:**
  - Update the existing `SRS-Cart.md` to document this new business validation rule.

# User Flow
1. Buyer logs in and opens a track detail page.
2. If the buyer has already purchased the track, the "Add to Cart" button is disabled and displays "Already Purchased".
3. If the buyer tries to call the backend API directly to add the purchased track (`POST /api/v1/buyer/cart/{musicId}`), the system responds with a `409 Conflict` error.

# Technical Design
- **Backend:**
  - Inject `PurchasedLibraryRepository` into `CartServiceImpl`.
  - In `CartServiceImpl.addToCart(Long musicId)`, query `purchasedLibraryRepository.existsByBuyerIdAndMusicId(buyer.getId(), musicId)`.
  - If true, throw a `ConflictException` with the message `"You have already purchased this music"`.
- **Frontend:**
  - In `music-detail-view.tsx`, the `MusicDetailPageView` component retrieves `ownedTrackIds` from the `useMusicPlayerStore`.
  - Check if the current track ID is in `ownedTrackIds` (`isBuyerOwnedTrack`).
  - Pass the purchase state or check it directly in `AddToCartButton`.
  - Disable the button and display "Already Purchased" if `isBuyerOwnedTrack` is true.

# API & Error Handling
- `POST /api/v1/buyer/cart/{musicId}`
  - Response: `409 Conflict`
  - Body: `{ "message": "You have already purchased this music" }` (or standard `ApiResponse` wrapping this error)

# Edge Cases
- Adding a track that the buyer has already purchased -> `409 Conflict`.
- User is a Seller or Admin: their flow does not allow adding to cart (existing checks apply).
- User is not logged in: they see the "Add to Cart" button and redirect to login on click (existing check).

# Acceptance Criteria
- [ ] Backend API rejects adding already purchased music with `409 Conflict`.
- [ ] Frontend detail page disables adding to cart and shows "Already Purchased" for owned tracks.
- [ ] `SRS-Cart.md` is updated with the new rule.
