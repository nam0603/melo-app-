# Melo Android — Đặc tả bản thử nghiệm và yêu cầu triển khai Codex

Ngày chốt: 22/10/2026 · Ngôn ngữ giao diện: Tiếng Việt · Tên Melo là tên làm việc, chưa kiểm tra nhãn hiệu.

**Trạng thái tệp:** Đây là tài liệu bàn giao để bắt đầu lập trình. Chưa có mã nguồn ứng dụng, APK hoặc kết quả kiểm thử thiết bị trong lần bàn giao này.

## 1. Sản phẩm cần xây

Ứng dụng Android chia sẻ ảnh riêng tư kèm một đoạn nhạc 10–15 giây giữa những người đã kết bạn. Cảm giác sử dụng: nhanh, thân mật, ít thao tác. Chụp một khoảnh khắc, chọn giai điệu và gửi cho một người bạn.

Mốc dùng thật đầu tiên: Máy A chụp ảnh, chọn đoạn nhạc và gửi → máy B nhận, mở ảnh và nghe đúng đoạn đã chọn → widget máy B hiển thị ảnh mới.

Chỉ làm Android trong giai đoạn này. Dùng Flutter cho ứng dụng và Kotlin cho widget Android. Không triển khai iOS, không làm website thay thế ứng dụng di động.

## 2. Phạm vi bản đầu

| Tính năng | Yêu cầu |
|---|---|
| Tài khoản | Đăng ký/đăng nhập bằng email và mật khẩu; xác minh email, quên mật khẩu; tên hiển thị và avatar |
| Kết bạn | Mã mời có hạn, lời mời và xác nhận hai chiều; hủy kết bạn; không đồng bộ danh bạ |
| Camera | Chụp ảnh, đổi camera, chụp lại; xử lý trường hợp từ chối quyền |
| Thêm nhạc | Chọn một trong 3–5 bản nhạc thử có nguồn rõ ràng, chọn đoạn 10–15 giây và nghe thử |
| Gửi | Chọn một hoặc nhiều bạn đã kết bạn; chú thích tối đa 100 ký tự; có tiến trình và thử lại |
| Hộp ảnh | Ảnh nhận/gửi, người gửi, thời gian, chú thích, tên nhạc, nút phát/dừng |
| Cảm xúc | Thả/bỏ một cảm xúc trên một ảnh; không cần chat |
| Widget | Ảnh mới nhất được phép hiển thị, tên người gửi và tên bài; chạm mở đúng bài trong app |
| Quyền riêng tư | Tắt hiển thị ảnh trên widget, chặn người dùng, báo cáo, xóa bài và xóa tài khoản |

Chưa cần video, feed công khai, AI, quảng cáo, gói trả phí, chat đầy đủ, kho nhạc thương mại hoặc đăng nhập bằng số điện thoại.

## 3. Trải nghiệm và thiết kế

- Mặc định giao diện tối, nền than đậm, chữ sáng, điểm nhấn tím lavender; bo tròn vừa phải. Không sao chép logo, tên hoặc giao diện Locket.
- Ba tab chính: Khoảnh khắc · Chụp · Bạn bè. Cài đặt qua avatar.
- Camera là thao tác trung tâm. Nút chụp lớn, ảnh xem trước ưu tiên tỷ lệ vuông.
- Sau khi chụp: xem trước → thêm nhạc → chọn đoạn → chọn người nhận → gửi.
- Ảnh không có nhạc vẫn gửi được. Không tự phát khi lướt; người dùng bấm để nghe.
- Chỉ một trình phát hoạt động tại một thời điểm. Tạm dừng khi rời trang, khóa màn hình hoặc app vào nền trong bản đầu.
- Mỗi trang phải có trạng thái đang tải, trống và lỗi. Nút gửi bị vô hiệu khi đang gửi.
- Không ghi “Đã gửi” nếu mới lưu vào máy hoặc đang chờ mạng. Bản demo phải ghi rõ “Bản thử trên máy”.
- Nút tối thiểu 48dp, hỗ trợ tăng cỡ chữ; kiểm tra màn hình nhỏ và điều hướng Back của Android.

## 4. Âm nhạc

