# Backend Architecture

## Layering

- Controller: HTTP request handling, Swagger annotations, response wrapping.
- Service: business rules, validation checks, soft delete, role-aware workflows.
- Repository: persistence queries.
- Entity: JPA mapping only.
- Mapper: MapStruct conversions between entities and DTOs.

## Music Module Placement

- Entity: `Music`
- DTOs: create, update, detail, list, and search responses
- Service: upload, slug generation, visibility rules, soft delete, owner checks
- Storage abstraction: Cloudinary integration for thumbnail and audio files
- Controller: seller, public, and admin endpoints

## Authentication Module Placement

- Entity: `User`
- DTOs: login and register requests, auth and user responses
- Service: registration, login, password hashing, JWT issuance
- Security: JWT filter, user details service, security configuration

## Genre Module Placement

- Entity: `Genre`
- DTOs: genre request and response objects
- Service: genre lifecycle and uniqueness checks
- Controller: public and admin endpoints


## Library Module Placement

- Entity: `PurchasedLibrary`
- DTOs: purchased library list and signed download URL responses
- Service: ownership lookup, library pagination, signed URL generation
- Controller: buyer-only endpoints under `/api/v1/buyer/library/**`

## Admin Dashboard Placement

- DTOs: admin dashboard stats response
- Service: repository aggregation for counts and revenue
- Controller: admin-only endpoint under `/api/v1/admin/dashboard`

## Security

- Public auth endpoints are permitted without authentication.
- Public genre reads are permitted without authentication.
- Public music reads are permitted without authentication.
- Seller music endpoints require SELLER.
- Admin music endpoints require ADMIN.
- Admin genre management is restricted at the HTTP layer to ADMIN.
- Buyer library endpoints require BUYER.
- Admin dashboard endpoint requires ADMIN.
- Method security is also enabled for fine-grained authorization.
# Related

## Cart Module Placement

- Entity: `CartItem` stored in `cart_item` table with `buyer_id`, `music_id`, timestamps and optional soft-delete metadata.
- DTOs: add/remove and list responses for buyer cart; no quantity in MVP.
- Service: `CartService` enforces buyer-only operations, prevents adding own music, validates `MusicStatus.PUBLISHED`, and enforces unique `(buyer_id,music_id)` constraint.
- Controller: buyer-only endpoints under `/api/v1/buyer` protected with `hasRole('BUYER')`.
- Transactions: cart mutating operations (add/remove/clear) use `@Transactional` to ensure consistency.
- Integration: references `User` and `Music` entities; should call `musicRepository.findByIdAndDeletedAtIsNull(...)` when validating items.
- Observability & scaling: small per-user payload; consider caching or a fast KV store if load increases.

## Order + Payment Module Placement

- Entities: `Order`, `OrderItem`, `PurchasedLibrary`
- Enums: `OrderStatus`, `PaymentMethod`, `PaymentStatus`
- Services:
	- `OrderServiceImpl`: checkout transaction, buyer/admin order queries
	- `PaymentServiceImpl`: sandbox payment simulation
	- `LibraryServiceImpl`: purchased library list + signed download URL generation
- Repositories:
	- `OrderRepository`, `OrderItemRepository`, `PurchasedLibraryRepository`
- Controllers:
	- `OrderController` under `/api/v1/buyer/orders/**` and `/api/v1/admin/orders/**`
	- `LibraryController` under `/api/v1/buyer/library/**`
- Transaction boundary:
	- Checkout uses `@Transactional` and executes create order -> process payment -> persist snapshots -> grant ownership -> clear cart atomically.
- DTO/Mapper pattern:
	- Responses are mapped by `OrderMapper` and `PurchasedLibraryMapper` and wrapped with `ApiResponse`/`PageResponse`.
- Ownership strategy:
	- Download authorization is checked against `purchased_library` for predictable lookup cost and clear rule ownership.

# Related

- [[System-Architecture]]
- [[Database-Design]]
- [[ADR-Authentication-JWT]]
- [[SRS-Cart]]
- [[API-Cart]]
- [[Table-Cart]]
- [[SRS-Order]]
- [[SRS-Payment]]
- [[API-Order]]
- [[API-Payment]]
- [[Table-Order]]
- [[Table-Payment]]
- [[ADR-Order-Payment-Ownership]]
