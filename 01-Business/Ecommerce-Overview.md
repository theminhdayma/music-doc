# Ecommerce Music Marketplace Overview

This project is a music license marketplace where sellers upload tracks and buyers purchase usage rights. It is not a streaming platform.

Authentication is required for protected actions, but the public signup flow is intentionally limited to Buyer and Seller roles.

Music uploads are stored in Cloudinary and referenced through secure URLs in the database. Public browsing only exposes published tracks.

Genres are a core catalog dimension used to organize tracks and improve discovery. The genre module must be secure, simple, and consistent with the platform's soft-delete model.

# Related

- [[Business-Rules]]
- [[User-Roles]]
- [[System-Architecture]]