- Dùng âm thanh tự sáng tác/tổng hợp hoặc bản nhạc có bằng chứng cho phép sử dụng trong ứng dụng. Ghi nguồn và phạm vi sử dụng trong MUSIC_SOURCES.md.
- Không tải âm thanh từ Spotify, YouTube hoặc Apple Music để phát lại trong app. Không giả định liên kết bài hát đồng nghĩa có quyền phân phối âm thanh.
- Track gồm id, title, creator, durationMs, asset/objectPath và thông tin nguồn. Một bài đăng tham chiếu trackId, startMs, durationMs; không nhân bản cả tệp nhạc cho mỗi bài.
- Kiểm tra 10000 <= durationMs <= 15000 và 0 <= startMs <= trackDurationMs - durationMs. Chỉ cho chọn track đủ dài.
- Preview và bài nhận phải phát cùng khoảng đã chọn; dừng đúng điểm cuối, xử lý đổi bài khi đang phát.
- Bản đầu không có tải nhạc cá nhân, không tự phát từ widget, không phát nền.

## 5. Công nghệ và tổ chức mã nguồn

- Flutter stable, Dart; xác nhận phiên bản thực tế khi khởi tạo, ghi lại và commit lockfile.
- Android minSdk 24 làm điểm bắt đầu theo image_picker hiện tại; xác minh lại tương thích khi chốt dependencies. compileSdk/targetSdk theo Flutter và yêu cầu phát hành tại thời điểm build.
- Firebase Authentication, Firestore, Cloud Storage, Cloud Functions và FCM cho bản hai thiết bị.
- image_picker cho thao tác chụp bằng camera hệ thống ở mốc đầu; có thể chuyển sang camera preview trong app sau khi luồng cốt lõi ổn định.
- Trình phát dùng package Flutter được duy trì, hỗ trợ seek/stop và xử lý vòng đời app; lựa chọn sau khi kiểm tra tài liệu hiện hành.
- Widget native Kotlin với AppWidgetProvider/RemoteViews; giao tiếp Flutter qua platform channel hoặc package đã kiểm tra.
- Tách UI, repositories và services; LocalRepository và FirebaseRepository tuân theo cùng hợp đồng. Không khởi tạo Firebase ở chế độ demo nếu thiếu cấu hình.
- Thư mục đề xuất: lib/features/{auth,camera,music,moments,friends,settings}, lib/data, lib/services, android, functions, test, integration_test, docs.
- Firebase Emulator cho kiểm thử tự động dữ liệu và phân quyền. Không đưa service-account key hoặc khóa ký phát hành vào Git.

## 6. Widget Android

- Widget kích thước gọn, người dùng chủ động thêm trên màn hình chính; hướng dẫn thêm thủ công nếu launcher không hỗ trợ ghim.
- Lưu ảnh thu nhỏ đã xác thực trong vùng riêng của app, giải mã ở kích thước phù hợp để tránh bitmap quá lớn.
- Khi có bài mới: đồng bộ quyền truy cập và nội dung trước, sau đó thay widget. Thông báo chỉ mang định danh, không mang URL ảnh công khai.
- Chạm widget mở đúng momentId; phải kiểm tra lại quyền trước khi hiển thị. Khi không còn quyền, hiện thông báo phù hợp và xóa bản cache đó.
- Đăng xuất, xóa tài khoản hoặc bật chế độ ẩn widget phải xóa cache và đưa widget về trạng thái riêng tư ngay trên thiết bị đang hoạt động.
- Có trạng thái “Chưa có ảnh” và “Mở Melo để cập nhật”. Không cam kết cập nhật tức thì khi Android hạn chế nền, mất mạng hoặc người dùng force-stop.
- Phải kiểm tra trường hợp app đang mở, vào nền, bị hệ điều hành kết thúc, force-stop, thiết bị khởi động lại và chế độ tiết kiệm pin. Ghi nhận khác biệt, không đánh dấu tất cả đã đạt nếu chưa thử.

## 7. Dữ liệu, quyền và chống gửi trùng

Các thực thể tối thiểu: users, friendRequests, friendships, moments, reactions, reports, tracks; token thiết bị chỉ nằm trong vùng dữ liệu riêng của chủ tài khoản và backend.

