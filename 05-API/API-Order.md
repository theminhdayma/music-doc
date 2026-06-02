---

title: Order API
module: order
version: 1
----------

# Order API

Base path: `/api/v1`

## Buyer Endpoints

* `POST /api/v1/buyer/orders/checkout`

  * Description: Checks out the current buyer's cart and creates an order, order items, and purchased library records.
  * Roles: BUYER
  * Success: `200 OK` with `ApiResponse<OrderResponse>`.
  * Errors: `400`, `401`, `403`, `404`, `409`.

* `GET /api/v1/buyer/orders`

  * Description: Retrieves the current buyer's order list with pagination.
  * Roles: BUYER
  * Success: `200 OK` with `ApiResponse<PageResponse<OrderSummaryResponse>>`.

* `GET /api/v1/buyer/orders/{orderId}`

  * Description: Retrieves details of an order belonging to the current buyer.
  * Roles: BUYER
  * Success: `200 OK` with `ApiResponse<OrderDetailResponse>`.
  * Errors: `404` if the order does not exist or does not belong to the buyer.

## Admin Endpoints

* `GET /api/v1/admin/orders`

  * Description: Retrieves a paginated list of all orders.
  * Roles: ADMIN
  * Success: `200 OK` with `ApiResponse<PageResponse<OrderSummaryResponse>>`.

* `GET /api/v1/admin/orders/{id}`

  * Description: Retrieves details of any order for auditing and administrative purposes.
  * Roles: ADMIN
  * Success: `200 OK` with `ApiResponse<OrderDetailResponse>`.

## DTOs

* `OrderResponse`: id, orderCode, totalAmount, orderStatus, paymentMethod, paymentStatus, createdAt, itemsCount.
* `OrderSummaryResponse`: id, orderCode, buyerId, buyerName, totalAmount, orderStatus, paymentStatus, createdAt.
* `OrderDetailResponse`: id, orderCode, buyerId, buyerName, totalAmount, orderStatus, paymentMethod, paymentStatus, createdAt, items.
* `OrderItemResponse`: id, musicId, musicTitleAtPurchase, artistNameAtPurchase, thumbnailUrlAtPurchase, priceAtPurchase.

## Response Convention

* List endpoints: `ResponseEntity<ApiResponse<PageResponse<T>>>`
* Detail and action endpoints: `ResponseEntity<ApiResponse<T>>`
* Entities must never be returned directly.

# Related

* [[SRS-Order]]
* [[Table-Order]]
* [[SRS-Payment]]
