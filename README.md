# Bài tập 4: Khai báo GitHub Secrets và cấu hình Đăng nhập Container Registry

- **Môn học:** IT215 - Cloud Infrastructure & DevOps Fundamentals
- **Học viên:** Trần Đức Anh
- **Đường dẫn nộp bài:** `homework/session_11/ex4/`

---

## 1. Mục tiêu

- Tạo Personal Access Token (classic) trên GitHub với quyền thao tác gói Container (`write:packages`).
- Cấu hình bí mật bảo mật cấp kho lưu trữ (Repository Secret) với tên biến `REGISTRY_TOKEN` trên GitHub.
- Cấu hình bước tự động xác thực đăng nhập vào GitHub Container Registry (GHCR) trong workflow GitHub Actions bằng `docker/login-action@v3`.

---

## 2. Cấu trúc thư mục dự án

```text
ss11/ex4/
├── .github/
│   └── workflows/
│       └── ci.yml
├── gradle/
│   └── wrapper/
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── src/
│   ├── main/java/com/example/ex11/Ex11Application.java
│   └── test/java/com/example/ex11/Ex11ApplicationTests.java
├── build.gradle
├── gradlew
├── gradlew.bat
├── settings.gradle
├── .gitignore
└── README.md
```

---

## 3. Nội dung tệp kịch bản CI Workflow (`.github/workflows/ci.yml`)

```yaml
name: Java Spring Boot CI (Gradle)

on:
  push:
    branches: ["main"]

jobs:
  test:
    name: Run Unit Tests and Build JAR
    runs-on: ubuntu-latest

    steps:
      - name: Checkout mã nguồn
        uses: actions/checkout@v5

      - name: Thiết lập môi trường Java JDK 17
        uses: actions/setup-java@v5
        with:
          java-version: "17"
          distribution: "temurin"
          cache: "gradle"

      - name: Cấp quyền cho Gradle Wrapper
        run: chmod +x gradlew

      - name: Thực thi JUnit Test với Gradle
        run: ./gradlew test

      - name: Đóng gói tệp JAR với Gradle
        run: ./gradlew bootJar

      - name: Lưu trữ thành phẩm JAR Artifact
        uses: actions/upload-artifact@v4
        with:
          name: app-jar
          path: build/libs/*.jar

      - name: Đăng nhập vào GitHub Container Registry
        uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.REGISTRY_TOKEN }}
```

---