- moments chứa senderId, recipientIds, imagePath, trackId (nullable), startMs, durationMs, caption, createdAt và trạng thái xóa. createdAt do server xác lập.
- Quyền đọc ảnh và metadata kiểm tra phía server; không chỉ ẩn nút trên giao diện. Người không được nhận phải bị từ chối dù biết object path hoặc momentId.
- Không lưu/chia sẻ Firebase download URL tồn tại lâu làm cơ chế bảo vệ ảnh riêng tư. Tải qua đường có kiểm tra xác thực/quyền; bản cache chỉ ở thiết bị có quyền.
- Khi tạo bài, backend kiểm tra quan hệ bạn bè được chấp nhận, trạng thái chặn hai chiều, danh sách người nhận và track/đoạn nhạc. Client không tự đổi senderId hoặc tăng quyền.
- Thiết kế ban đầu: hủy kết bạn/chặn sẽ thu hồi quyền truy cập các bài giữa hai người khi thiết bị đồng bộ. Ảnh đã được người nhận lưu/chụp màn hình không thể thu hồi; thiết bị ngoại tuyến cũng không thể nhận lệnh xóa ngay.
- Dùng requestId ổn định cho mỗi lần gửi, tái sử dụng khi thử lại. Tạo bài và gửi thông báo phải idempotent, không tạo bài trùng do bấm lại hoặc trigger chạy lại.
- Nén ảnh, bỏ metadata vị trí trước khi tải. Đặt mục tiêu cạnh dài tối đa 1600px; giới hạn tệp sau xử lý 5MB và kiểm tra loại nội dung thực tế phía tin cậy.
- Quota thử nghiệm đề xuất: 30 bài/người/ngày, tối đa 10 người nhận/bài; áp dụng phía server, có thể chỉnh theo kết quả thử.
- Xóa bài làm mất quyền truy cập ngay trên backend; xóa tệp/các bản cache theo luồng dọn dẹp có thử lại. Có job dọn ảnh tải lên nhưng không tạo bài thành công.
- Xóa tài khoản cần xác thực lại, vô hiệu quyền truy cập và dọn ảnh/token/dữ liệu liên quan qua job có trạng thái. Không coi thao tác xóa Auth user đơn lẻ là đã xóa hết dữ liệu.
- Quản trị không có nút xem toàn bộ ảnh riêng tư. Truy cập nội dung báo cáo theo quyền riêng và có nhật ký thao tác.

## 8. Các mốc triển khai và điều kiện hoàn thành

### Mốc A — APK demo cục bộ

Làm camera, kho 3–5 track thử, chọn đoạn, nghe thử, lưu/xóa bài trên máy, lịch sử cục bộ và widget lấy ảnh cục bộ. Dữ liệu còn sau khi mở lại app. Không có bạn bè/người nhận giả làm người dùng thật. Demo không phát sinh chi phí backend.

Hoàn thành khi build được APK debug, cài và thử được trên một Android; có video hoặc nhật ký thao tác. Nếu chưa có thiết bị thì ghi rõ phần chưa kiểm tra. APK debug dùng thử, không phải bản phát hành Play Store.

### Mốc B — Hai điện thoại dùng thật

Thêm Firebase, tài khoản, kết bạn hai chiều, gửi/nhận ảnh nhạc thật, push, widget cập nhật từ dữ liệu nhận. Mọi rule và API phải có kiểm thử truy cập trái phép. Thiếu tài khoản/cấu hình thì hoàn thành mọi phần độc lập, ghi đúng bước còn thiếu và yêu cầu người sở hữu cung cấp cấu hình; không giả kết quả.

Hoàn thành khi A và B dùng hai tài khoản trên hai máy; C không được nhận phải không đọc được cả ảnh và metadata. Gửi thất bại có thể thử lại, không tạo trùng.

### Mốc C — Thử kín với 10–20 người

Hoàn thiện chặn/báo cáo/xóa tài khoản, quyền riêng tư widget, theo dõi lỗi và quản trị tối thiểu. Chỉ số thử: số người thực sự gửi ảnh, tỷ lệ thêm nhạc, tỷ lệ gửi thành công và người quay lại. Không ghi ảnh/âm thanh/nội dung chú thích vào log phân tích.

