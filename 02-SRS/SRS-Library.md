# SRS - Library Module

## Purpose

Defines the buyer's purchased library module.

The library allows buyers to view purchased tracks and download files using signed URLs.

## Actors

* **BUYER**: views the purchased library and downloads purchased tracks.

## Functional Requirements

1. Buyers can view their own purchased tracks.
2. The library list must support pagination.
3. Buyers can download a file only if the track exists in their purchased library.
4. Download URLs must be signed URLs with an expiration time.
5. The system must never expose raw audio URLs directly to buyers.
6. The frontend player can use library data to verify ownership and enable full playback.

## Non-Functional Requirements

* Only users with the **BUYER** role can access library endpoints.
* Ownership verification must be based on `purchased_library` for efficient and clear lookup.
* Responses must use DTOs and be wrapped in `ApiResponse` or `PageResponse`.

## Related

* [[API-Library]]
* [[API-Order]]
* [[SRS-Order]]
* [[Table-Order]]
* [[System-Architecture]]
