# Giải thích bản Shadowrocket đã sửa

File sử dụng: `Shadowrocket-fixed.conf`. Đây là cấu hình đầy đủ, không phải module. File gốc đính kèm không bị sửa.

## Các thay đổi và lý do

| Thay đổi | Vì sao và tác dụng |
|---|---|
| Chỉ bật `RevenueCatVip` cho phản hồi RevenueCat | Ba mục VSCO, Locket và RevenueCat chung cùng bắt các URL tương tự. Chỉ giữ một bộ xử lý để tránh sửa cùng dữ liệu nhiều lần hoặc trả dữ liệu sai ứng dụng. |
| Tắt mục VSCO riêng bằng dấu `#` | Script trả một phản hồi cố định ngay từ bước request, trong khi mẫu URL áp dụng cho mọi ứng dụng RevenueCat. Cú pháp thiếu `pattern=` cũng đã được sửa trong dòng lưu lại. |
| Tắt hai script Locket riêng | Khi kiểm tra, hai URL nguồn trả lỗi 404. Không thể khôi phục tính năng Firebase chỉ bằng việc sửa cú pháp cấu hình. |
| Giữ script RevenueCat chung | Nguồn hiện đọc được và có ánh xạ `Locket` sang `Gold`, cùng nhiều ứng dụng khác. Đây chỉ là dữ liệu phản hồi được sửa trên thiết bị; chưa xác nhận hiệu quả với phiên bản ứng dụng đang dùng. |
| Thay script `deleteHeader` bằng Header Rewrite có sẵn | Nguồn script không tải được. Xóa header ETag giúp yêu cầu không chỉ nhận phản hồi dựa trên bộ nhớ đệm. Giữ cả hai cách viết hoa/thường như file gốc. |
| Sửa mẫu URL RevenueCat | Thêm tên miền dự phòng, cho phép query string và dấu `/` cuối, giới hạn ở receipts hoặc bản ghi subscriber, không bắt các đường dẫn con như offerings. |
| Sửa YouTube `engine={{{script}}}` thành `engine=webview` | Giá trị cũ là biến mẫu chưa được điền. Đồng thời sửa dấu nháy của JSON trong `argument`. Hai script vẫn tắt vì nguồn không tải được. |
| Tắt chuyển hướng `ctier` cũ | Dòng này sửa URL phát video nhưng chưa xác định được đầu ra mong muốn, có thể làm sai tham số phát. Giữ nguyên dòng gốc dưới dạng chú thích để có thể đối chiếu. |
| Tắt các script khác chưa tải được | SoundCloud, Alight Motion, PicsArt purchases, Wink, Truecaller, Kinemaster, CamScanner, BeautyPlus và Meitu được giữ dưới dạng chú thích, có lý do ở ngay phía trên. Lỗi truy cập qua công cụ kiểm tra không đủ chứng minh các file này đã bị xóa vĩnh viễn. |
| Sửa khoảng trắng mẫu URL CamScanner | Bỏ khoảng trắng ngay sau `pattern=` trong dòng được giữ lại. |
| Đặt cron Sub-Store trong dấu nháy | Giữ lịch chạy mỗi ngày lúc 00:00 theo thời gian thiết bị, làm rõ giá trị chứa khoảng trắng. Chức năng đồng bộ còn phụ thuộc thiết lập trong Sub-Store. |
| Tắt `update-url` cũ | URL dẫn đến trang kho GitHub, không phải nội dung file cấu hình thô. Chưa có URL raw chính xác nên không tự đoán đường dẫn thay thế. |
| Dọn MITM hostname | Bỏ phần tử rỗng, bỏ `%APPEND%` khỏi cấu hình đầy đủ, chỉ giữ các hostname phục vụ mục đang bật. Các miền định vị Apple/Autonavi không có rule hoặc script tương ứng đã được bỏ. |
| Giữ nguyên chứng chỉ | Hai trường CA được đối chiếu với file gốc và không thay đổi. Chứng chỉ vẫn phải được cài và tin cậy trên thiết bị mới có thể giải mã HTTPS. |

## Các phần của file làm gì?