## 9. Danh sách kiểm thử bắt buộc

- Camera: chụp, hủy, đổi camera, từ chối quyền; phục hồi kết quả nếu Android kết thúc Activity lúc mở camera.
- Nhạc: chọn đầu/giữa/cuối track, đoạn không hợp lệ, đổi track, phát/dừng, rời trang và khóa máy; preview giống đoạn bài nhận.
- Dữ liệu: mở lại app, xóa bài đang phát hoặc đang nằm trên widget, mất mạng giữa upload và tạo bài, thử lại không trùng.
- Phân quyền: người lạ, lời mời chưa chấp nhận, người đã bị chặn, token hết hạn, người biết object path và senderId giả.
- Widget: thêm/xóa nhiều instance, chạm bài, đăng xuất, ẩn ảnh, ảnh lỗi, xoay/thay kích thước widget nếu launcher cho phép.
- Push: cấp/từ chối quyền thông báo trên Android phù hợp; foreground/background; token thay đổi; force-stop được ghi nhận đúng giới hạn.
- Báo cáo kiểm thử ghi lệnh, kết quả, thiết bị/API và mục chưa chạy. Test quan trọng: quyền Storage/Firestore, validation clip và idempotency.

## 10. Bàn giao kỹ thuật cần có

Mã nguồn đầy đủ; README chạy trên Windows; AGENTS.md hướng dẫn dự án; lockfile; hướng dẫn cấu hình Firebase; rules và test; APK debug sau khi build thành công; CHECKLIST.md và TEST_REPORT.md ghi đúng kết quả.

Lệnh dự kiến sau khi cài Flutter và Android SDK:

```sh
flutter doctor -v
flutter pub get
flutter analyze
flutter test
flutter build apk --debug
```

APK dự kiến: build/app/outputs/flutter-apk/app-debug.apk. Chỉ thông báo có APK khi tệp thật đã được tạo và xác minh. Build thành công không thay cho kiểm thử trên điện thoại.

## 11. Nội dung gửi cho Codex

Sao chép đoạn bên dưới vào task đầu tiên, đồng thời đặt tệp này tại gốc repository:

> Hãy triển khai dự án theo Melo_Android_Codex_Brief.md. Chỉ làm Android trước. Bắt đầu Mốc A bằng Flutter và widget native Kotlin, tạo mã nguồn thực tế và APK debug nếu môi trường hỗ trợ. Tự kiểm tra công cụ, cài dependencies được phép và dùng phiên bản tương thích đã xác minh; không chỉ trả về kế hoạch. Tạo bộ nhạc demo nguyên bản hoặc có bằng chứng nguồn, không dùng bài thương mại. Demo chạy được khi chưa có Firebase và phải ghi rõ dữ liệu chỉ lưu trên máy. Viết test có ý nghĩa cho validation đoạn nhạc và lưu/xóa dữ liệu; chạy analyze, test, build. Nếu thiếu SDK/mạng, hoàn thành các phần độc lập và báo chính xác lệnh bị chặn; không khẳng định đã build/test. Khi Mốc A đạt, tiếp tục các phần Mốc B độc lập với tài khoản dịch vụ; chỉ hỏi những thông tin thật sự thiếu. Không tự tạo dịch vụ có phí hay công bố ứng dụng. Bàn giao source, hướng dẫn Windows, APK nếu có và TEST_REPORT.md với phần đã kiểm tra/chưa kiểm tra. Ưu tiên một luồng hoàn chỉnh trước khi thêm tính năng.

## 12. Tài liệu kỹ thuật tham chiếu

- Flutter Android build: https://docs.flutter.dev/deployment/android
- image_picker, hỗ trợ Android và xử lý lost data: https://pub.dev/packages/image_picker
- Android widgets: https://developer.android.com/develop/ui/views/appwidgets/overview
- Firebase Flutter nhận thông báo: https://firebase.google.com/docs/cloud-messaging/flutter/receive-messages

Các lựa chọn trong tệp là phạm vi đề xuất cho dự án; cần xác minh cấu hình và phiên bản package khi thực sự triển khai.
