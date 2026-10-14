# Bài 2: Biên dịch đóng gói tệp .jar với Gradle và lưu trữ Artifacts

## Mục tiêu
- Tự động hóa quá trình biên dịch và đóng gói ứng dụng Spring Boot thành tệp Fat JAR thực thi (`./gradlew bootJar`).
- Sử dụng action `actions/upload-artifact@v4` để đẩy tệp JAR lên bộ lưu trữ tạm thời của GitHub Actions.
- Cấu hình thời gian lưu trữ (retention-days) hợp lý và tải artifact về kiểm tra.

---

## 1. Cấu trúc Dự án
```text
ss11/b2/
├── .github/
│   └── workflows/
│       └── package.yml         # Workflow đóng gói & upload artifact
├── gradle/wrapper/
├── src/
│   ├── main/java/com/example/demo/DemoApplication.java
│   └── test/java/com/example/demo/DemoApplicationTests.java
├── build.gradle                # Cấu hình archiveFileName = 'app.jar'
├── settings.gradle
├── gradlew
└── README.md
```

---

## 2. Cấu hình GitHub Actions
Trong `.github/workflows/package.yml`:
- Bước 1: Checkout mã nguồn qua `actions/checkout@v4`.
- Bước 2: Thiết lập OpenJDK 17 với `actions/setup-java@v4`.
- Bước 3: Phân quyền `chmod +x gradlew` và chạy lệnh `./gradlew bootJar`.
- Bước 4: Upload artifact:
  ```yaml
  - name: Upload Spring Boot JAR Artifact
    uses: actions/upload-artifact@v4
    with:
      name: spring-boot-app-jar
      path: build/libs/*.jar
      retention-days: 5
  ```

---

## 3. Đầu ra thực thi giả lập
```bash
$ ./gradlew bootJar

> Task :compileJava
> Task :processResources
> Task :classes
> Task :bootJar

BUILD SUCCESSFUL in 5s
3 actionable tasks: 3 executed

$ ls -lh build/libs/
-rw-r--r-- 1 runner docker 18M Oct 11 04:00 app.jar
```

Artifact xuất hiện trên tab GitHub Actions với tên `spring-boot-app-jar` (dung lượng ~18MB).
