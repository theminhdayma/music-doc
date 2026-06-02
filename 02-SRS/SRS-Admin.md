# SRS - Admin Module

## Purpose

Defines the system administration functionalities for the marketplace.

Admins do not participate directly in the buying and selling process. Their primary responsibilities are monitoring, moderation, and platform data management.

## Actors

* **ADMIN**: views the dashboard, manages users, manages genres, manages songs, and reviews orders.

## Functional Requirements

1. Admin can view a dashboard with overall system statistics.
2. Admin can view the user list with pagination and filtering by keyword, role, and status.
3. Admin can view user details and update account status.
4. Admin can view the genre list and perform CRUD operations based on permissions.
5. Admin can view the song list, view song details, hide songs, and perform soft deletion.
6. Admin can view the order list and order details for auditing and verification purposes.
7. The admin dashboard returns aggregated data only and does not expose entities directly.

## Non-Functional Requirements

* Only users with the **ADMIN** role can access administrative APIs.
* All responses must use DTOs and be wrapped in `ApiResponse` or `PageResponse`.
* Admin screens should prioritize table-based layouts with drawer/sheet components for efficient operations.

## Related

* [[API-Admin]]
* [[API-User]]
* [[API-Genre]]
* [[API-Music]]
* [[API-Order]]
* [[System-Architecture]]
