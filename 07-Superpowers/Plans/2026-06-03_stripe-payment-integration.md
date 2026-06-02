# Stripe Payment Integration Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Thay thế thanh toán giả lập Sandbox bằng Stripe Checkout Session thực tế, hiển thị line items bài hát và thực hiện verify thanh toán trước khi cấp quyền tải nhạc.

**Architecture:** Sử dụng `StripeService` wrapper để cô lập tương tác với Stripe SDK, giúp viết unit test bằng Mockito dễ dàng. Cập nhật `OrderServiceImpl` để tạo Checkout Session trả về checkoutUrl, đồng thời thêm API `confirmCheckout` xác nhận giao dịch thành công mới lưu thư viện nhạc và xóa giỏ hàng.

**Tech Stack:** Node.js (React / Next.js), Spring Boot (Java), Gradle, Stripe Java SDK, Vitest (nếu có).

---

### Task 1: Thêm Thư viện Stripe & Cấu hình Hệ thống

**Files:**
- Modify: [build.gradle](file:///c:/Users/USER-PC/OneDrive/Desktop/AI-Music-System/workspace/e-commerce_music_be/build.gradle)
- Modify: [application.properties](file:///c:/Users/USER-PC/OneDrive/Desktop/AI-Music-System/workspace/e-commerce_music_be/src/main/resources/application.properties)

- [ ] **Step 1: Thêm dependency `stripe-java`**
  Thêm dòng sau vào danh sách dependencies trong `workspace/e-commerce_music_be/build.gradle`:
  ```groovy
  implementation 'com.stripe:stripe-java:28.3.0'
  ```

- [ ] **Step 2: Cấu hình `stripe.api.key`**
  Thêm dòng cấu hình Stripe key vào `workspace/e-commerce_music_be/src/main/resources/application.properties`:
  ```properties
  stripe.api.key=${STRIPE_API_KEY:sk_test_dummy}
  ```

- [ ] **Step 3: Chạy build dự án**
  Chạy lệnh gradle để tải thư viện mới và xác nhận không có lỗi build:
  Run: `cmd.exe /c "gradlew.bat compileJava"`
  Expected: BUILD SUCCESSFUL

- [ ] **Step 4: Commit thay đổi**
  ```bash
  git add workspace/e-commerce_music_be/build.gradle workspace/e-commerce_music_be/src/main/resources/application.properties
  git commit -m "build: add stripe-java dependency and config property"
  ```

---

### Task 2: Định nghĩa Model & Enum cho Stripe

**Files:**
- Modify: [PaymentMethod.java](file:///c:/Users/USER-PC/OneDrive/Desktop/AI-Music-System/workspace/e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/enums/PaymentMethod.java)
- Modify: [OrderResponse.java](file:///c:/Users/USER-PC/OneDrive/Desktop/AI-Music-System/workspace/e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/dto/response/OrderResponse.java)
- Modify: [OrderMapper.java](file:///c:/Users/USER-PC/OneDrive/Desktop/AI-Music-System/workspace/e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/mapper/OrderMapper.java)

- [ ] **Step 1: Viết test kiểm tra Enum PaymentMethod chứa giá trị STRIPE**
  Tạo hoặc thêm kiểm tra trong `src/test/java/com/tien/ecommerce_music_be/enums/PaymentMethodTest.java` (nếu chưa có thì tạo mới):
  ```java
  package com.tien.ecommerce_music_be.enums;
  
  import org.junit.jupiter.api.Test;
  import static org.junit.jupiter.api.Assertions.assertNotNull;
  
  class PaymentMethodTest {
      @Test
      void testEnumContainsStripe() {
          assertNotNull(PaymentMethod.valueOf("STRIPE"));
      }
  }
  ```
  Run: `cmd.exe /c "gradlew.bat test --tests com.tien.ecommerce_music_be.enums.PaymentMethodTest"`
  Expected: FAIL (Chưa khai báo STRIPE trong Enum)

- [ ] **Step 2: Cập nhật `PaymentMethod.java`**
  Thêm `STRIPE` vào enum:
  ```java
  package com.tien.ecommerce_music_be.enums;
  
  public enum PaymentMethod {
      SANDBOX,
      STRIPE
  }
  ```

- [ ] **Step 3: Chạy test lại**
  Run: `cmd.exe /c "gradlew.bat test --tests com.tien.ecommerce_music_be.enums.PaymentMethodTest"`
  Expected: PASS

- [ ] **Step 4: Cập nhật DTO `OrderResponse.java`**
  Thêm trường `checkoutUrl` để lưu Stripe URL:
  ```java
  private String checkoutUrl;
  ```

- [ ] **Step 5: Cập nhật Mapper `OrderMapper.java`**
  MapStruct tự động bỏ qua nếu trường không tồn tại trong source Entity (Order), nhưng để an toàn, cấu hình ignore rõ ràng hoặc để mặc định. Hãy cập nhật MapStruct annotation trong `OrderMapper.java`:
  ```java
  @Mapping(target = "itemsCount", expression = "java(order.getItems() == null ? 0 : order.getItems().size())")
  @Mapping(target = "checkoutUrl", ignore = true)
  OrderResponse toResponse(Order order);
  ```

- [ ] **Step 6: Chạy build gradle kiểm tra Mapper compile**
  Run: `cmd.exe /c "gradlew.bat compileJava"`
  Expected: BUILD SUCCESSFUL

- [ ] **Step 7: Commit thay đổi**
  ```bash
  git add workspace/e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/enums/PaymentMethod.java workspace/e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/dto/response/OrderResponse.java workspace/e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/mapper/OrderMapper.java
  git commit -m "feat: update PaymentMethod enum and OrderResponse DTO for Stripe"
  ```

---

### Task 3: Định nghĩa StripeService & Repository Query

**Files:**
- Create: [StripeService.java](file:///c:/Users/USER-PC/OneDrive/Desktop/AI-Music-System/workspace/e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/service/StripeService.java)
- Create: [StripeServiceImpl.java](file:///c:/Users/USER-PC/OneDrive/Desktop/AI-Music-System/workspace/e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/service/StripeServiceImpl.java)
- Modify: [OrderRepository.java](file:///c:/Users/USER-PC/OneDrive/Desktop/AI-Music-System/workspace/e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/repository/OrderRepository.java)

- [ ] **Step 1: Viết test cho `OrderRepository.findByOrderCode`**
  Thêm test case vào một test suite DB (hoặc tạo test mới) để đảm bảo tìm được Order bằng `orderCode`:
  Tạo `src/test/java/com/tien/ecommerce_music_be/repository/OrderRepositoryTest.java`:
  ```java
  package com.tien.ecommerce_music_be.repository;
  
  import org.junit.jupiter.api.Test;
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
  import static org.junit.jupiter.api.Assertions.assertFalse;
  
  @DataJpaTest
  class OrderRepositoryTest {
      @Autowired
      private OrderRepository orderRepository;
  
      @Test
      void testFindByOrderCodeEmpty() {
          assertFalse(orderRepository.findByOrderCode("NONEXIST").isPresent());
      }
  }
  ```
  Run: `cmd.exe /c "gradlew.bat test --tests com.tien.ecommerce_music_be.repository.OrderRepositoryTest"`
  Expected: FAIL (chưa định nghĩa findByOrderCode trong OrderRepository)

- [ ] **Step 2: Thêm query method vào `OrderRepository.java`**
  Thêm phương thức sau:
  ```java
  @EntityGraph(attributePaths = {"buyer"})
  Optional<Order> findByOrderCode(String orderCode);
  ```

- [ ] **Step 3: Chạy test lại**
  Run: `cmd.exe /c "gradlew.bat test --tests com.tien.ecommerce_music_be.repository.OrderRepositoryTest"`
  Expected: PASS

- [ ] **Step 4: Tạo Interface `StripeService.java`**
  Tạo file mới:
  ```java
  package com.tien.ecommerce_music_be.service;
  
  import com.tien.ecommerce_music_be.entity.CartItem;
  import com.tien.ecommerce_music_be.entity.Order;
  import java.util.List;
  
  public interface StripeService {
      String createCheckoutSession(Order order, List<CartItem> cartItems);
      boolean isSessionPaid(String sessionId);
      String getOrderCodeFromSession(String sessionId);
  }
  ```

- [ ] **Step 5: Tạo class triển khai `StripeServiceImpl.java`**
  Tạo file mới:
  ```java
  package com.tien.ecommerce_music_be.service;
  
  import com.stripe.Stripe;
  import com.stripe.model.checkout.Session;
  import com.stripe.param.checkout.SessionCreateParams;
  import com.tien.ecommerce_music_be.entity.CartItem;
  import com.tien.ecommerce_music_be.entity.Music;
  import com.tien.ecommerce_music_be.entity.Order;
  import com.tien.ecommerce_music_be.exception.BadRequestException;
  import jakarta.annotation.PostConstruct;
  import java.math.BigDecimal;
  import java.util.List;
  import org.springframework.beans.factory.annotation.Value;
  import org.springframework.stereotype.Service;
  
  @Service
  public class StripeServiceImpl implements StripeService {
  
      @Value("${stripe.api.key}")
      private String stripeApiKey;
  
      @PostConstruct
      public void init() {
          Stripe.apiKey = stripeApiKey;
      }
  
      @Override
      public String createCheckoutSession(Order order, List<CartItem> cartItems) {
          try {
              SessionCreateParams.Builder paramsBuilder = SessionCreateParams.builder()
                      .setMode(SessionCreateParams.Mode.PAYMENT)
                      .setSuccessUrl("http://localhost:3000/checkout/success?session_id={CHECKOUT_SESSION_ID}")
                      .setCancelUrl("http://localhost:3000/checkout")
                      .putMetadata("orderCode", order.getOrderCode());
  
              for (CartItem item : cartItems) {
                  Music music = item.getMusic();
                  BigDecimal price = music.getPrice() == null ? BigDecimal.ZERO : music.getPrice();
                  long amountInCents = price.multiply(new BigDecimal("100")).longValue();
  
                  SessionCreateParams.LineItem.PriceData.ProductData.Builder productBuilder = 
                          SessionCreateParams.LineItem.PriceData.ProductData.builder()
                                  .setName(music.getTitle())
                                  .setDescription("Artist: " + music.getArtistName());
  
                  if (music.getThumbnailUrl() != null && !music.getThumbnailUrl().isBlank()) {
                      productBuilder.addImage(music.getThumbnailUrl());
                  }
  
                  SessionCreateParams.LineItem.PriceData priceData = SessionCreateParams.LineItem.PriceData.builder()
                          .setCurrency("usd")
                          .setUnitAmount(amountInCents)
                          .setProductData(productBuilder.build())
                          .build();
  
                  paramsBuilder.addLineItem(
                          SessionCreateParams.LineItem.builder()
                                  .setQuantity(1L)
                                  .setPriceData(priceData)
                                  .build()
                  );
              }
  
              // Tính thuế 5%
              BigDecimal totalPrice = order.getTotalAmount();
              BigDecimal taxAmount = totalPrice.multiply(new BigDecimal("0.05")).setScale(2, java.math.RoundingMode.HALF_UP);
              long taxInCents = taxAmount.multiply(new BigDecimal("100")).longValue();
  
              if (taxInCents > 0) {
                  paramsBuilder.addLineItem(
                          SessionCreateParams.LineItem.builder()
                                  .setQuantity(1L)
                                  .setPriceData(
                                          SessionCreateParams.LineItem.PriceData.builder()
                                                  .setCurrency("usd")
                                                  .setUnitAmount(taxInCents)
                                                  .setProductData(
                                                          SessionCreateParams.LineItem.PriceData.ProductData.builder()
                                                                  .setName("Tax (5%)")
                                                                  .build()
                                                  )
                                                  .build()
                                  )
                                  .build()
                  );
              }
  
              Session session = Session.create(paramsBuilder.build());
              return session.getUrl();
          } catch (Exception e) {
              throw new BadRequestException("Failed to create Stripe Checkout Session: " + e.getMessage());
          }
      }
  
      @Override
      public boolean isSessionPaid(String sessionId) {
          try {
              Session session = Session.retrieve(sessionId);
              return "paid".equals(session.getPaymentStatus());
          } catch (Exception e) {
              throw new BadRequestException("Failed to retrieve Stripe Session: " + e.getMessage());
          }
      }
  
      @Override
      public String getOrderCodeFromSession(String sessionId) {
          try {
              Session session = Session.retrieve(sessionId);
              if (session.getMetadata() == null) {
                  return null;
              }
              return session.getMetadata().get("orderCode");
          } catch (Exception e) {
              throw new BadRequestException("Failed to retrieve Stripe Session metadata: " + e.getMessage());
          }
      }
  }
  ```

- [ ] **Step 6: Chạy build compile**
  Run: `cmd.exe /c "gradlew.bat compileJava"`
  Expected: BUILD SUCCESSFUL

- [ ] **Step 7: Commit thay đổi**
  ```bash
  git add workspace/e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/service/StripeService.java workspace/e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/service/StripeServiceImpl.java workspace/e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/repository/OrderRepository.java
  git commit -m "feat: add StripeService and query to find order by code"
  ```

---

### Task 4: Cập nhật OrderService & Triển khai Checkout Flow mới

**Files:**
- Modify: [OrderService.java](file:///c:/Users/USER-PC/OneDrive/Desktop/AI-Music-System/workspace/e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/service/OrderService.java)
- Modify: [OrderServiceImpl.java](file:///c:/Users/USER-PC/OneDrive/Desktop/AI-Music-System/workspace/e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/service/OrderServiceImpl.java)
- Modify: [OrderServiceTest.java](file:///c:/Users/USER-PC/OneDrive/Desktop/AI-Music-System/workspace/e-commerce_music_be/src/test/java/com/tien/ecommerce_music_be/service/OrderServiceTest.java)

- [ ] **Step 1: Viết test lỗi cho luồng checkout và confirm mới**
  Mở `OrderServiceTest.java`. Thay đổi cách mock `paymentService` thành `StripeService`.
  Viết lại `checkoutShouldCreatePaidOrderAndClearCart` thành `checkoutShouldCreatePendingOrderAndReturnStripeUrl` và viết thêm test case cho `confirmCheckout`.
  
  Cụ thể, chỉnh sửa phần khai báo và thêm mock:
  ```java
  @Mock
  private StripeService stripeService; // Đổi từ PaymentService
  ```
  Thêm các test case sau vào `OrderServiceTest.java`:
  ```java
      @Test
      void checkoutShouldCreatePendingOrderAndReturnStripeUrl() {
          when(userRepository.findByIdAndDeletedAtIsNull(10L)).thenReturn(Optional.of(buyer));
  
          Music music = Music.builder()
                  .id(1L)
                  .title("Track One")
                  .artistName("Artist One")
                  .thumbnailUrl("https://cdn/thumb.jpg")
                  .price(new BigDecimal("9.99"))
                  .status(MusicStatus.PUBLISHED)
                  .build();
  
          CartItem cartItem = CartItem.builder()
                  .id(100L)
                  .buyer(buyer)
                  .music(music)
                  .build();
  
          when(cartItemRepository.findByBuyerIdWithMusic(10L)).thenReturn(List.of(cartItem));
          when(purchasedLibraryRepository.existsByBuyerIdAndMusicId(10L, 1L)).thenReturn(false);
          when(orderRepository.save(org.mockito.ArgumentMatchers.any(Order.class))).thenAnswer(invocation -> {
              Order order = invocation.getArgument(0);
              if (order.getId() == null) {
                  order.setId(99L);
              }
              return order;
          });
          
          when(stripeService.createCheckoutSession(org.mockito.ArgumentMatchers.any(Order.class), org.mockito.ArgumentMatchers.anyList()))
                  .thenReturn("https://checkout.stripe.com/pay/cs_test_123");
                  
          when(orderMapper.toResponse(org.mockito.ArgumentMatchers.any(Order.class))).thenAnswer(invocation -> {
              Order order = invocation.getArgument(0);
              return OrderResponse.builder()
                  .id(order.getId())
                  .orderCode(order.getOrderCode())
                  .totalAmount(order.getTotalAmount())
                  .orderStatus(order.getOrderStatus())
                  .paymentMethod(order.getPaymentMethod())
                  .paymentStatus(order.getPaymentStatus())
                  .createdAt(order.getCreatedAt())
                  .itemsCount(order.getItems() == null ? 0 : order.getItems().size())
                  .build();
          });
  
          OrderResponse response = orderService.checkout();
  
          assertEquals("PENDING", response.getOrderStatus().name());
          assertEquals("PENDING", response.getPaymentStatus().name());
          assertEquals("STRIPE", response.getPaymentMethod().name());
          assertEquals("https://checkout.stripe.com/pay/cs_test_123", response.getCheckoutUrl());
      }
      
      @Test
      void confirmCheckoutShouldMarkPaidAndClearCart() {
          Order order = Order.builder()
                  .id(99L)
                  .orderCode("ORD-12345")
                  .buyer(buyer)
                  .totalAmount(new BigDecimal("9.99"))
                  .orderStatus(OrderStatus.PENDING)
                  .paymentMethod(PaymentMethod.STRIPE)
                  .paymentStatus(PaymentStatus.PENDING)
                  .build();
                  
          Music music = Music.builder().id(1L).title("Track One").price(new BigDecimal("9.99")).build();
          OrderItem item = OrderItem.builder().id(1L).order(order).music(music).build();
          order.setItems(List.of(item));
          
          CartItem cartItem = CartItem.builder().id(100L).buyer(buyer).music(music).build();
  
          when(stripeService.getOrderCodeFromSession("cs_test_123")).thenReturn("ORD-12345");
          when(stripeService.isSessionPaid("cs_test_123")).thenReturn(true);
          when(orderRepository.findByOrderCode("ORD-12345")).thenReturn(Optional.of(order));
          when(cartItemRepository.findByBuyerIdWithMusic(10L)).thenReturn(List.of(cartItem));
          
          when(orderMapper.toResponse(org.mockito.ArgumentMatchers.any(Order.class))).thenAnswer(invocation -> {
              Order o = invocation.getArgument(0);
              return OrderResponse.builder()
                  .id(o.getId())
                  .orderCode(o.getOrderCode())
                  .orderStatus(o.getOrderStatus())
                  .paymentStatus(o.getPaymentStatus())
                  .build();
          });
  
          OrderResponse response = orderService.confirmCheckout("cs_test_123");
  
          assertEquals("PAID", response.getOrderStatus().name());
          assertEquals("SUCCESS", response.getPaymentStatus().name());
          verify(cartItemRepository).deleteAll(org.mockito.ArgumentMatchers.anyList());
          verify(purchasedLibraryRepository).saveAll(org.mockito.ArgumentMatchers.anyList());
      }
  ```
  Run: `cmd.exe /c "gradlew.bat test --tests com.tien.ecommerce_music_be.service.OrderServiceTest"`
  Expected: FAIL (Compile error do `OrderService` chưa có `confirmCheckout` và `OrderServiceImpl` chưa dùng `StripeService`)

- [ ] **Step 2: Cập nhật Interface `OrderService.java`**
  Thêm phương thức:
  ```java
  OrderResponse confirmCheckout(String sessionId);
  ```

- [ ] **Step 3: Cập nhật `OrderServiceImpl.java`**
  Thay thế `PaymentService` bằng `StripeService`.
  Cập nhật logic `checkout()` để không hoàn tất thanh toán ngay, mà tạo đơn hàng ở trạng thái `PENDING`, thiết lập `paymentMethod = STRIPE`, gọi `stripeService.createCheckoutSession` và trả về response kèm `checkoutUrl`.
  Đồng thời xây dựng `confirmCheckout(String sessionId)` thực hiện kiểm tra thanh toán, đánh dấu đơn hàng thành công, cấp quyền và xóa giỏ hàng.
  
  ```java
  // Thay thế thuộc tính:
  private final StripeService stripeService;
  
  // Điều chỉnh constructor:
  public OrderServiceImpl(OrderRepository orderRepository,
                          OrderItemRepository orderItemRepository,
                          PurchasedLibraryRepository purchasedLibraryRepository,
                          CartItemRepository cartItemRepository,
                          UserRepository userRepository,
                          StripeService stripeService,
                          OrderMapper orderMapper) {
      this.orderRepository = orderRepository;
      this.orderItemRepository = orderItemRepository;
      this.purchasedLibraryRepository = purchasedLibraryRepository;
      this.cartItemRepository = cartItemRepository;
      this.userRepository = userRepository;
      this.stripeService = stripeService;
      this.orderMapper = orderMapper;
  }
  ```
  
  Cập nhật `checkout()`:
  ```java
  @Override
  @Transactional
  public OrderResponse checkout() {
      AuthenticatedUser currentUser = getAuthenticatedUser();
      User buyer = loadBuyer(currentUser);
      List<CartItem> cartItems = cartItemRepository.findByBuyerIdWithMusic(buyer.getId());

      if (cartItems.isEmpty()) {
          throw new BadRequestException("Cart is empty");
      }

      validateDuplicateCartItems(cartItems);
      validateCartOwnership(cartItems, buyer.getId());

      BigDecimal totalAmount = calculateTotalAmount(cartItems);

      Order order = Order.builder()
              .buyer(buyer)
              .orderCode(generateOrderCode())
              .totalAmount(totalAmount)
              .paymentMethod(PaymentMethod.STRIPE)
              .paymentStatus(PaymentStatus.PENDING)
              .orderStatus(OrderStatus.PENDING)
              .build();
      order = orderRepository.save(order);

      // Tạo các order items snapshot
      List<OrderItem> orderItems = cartItems.stream()
              .map(item -> buildOrderItem(order, item))
              .collect(Collectors.toCollection(ArrayList::new));
      orderItemRepository.saveAll(orderItems);
      order.setItems(orderItems);

      // Gọi tạo Stripe session
      String checkoutUrl = stripeService.createCheckoutSession(order, cartItems);

      OrderResponse response = orderMapper.toResponse(order);
      response.setCheckoutUrl(checkoutUrl);
      return response;
  }
  ```
  
  Thêm `confirmCheckout()`:
  ```java
  @Override
  @Transactional
  public OrderResponse confirmCheckout(String sessionId) {
      if (sessionId == null || sessionId.isBlank()) {
          throw new BadRequestException("Session ID is required");
      }

      String orderCode = stripeService.getOrderCodeFromSession(sessionId);
      if (orderCode == null) {
          throw new BadRequestException("Invalid Stripe Checkout Session");
      }

      Order order = orderRepository.findByOrderCode(orderCode)
              .orElseThrow(() -> new NotFoundException("Order not found"));

      // Nếu đơn hàng đã hoàn tất thanh toán trước đó (idempotent check)
      if (order.getOrderStatus() == OrderStatus.PAID && order.getPaymentStatus() == PaymentStatus.SUCCESS) {
          return orderMapper.toResponse(order);
      }

      // Xác minh trạng thái thanh toán từ Stripe
      boolean isPaid = stripeService.isSessionPaid(sessionId);
      if (!isPaid) {
          order.setPaymentStatus(PaymentStatus.FAILED);
          orderRepository.save(order);
          throw new BadRequestException("Stripe payment was not successful");
      }

      // Cập nhật trạng thái Order
      order.setPaymentStatus(PaymentStatus.SUCCESS);
      order.setOrderStatus(OrderStatus.PAID);
      orderRepository.save(order);

      // Nạp danh sách item nếu chưa được nạp
      if (order.getItems() == null || order.getItems().isEmpty()) {
          order.setItems(orderItemRepository.findByOrderIdOrderByCreatedAtAsc(order.getId()));
      }

      // Cấp quyền tải nhạc trong Purchased Library
      List<PurchasedLibrary> purchasedLibraries = order.getItems().stream()
              .map(item -> PurchasedLibrary.builder()
                      .buyer(order.getBuyer())
                      .music(item.getMusic())
                      .build())
              .collect(Collectors.toCollection(ArrayList::new));
      purchasedLibraryRepository.saveAll(purchasedLibraries);

      // Xóa giỏ hàng của buyer tương ứng với những nhạc đã mua
      List<Long> musicIds = order.getItems().stream()
              .map(item -> item.getMusic().getId())
              .toList();
      List<CartItem> cartItems = cartItemRepository.findByBuyerIdWithMusic(order.getBuyer().getId());
      List<CartItem> itemsToRemove = cartItems.stream()
              .filter(item -> musicIds.contains(item.getMusic().getId()))
              .collect(Collectors.toList());
      if (!itemsToRemove.isEmpty()) {
          cartItemRepository.deleteAll(itemsToRemove);
      }

      return orderMapper.toResponse(order);
  }
  ```

- [ ] **Step 4: Chạy test kiểm tra toàn bộ service**
  Run: `cmd.exe /c "gradlew.bat test --tests com.tien.ecommerce_music_be.service.OrderServiceTest"`
  Expected: PASS

- [ ] **Step 5: Commit thay đổi**
  ```bash
  git add workspace/e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/service/OrderService.java workspace/e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/service/OrderServiceImpl.java workspace/e-commerce_music_be/src/test/java/com/tien/ecommerce_music_be/service/OrderServiceTest.java
  git commit -m "feat: implement Stripe checkout flow and payment confirmation in OrderService"
  ```

---

### Task 5: Cập nhật Controller và Security

**Files:**
- Modify: [OrderController.java](file:///c:/Users/USER-PC/OneDrive/Desktop/AI-Music-System/workspace/e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/controller/OrderController.java)

- [ ] **Step 1: Viết test lỗi cho endpoint confirm trong Controller**
  Mở `workspace/e-commerce_music_be/src/test/java/com/tien/ecommerce_music_be/controller/OrderControllerSecurityTest.java`.
  Thêm test kiểm tra quyền truy cập của endpoint `/buyer/orders/checkout/confirm`:
  ```java
      @Test
      void confirmCheckoutShouldBeAccessibleByBuyerAndForbiddenForOthers() throws Exception {
          // Viết mã kiểm thử quyền truy cập endpoint POST /api/v1/buyer/orders/checkout/confirm
      }
  ```
  Run: `cmd.exe /c "gradlew.bat test --tests com.tien.ecommerce_music_be.controller.OrderControllerSecurityTest"`
  Expected: FAIL (Endpoint confirm chưa tồn tại)

- [ ] **Step 2: Thêm endpoint `/buyer/orders/checkout/confirm` vào `OrderController.java`**
  Thêm mapping:
  ```java
      @PostMapping("/buyer/orders/checkout/confirm")
      @PreAuthorize("hasRole('BUYER')")
      @Operation(summary = "Confirm Stripe Checkout Session")
      public ResponseEntity<ApiResponse<OrderResponse>> confirmCheckout(@RequestParam String sessionId) {
          return ResponseEntity.ok(ApiResponse.success(orderService.confirmCheckout(sessionId), "Payment confirmed successfully"));
      }
  ```

- [ ] **Step 3: Chạy test lại**
  Run: `cmd.exe /c "gradlew.bat test --tests com.tien.ecommerce_music_be.controller.OrderControllerSecurityTest"`
  Expected: PASS

- [ ] **Step 4: Chạy toàn bộ test suite của backend để đảm bảo không bị regression**
  Run: `cmd.exe /c "gradlew.bat test"`
  Expected: BUILD SUCCESSFUL

- [ ] **Step 5: Commit thay đổi**
  ```bash
  git add workspace/e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/controller/OrderController.java workspace/e-commerce_music_be/src/test/java/com/tien/ecommerce_music_be/controller/OrderControllerSecurityTest.java
  git commit -m "feat: add confirm checkout endpoint to OrderController with tests"
  ```

---

### Task 6: Cập nhật Frontend Checkout Page

**Files:**
- Modify: [checkout-page-view.tsx](file:///c:/Users/USER-PC/OneDrive/Desktop/AI-Music-System/workspace/e-commerce_music_fe/src/features/checkout/components/checkout-page-view.tsx)

- [ ] **Step 1: Cập nhật `CheckoutPageView` để bỏ form giả lập và tích hợp Stripe redirect**
  Thay thế hàm `handleCompletePurchase` để thực hiện redirect người dùng tới `checkoutUrl` nhận được từ backend:
  ```typescript
    const handleCompletePurchase = async () => {
      if (!totalItems || isProcessing) return
      setIsProcessing(true)
  
      try {
        const response = await api.post<ApiResponse<CheckoutOrderResponse>>("/buyer/orders/checkout")
        const order = response.data?.data
        if (order?.checkoutUrl) {
          toast.success("Redirecting to Stripe...")
          window.location.href = order.checkoutUrl
        } else {
          throw new Error("Checkout URL is missing")
        }
      } catch (error) {
        toast.error(extractErrorMessage(error))
        setIsProcessing(false)
      }
    }
  ```

- [ ] **Step 2: Dọn dẹp UI phần nhập thẻ ảo**
  Xóa bỏ các trường `Field` nhập Cardholder name, Card number, v.v.
  Thay thế khối **"Payment method"** và **"Billing details"** bằng một khối thông tin tích hợp Stripe an toàn chuyên nghiệp.
  Ví dụ:
  ```tsx
  <Card className="border-white/10 bg-white/[0.04] shadow-[0_18px_60px_rgba(0,0,0,0.22)] backdrop-blur-md">
    <CardContent className="space-y-6 p-6">
      <div className="flex items-center gap-3">
        <div className="flex size-10 items-center justify-center rounded-xl bg-fuchsia-500/10 text-fuchsia-400">
          <ShieldCheck className="size-6" />
        </div>
        <div>
          <h2 className="text-xl font-semibold">Secure Payment via Stripe</h2>
          <p className="text-sm text-zinc-400">All transactions are encrypted and processed securely by Stripe.</p>
        </div>
      </div>
      
      <div className="rounded-xl border border-white/5 bg-black/30 p-4 space-y-3">
        <p className="text-xs text-zinc-400">By proceeding, you will be redirected to Stripe Checkout to complete your purchase of licensing rights for selected music tracks.</p>
        <div className="flex flex-wrap gap-4 text-xs text-zinc-400">
          <div className="flex items-center gap-1.5"><Lock className="size-3 text-fuchsia-400" /> SSL Secured</div>
          <div className="flex items-center gap-1.5"><ShieldCheck className="size-3 text-fuchsia-400" /> PCI DSS Compliant</div>
        </div>
      </div>
    </CardContent>
  </Card>
  ```

- [ ] **Step 3: Chạy lint/build frontend để đảm bảo không lỗi cú pháp**
  Run: `npm run build` ở thư mục `workspace/e-commerce_music_fe`
  Expected: BUILD SUCCESSFUL (không có lỗi TypeScript hoặc lint)

- [ ] **Step 4: Commit thay đổi**
  ```bash
  git add workspace/e-commerce_music_fe/src/features/checkout/components/checkout-page-view.tsx
  git commit -m "feat: integrate Stripe redirect on checkout page and clean up fake form"
  ```

---

### Task 7: Cập nhật Frontend Success Verification Page

**Files:**
- Modify: [checkout-success-page-view.tsx](file:///c:/Users/USER-PC/OneDrive/Desktop/AI-Music-System/workspace/e-commerce_music_fe/src/features/checkout/components/checkout-success-page-view.tsx)

- [ ] **Step 1: Cập nhật `CheckoutSuccessPageView` để xử lý xác thực thanh toán qua `session_id`**
  Thêm logic kiểm tra `session_id` từ URL query. Nếu có, thực hiện gọi API `/buyer/orders/checkout/confirm?sessionId=...` để xác minh và hoàn tất đơn hàng, dọn dẹp giỏ hàng local và refresh danh sách nhạc đã mua.
  
  Chỉnh sửa file `checkout-success-page-view.tsx` như sau:
  ```typescript
  import { useState } from "react"
  import { api } from "@/api/api"
  import type { ApiResponse } from "@/types/home"
  import useCartStore from "@/store/cart-store"
  import { Loader2 } from "lucide-react"
  import { toast } from "sonner"
  ```
  
  Thêm các states và `useEffect` cập nhật:
  ```typescript
  export function CheckoutSuccessPageView() {
    const searchParams = useSearchParams()
    const sessionId = searchParams.get("session_id")
    const [verifying, setVerifying] = useState(!!sessionId)
    const [error, setError] = useState<string | null>(null)
    const [confirmedOrderCode, setConfirmedOrderCode] = useState<string | null>(searchParams.get("orderCode"))
    
    const refreshOwnedTrackIds = useMusicPlayerStore((state) => state.refreshOwnedTrackIds)
    const clearCart = useCartStore((state) => state.clearCart)
  
    useEffect(() => {
      if (sessionId) {
        const confirmPayment = async () => {
          try {
            const response = await api.post<ApiResponse<{ orderCode: string }>>(`/buyer/orders/checkout/confirm?sessionId=${sessionId}`)
            const orderData = response.data?.data
            if (orderData?.orderCode) {
              setConfirmedOrderCode(orderData.orderCode)
            }
            clearCart()
            await refreshOwnedTrackIds()
            toast.success("Payment confirmed and library updated!")
          } catch (err: any) {
            console.error("Payment confirmation failed", err)
            setError(err.response?.data?.message || "Failed to verify payment with Stripe. Please contact support.")
            toast.error("Failed to verify payment")
          } finally {
            setVerifying(false)
          }
        }
        void confirmPayment()
      } else {
        void refreshOwnedTrackIds()
      }
    }, [sessionId, clearCart, refreshOwnedTrackIds])
  ```
  
  Cập nhật phần render để hiển thị spinner khi đang tải:
  ```tsx
  if (verifying) {
    return (
      <div className="min-h-screen bg-[#040307] text-white">
        <Navbar />
        <main className="flex min-h-[calc(100vh-80px)] w-full items-center justify-center">
          <div className="text-center space-y-4">
            <Loader2 className="size-12 animate-spin text-fuchsia-500 mx-auto" />
            <h2 className="text-xl font-semibold">Verifying your payment...</h2>
            <p className="text-zinc-400 text-sm">Please wait while we confirm your transaction with Stripe.</p>
          </div>
        </main>
      </div>
    )
  }
  
  if (error) {
    return (
      <div className="min-h-screen bg-[#040307] text-white">
        <Navbar />
        <main className="flex min-h-[calc(100vh-80px)] w-full items-center justify-center px-4">
          <div className="max-w-md w-full border border-red-500/20 bg-red-500/5 rounded-2xl p-6 text-center space-y-4">
            <h2 className="text-2xl font-bold text-red-400">Payment Verification Failed</h2>
            <p className="text-zinc-300 text-sm">{error}</p>
            <Button asChild className="w-full bg-zinc-800 text-white hover:bg-zinc-700">
              <Link href="/checkout">Back to Checkout</Link>
            </Button>
          </div>
        </main>
      </div>
    )
  }
  ```
  Sử dụng `confirmedOrderCode` thay thế cho `orderCode` thô từ URL để đảm bảo an toàn.

- [ ] **Step 2: Chạy lint/build frontend để xác nhận không lỗi biên dịch**
  Run: `npm run build` ở thư mục `workspace/e-commerce_music_fe`
  Expected: BUILD SUCCESSFUL

- [ ] **Step 3: Commit thay đổi**
  ```bash
  git add workspace/e-commerce_music_fe/src/features/checkout/components/checkout-success-page-view.tsx
  git commit -m "feat: verify Stripe payment on success page mount and clear cart"
  ```
