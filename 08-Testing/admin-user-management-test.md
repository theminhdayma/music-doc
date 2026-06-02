# Admin User Management Test Checklist

## Service and controller coverage

- [x] Admin list users returns paged summary results.
- [x] Admin list users filters by keyword across email and full name.
- [x] Admin list users filters by role and account status.
- [x] Admin list users excludes soft-deleted users.
- [x] Admin read user detail returns full safe profile data.
- [x] Admin update status changes another user's status.
- [x] Admin update status rejects self-status changes.
- [x] Admin update status rejects missing status payload.
- [x] Security test: admin routes blocked for non-ADMIN.

## Executed test suites

- [x] `UserRepositorySpecificationTest`
- [x] `AdminUserServiceTest`
- [x] `AdminUserControllerSecurityTest`
- [x] Full backend suite: `./gradlew test` (blocked by unrelated existing failures in `CartServiceTest` and `UserProfileServiceTest`)
- [x] Build verification: `./gradlew build`

# Related

- [[API-User]]
- [[Table-User]]