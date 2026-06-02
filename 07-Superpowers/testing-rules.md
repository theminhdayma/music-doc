# Testing Rules

## Nguyên Tắc Chung

- TDD bắt buộc với feature liên quan đến license và payment
- Với feature khác: viết test song song hoặc ngay sau khi code
- Không merge nếu test fail
- Coverage tối thiểu: 80% với service layer

## Workflow TDD: RED → GREEN → REFACTOR

```
RED    → Viết test thất bại trước (test mô tả đúng behavior mong muốn)
GREEN  → Viết code tối thiểu để test pass (không cần đẹp)
REFACTOR → Dọn dẹp code, test vẫn phải pass
```

## Backend — Spring Boot Test

### Unit Test (Service Layer)

```java
@ExtendWith(MockitoExtension.class)
class LicenseServiceTest {

    @Mock
    private LicenseRepository licenseRepository;

    @InjectMocks
    private LicenseService licenseService;

    @Test
    void shouldRevokeLicenseWhenOrderCancelled() {
        // Arrange
        License license = License.builder()
            .id(1L)
            .status(LicenseStatus.ACTIVE)
            .build();
        when(licenseRepository.findByOrderId(1L)).thenReturn(Optional.of(license));

        // Act
        licenseService.revokeByOrderId(1L);

        // Assert
        assertEquals(LicenseStatus.REVOKED, license.getStatus());
        verify(licenseRepository).save(license);
    }
}
```

Đặt file: `src/test/java/.../service/LicenseServiceTest.java`

### Integration Test (Controller + DB)

```java
@SpringBootTest
@AutoConfigureMockMvc
@Transactional
class TrackControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void shouldReturn30sPreviewOnly() throws Exception {
        mockMvc.perform(get("/api/v1/public/musics/{slug}/preview", 1L))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.durationSeconds").value(lessThanOrEqualTo(30)));
    }
}
```

Dùng MySQL test riêng hoặc H2 in-memory cho integration test.

### Cách chạy test

```bash
# Chạy tất cả test
./gradlew test

# Chạy tất cả lint
./gradlew lint


# Chạy một class cụ thể
./gradlew test --tests "com.example.music.service.LicenseServiceTest"

# Xem report
open build/reports/tests/test/index.html
```

## Frontend — Next.js

- Unit test component dùng Jest + React Testing Library
- Chạy: `npm run test`

## Các Luồng BẮT BUỘC Có Test

1. Buyer mua license → order COMPLETED → download được
2. Buyer cancel order → license REVOKED → không download được nữa
3. Preview track → chỉ trả về 30 giây đầu
4. Upload track (Seller) → cần auth, đúng role
5. API yêu cầu auth → trả 401 nếu không có token

## Quy Tắc Viết Test

- Tên test mô tả behavior: `shouldRevokeLicenseWhenOrderCancelled`
- Cấu trúc: Arrange → Act → Assert
- Mỗi test chỉ test một việc
- Mock external service (storage, payment) trong unit test
- Không để test phụ thuộc vào nhau