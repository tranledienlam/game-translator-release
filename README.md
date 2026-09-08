# Game Translator — Giai đoạn 1

Mục tiêu giai đoạn này: xác nhận có thể (1) tìm được cửa sổ RetroArch bằng code,
và (2) hiển thị overlay đè lên trên cửa sổ đó — kể cả khi RetroArch đang chạy
ở chế độ "windowed fullscreen".

## Yêu cầu trước khi chạy

1. Máy Windows (vì dùng pywin32).
2. Python 3.9+ đã cài.
3. Trong RetroBat, mở file cấu hình RetroArch (thường ở
   `RetroBat/emulators/retroarch/retroarch.cfg`), đảm bảo có dòng:
   ```
   video_fullscreen = "true"
   video_windowed_fullscreen = "true"
   ```
   Nếu không thấy dòng nào, tự thêm vào. Đây là bước bắt buộc — nếu để
   `video_windowed_fullscreen = "false"` (exclusive fullscreen thật), overlay
   sẽ KHÔNG hiển thị đè lên được.

## Cài đặt

```bash
cd game-translator
pip install -r requirements.txt
```

## Cách test

### Test 1: chỉ overlay (không cần RetroBat)

```bash
python ui/overlay.py
```

Sẽ thấy 1 dòng chữ vàng nổi trên nền trong suốt, phủ kín màn hình chính.
Bấm Alt+F4 hoặc đóng bằng Task Manager để tắt (vì overlay không có nút close,
đây là hành vi cố ý vì nó sẽ chạy nền sau này).

### Test 2: chỉ window finder (không cần overlay)

```bash
python core/window_finder.py
```

Mở RetroBat, vào 1 game bất kỳ, rồi chạy lệnh trên trong 1 cửa sổ terminal khác.
Kết quả mong đợi: in ra title, vị trí, kích thước cửa sổ RetroArch tìm được.

Nếu báo "Không tìm thấy cửa sổ RetroArch" — kiểm tra lại RetroArch đã bật
windowed fullscreen theo hướng dẫn ở trên chưa (exclusive fullscreen sẽ khiến
window title không enum được theo cách bình thường trong một số trường hợp).

### Test 3: full pipeline giai đoạn 1 (bản mới — chạy tool trước, không cần mở game trước)

```bash
python main.py
```

1. Chạy `python main.py` TRƯỚC KHI mở game — khung dịch nhỏ sẽ hiện lên ngay
   với chữ "Đang chờ bạn mở game trong RetroBat...".
2. **Kéo khung dịch** đến vị trí bạn muốn (bấm giữ chuột trái ở bất kỳ đâu
   trong khung rồi kéo). **Resize** bằng cách kéo góc dưới phải khung.
3. Mở RetroBat, vào chơi 1 game bất kỳ — không cần thao tác gì thêm, trong
   vòng 1 giây khung dịch sẽ tự cập nhật thành "Đã kết nối RetroArch / Game: ...".
4. Đổi sang game khác trong RetroBat — khung tự cập nhật tên game mới.
5. Đóng game — khung quay lại trạng thái "Đang chờ...", **chương trình KHÔNG
   tự thoát**, bạn có thể mở game khác mà không cần chạy lại tool.
6. Muốn dừng hẳn — bấm nút **"×"** ở góc trên phải khung dịch.

Lưu ý quan trọng: khung dịch giờ là 1 cửa sổ có thể thao tác được (không
click-through), nên nó sẽ **chặn click chuột** trong vùng nó đang che. Đặt nó
ở khu vực không có nút bấm quan trọng trong game (ví dụ mép dưới màn hình,
nơi thường hiển thị dialogue/phụ đề).

## Nếu overlay không hiện lên trên RetroArch

Khả năng cao nhất: RetroArch vẫn đang chạy exclusive fullscreen thật, không
phải windowed fullscreen. Kiểm tra lại:

- File `retroarch.cfg` có đúng `video_windowed_fullscreen = "true"` chưa.
- Thử trong RetroArch UI: Settings → Video → "Windowed Fullscreen Mode" → ON.
- Một số driver video cụ thể (`video_driver`) có thể ảnh hưởng — nếu vẫn không
  được, thử đổi `video_driver` sang `"gl"` hoặc `"d3d11"` trong cfg rồi test lại.

## Sau khi giai đoạn 1 chạy ổn

Bước tiếp theo (giai đoạn 2) sẽ thêm:
- Chọn vùng OCR bằng chuột (overlay riêng để vẽ hình chữ nhật)
- Lưu vùng đã chọn theo tên game vào `config/games/<ten_game>.json`
- Tự động load lại vùng cũ khi mở lại game đó

Chưa cần làm gì thêm cho tới khi giai đoạn 1 này chạy ổn định trên máy bạn.
