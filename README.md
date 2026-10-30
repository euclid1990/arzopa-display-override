# Arzopa Z3FC + MacBook Pro M5: độ phân giải nét mà không cần BetterDisplay

Ngày làm: 2026-10-30. Máy: MacBook Pro 14" M5, macOS 27. Màn: Arzopa Z3FC 16.1", tấm nền 2560×1440, 180Hz, nối USB-C.

## Vấn đề

- macOS mặc định chỉ có một chế độ nét (HiDPI) cho màn này là 1280×720, quá ít không gian.
- Các mức 1344×756, 1600×900 có sẵn trong System Settings là bản thường (LoDPI), chữ mờ.
- BetterDisplay tạo được chế độ nét, nhưng không muốn mua bản Pro và không muốn chạy app nền.

## Cách giải quyết

macOS cho phép khai báo thêm chế độ HiDPI bằng một file cấu hình đặt theo mã nhà sản xuất và mã sản phẩm của màn. File này do BetterDisplay (bản miễn phí) tạo một lần, sau đó gỡ app vẫn dùng được.

Đường dẫn file:

```
/Library/Displays/Contents/Resources/Overrides/DisplayVendorID-1ee4/DisplayProductID-b161
```

Mã màn: vendor `0x1ee4` (7908), product `0xb161` (45409). Xem bằng `system_profiler SPDisplaysDataType` hoặc `ioreg -l | grep -i arzopa`.

Bản sao file nằm cùng thư mục với tài liệu này: `DisplayProductID-b161`.

## Các bước đã làm

1. Cài BetterDisplay (bản miễn phí).
2. Settings → Displays → ARZOPA → *System Configuration and Flexible Scaling*.
3. Bật *Edit the system configuration of this display model*.
4. Bật *Custom scaled resolutions* → *Add Custom Resolution*, thêm 4 dòng: 1344×756, 1440×810, 1536×864, 1600×900. Nhập đúng kích thước muốn thấy trên màn, không nhân đôi.
5. Bấm **Apply**. Khi app hỏi quyền, chọn *Use Admin Prompt Instead* và nhập mật khẩu. Không chọn *Open System Settings*, vì cách đó cài thêm tiến trình nền của app.
6. Bấm **Activate** (hoặc khởi động lại nếu app yêu cầu).
7. Thoát app, kiểm tra System Settings → Displays → ARZOPA → *Show all resolutions* vẫn còn 4 chế độ mới. Mỗi chế độ có đủ 60–180Hz.
8. Kéo BetterDisplay vào Thùng rác. **Không** vào app tắt mục *Edit the system configuration* rồi Apply, vì thao tác đó xóa luôn file.
9. Khởi động lại máy, kiểm tra lại.

## Cài lại file khi bị mất (ví dụ sau khi cập nhật macOS)

```bash
sudo mkdir -p /Library/Displays/Contents/Resources/Overrides/DisplayVendorID-1ee4
sudo cp ~/Documents/arzopa-display-override/DisplayProductID-b161 /Library/Displays/Contents/Resources/Overrides/DisplayVendorID-1ee4/
```

Khởi động lại máy. Nếu muốn bỏ hẳn:

```bash
sudo rm -rf /Library/Displays/Contents/Resources/Overrides/DisplayVendorID-1ee4
```

## Chọn độ phân giải

System Settings → Displays → ARZOPA → bật *Show all resolutions*. Mỗi kích thước có hai dòng, chọn dòng **không có** chữ "(low resolution)". Sau khi rút cắm lại màn, macOS hay tự chọn nhầm dòng mờ, chỉ cần chọn lại.

Gợi ý cho màn 16.1" để code: 1440×810 hoặc 1536×864 cân bằng giữa nét và không gian. 1600×900 rộng nhất nhưng chữ hơi mềm.

Ở mục Refresh Rate chọn 120 hoặc 180Hz.

## Lỗi không chỉnh được độ sáng

Triệu chứng: nút cứng trên màn không chỉnh được độ sáng, mục Brightness trong menu màn bị xám, màn rất sáng.

Nguyên nhân: menu màn đặt **HDR = Auto**. Màn coi tín hiệu từ Mac là HDR, đẩy đèn nền tối đa và khóa chỉnh tay. Không liên quan đến file cấu hình hay cài đặt HDR của macOS.

Cách sửa: vào menu màn, đặt **HDR = Off**.

## Những thứ đã thử và không được

- Tự viết file cấu hình bằng tay (chỉ khai bản HiDPI, cờ `1`, có ghi tên màn): macOS trên chip M bỏ qua, không thêm được chế độ nào. File của BetterDisplay khác ở chỗ: mỗi kích thước khai cả hai bản (HiDPI 16 byte với cờ `9`, và bản thường 8 byte), không ghi tên màn.
- Chọn chế độ nét từ menu Display Mode của BetterDisplay: chỉ có tác dụng lúc app chạy, đổi độ phân giải là mất. Phải dùng mục *System Configuration* mới lưu được.

## Kiểm tra nhanh bằng lệnh

Xem màn đang ở chế độ gì:

```bash
system_profiler SPDisplaysDataType | grep -A3 ARZOPA
```

Dòng `UI Looks like: 1440 x 810` với `Resolution: 2880 x 1620` là bản nét (gấp đôi). Nếu hai số bằng nhau là bản mờ.
