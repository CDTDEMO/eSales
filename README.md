# eSales

Ứng dụng Android được xây dựng bằng Kotlin và Jetpack Compose.

> **Trạng thái:** Project đang ở giai đoạn khởi tạo. Phần khung (build config, theme Material 3,
> cấu trúc module, bộ test) đã sẵn sàng; `MainActivity` hiện chỉ hiển thị màn hình mẫu
> `Greeting` từ template. Các tính năng nghiệp vụ sẽ được bổ sung dần.

## Tech stack

| Thành phần | Phiên bản |
|---|---|
| Kotlin | 2.2.10 |
| Android Gradle Plugin | 9.2.1 |
| Gradle | 9.4.1 |
| Compose BOM | 2026.02.01 |
| Material 3 | theo BOM |

UI dựng hoàn toàn bằng Jetpack Compose, không dùng XML layout. Theme `ESalesTheme` hỗ trợ
dynamic color (lấy màu từ hình nền hệ thống trên Android 12+) và tự động đổi sáng/tối theo
cài đặt của máy. Màn hình chạy ở chế độ edge-to-edge.

## Cấu hình build

| Thuộc tính | Giá trị |
|---|---|
| `applicationId` | `com.tinhcd.esales` |
| `namespace` | `com.tinhcd.esales` |
| `minSdk` | 26 (Android 8.0 Oreo) |
| `targetSdk` / `compileSdk` | 36 |
| Java source/target | 11 |
| Gradle daemon toolchain | JDK 21 |

## Yêu cầu môi trường

- **Android Studio** bản hỗ trợ AGP 9.2 trở lên
- **JDK 21** — Gradle daemon dùng toolchain 21, tự tải qua Foojay nếu máy chưa có
- **Android SDK API 36**
- Thiết bị thật hoặc emulator chạy **Android 8.0 (API 26)** trở lên

## Bắt đầu

```bash
git clone https://github.com/CDTDEMO/eSales.git
cd eSales
```

Mở thư mục project bằng Android Studio và chờ Gradle sync. Android Studio sẽ tự sinh file
`local.properties` trỏ tới Android SDK trên máy bạn — file này nằm trong `.gitignore` nên
không được commit. Nếu build bằng dòng lệnh mà chưa có file đó, tạo thủ công:

```properties
sdk.dir=/duong/dan/toi/Android/Sdk
```

## Các lệnh thường dùng

Trên Windows thay `./gradlew` bằng `gradlew.bat`.

```bash
./gradlew assembleDebug          # Build APK debug
./gradlew installDebug           # Build và cài lên thiết bị đang kết nối
./gradlew test                   # Chạy unit test (JVM)
./gradlew connectedAndroidTest   # Chạy instrumented test (cần thiết bị/emulator)
./gradlew clean                  # Xoá thư mục build
```

APK sau khi build nằm ở `app/build/outputs/apk/`.

## Cấu trúc thư mục

```
eSales/
├── app/
│   └── src/
│       ├── main/
│       │   ├── java/com/tinhcd/esales/
│       │   │   ├── MainActivity.kt        # Activity duy nhất, entry point của app
│       │   │   └── ui/theme/              # Theme Compose
│       │   │       ├── Color.kt           # Bảng màu
│       │   │       ├── Theme.kt           # ESalesTheme + dynamic color
│       │   │       └── Type.kt            # Typography
│       │   ├── keepRules/rules.keep       # Keep rules cho R8
│       │   ├── res/                       # Icon, string, theme XML
│       │   └── AndroidManifest.xml
│       ├── test/                          # Unit test chạy trên JVM
│       └── androidTest/                   # Instrumented test (Espresso + Compose UI test)
├── gradle/
│   └── libs.versions.toml                 # Version catalog - khai báo dependency tập trung
├── build.gradle.kts                       # Build script cấp root
└── settings.gradle.kts                    # Khai báo module
```

Project hiện chỉ có một module là `:app`.

## Ghi chú

**Quản lý dependency.** Mọi thư viện và phiên bản được khai báo tập trung trong
`gradle/libs.versions.toml`. Khi thêm dependency mới, khai báo trong file này trước rồi tham
chiếu qua `libs.` trong `app/build.gradle.kts`, đừng viết trực tiếp toạ độ Maven vào build script.

**Configuration cache.** Đã bật trong `gradle.properties`, giúp Gradle bỏ qua pha configuration
khi build script không đổi. Nếu thêm plugin hoặc logic build không tương thích, Gradle sẽ báo
lỗi ở pha này.

**R8 cho bản release.** Hiện đang tắt tối ưu (`optimization { enable = false }` trong
`app/build.gradle.kts`). Trước khi phát hành thật, nên bật lại và bổ sung keep rules cần thiết
vào `app/src/main/keepRules/rules.keep`.