| Phần | Vai trò |
|---|---|
| `[General]` | Giữ các thiết lập bỏ qua lưu lượng hệ thống, miền nội bộ và các dải mạng riêng từ bản gốc. |
| `[Rule]` | Chặn UDP của các miền YouTube được liệt kê, nhằm tạo điều kiện để ứng dụng dùng kết nối có thể xử lý bằng HTTP/TLS. Không bảo đảm mọi kết nối sẽ chuyển giao thức thành công. |
| `[URL Rewrite]` | Các mục còn bật chặn một số URL quảng cáo hoặc thống kê quảng cáo YouTube. Không bảo đảm loại bỏ mọi quảng cáo. |
| `[Header Rewrite]` | Xóa header bộ nhớ đệm của RevenueCat và một endpoint Spotify để hỗ trợ xử lý phản hồi. |
| `[Map Local]` | Trả nội dung rỗng HTTP 200 cho yêu cầu initplayback có tham số quảng cáo khớp mẫu. |
| `[Script]` | Chạy JavaScript tại các request/response khớp URL hoặc theo lịch. Tên mục có chữ Premium/Gold không chứng minh tài khoản đã được cấp thuê bao. |
| `[MITM]` | Cho Shadowrocket giải mã HTTPS trên những hostname được liệt kê để đọc và sửa nội dung. |

## Những mục đang bật

- **Sub-Store Core, Simple, Sync:** xử lý truy cập Sub-Store và lịch đồng bộ. Không tự thêm máy chủ proxy.
- **Spotify JSON/Proto:** sửa request hoặc dữ liệu phản hồi Spotify theo script bên thứ ba.
- **PicsArtStorages:** xử lý phản hồi dung lượng; script purchases riêng đang tắt.
- **Lightroom, iTunes, Snow:** giữ các script sửa dữ liệu phản hồi từ bản gốc vì nguồn đọc được. Chưa chạy thử nội dung JavaScript hoặc xác nhận hiệu quả.
- **RevenueCatVip:** sửa dữ liệu thuê bao/entitlement của các ứng dụng được script nhận diện, bao gồm ánh xạ Locket/Gold.

**Không còn bật:** script YouTube cho PiP/phát nền/phụ đề, Locket Firebase Remote Config và các script được đánh dấu `DISABLED`. Một số rule chặn quảng cáo YouTube cơ bản vẫn bật. Bản này không tạo giao dịch App Store hay bảo đảm dịch vụ phía máy chủ chấp nhận quyền trả phí.

## Kiểm tra đã thực hiện

- Đối chiếu chứng chỉ với bản gốc.
- Kiểm tra tên script đang bật không trùng, có loại và URL nguồn HTTPS.
- Biên dịch các regex script đang bật bằng .NET; đây không phải trình biên dịch Shadowrocket.
- Kiểm tra mẫu RevenueCat với URL hợp lệ, query string, tên miền giả và đường dẫn con không nên bắt.
- Kiểm tra không còn biến mẫu chưa điền hoặc hostname rỗng.
- Kiểm tra khả năng đọc URL nguồn bằng công cụ web; chưa kiểm thử tải nguồn từ mạng trên iPhone.

Chưa nhập file vào Shadowrocket, chưa chạy JavaScript trên ứng dụng và chưa xác nhận tính năng trả phí. Do đó đây là bản sửa cấu hình và giảm xung đột, không phải bản đã xác nhận mở khóa toàn bộ ứng dụng.

## Cách dùng và kiểm tra trên máy

1. Lưu bản cấu hình đang dùng để có thể quay lại.
2. Chuyển file `.conf` sang iPhone và nhập vào Shadowrocket, chọn sử dụng cấu hình đó.
3. Kiểm tra mục HTTPS Decryption và chứng chỉ. Nếu chứng chỉ trong file đã được tin cậy trên máy thì dùng lại; nếu chưa, cài và bật tin cậy trong Cài đặt iOS → Cài đặt chung → Giới thiệu → Cài đặt tin cậy chứng chỉ.
4. Kiểm tra danh sách Script URL để biết các nguồn đang bật có tải thành công trên máy không.
5. Kết nối lại Shadowrocket, đóng rồi mở ứng dụng cần kiểm tra. Xem log để xác định request có khớp script và MITM hay không.
6. Khi có nguồn thay thế hợp lệ cho một mục bị tắt, cập nhật URL, khôi phục hostname cần thiết rồi mới bỏ dấu `#` ở dòng script.

## Nguồn đối chiếu

- [Mã nguồn RevenueCat dùng trong file](https://raw.githubusercontent.com/duyvinh09/Module_IOS/refs/heads/main/js/revenuecat.js)
- [Mã nguồn VSCO dùng trong file gốc](https://raw.githubusercontent.com/duyvinh09/Module_IOS/refs/heads/main/js/VSCO.js)
- [Tài liệu cấu hình cộng đồng Shadowrocket](https://github.com/LOWERTOP/Shadowrocket/wiki) — phần header rewrite, script, HTTPS decryption và module.
- [Bản phát hành Sub-Store](https://github.com/sub-store-org/Sub-Store/releases/latest)
