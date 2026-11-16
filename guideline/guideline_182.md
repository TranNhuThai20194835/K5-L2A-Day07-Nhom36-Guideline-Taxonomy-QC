# Guideline gán nhãn — v0 (bản khởi đầu)

> **Đây là bản do giảng viên viết nhanh. Nó CHƯA đủ dùng.**
> Đừng sửa file này. Hãy copy sang `submission/guidelines/individual/` rồi sửa bản của bạn
> (xem `GUIDE.md`, Pha 1).

- Phiên bản: v0
- Loại nhãn: bounding box (hộp chữ nhật) trên ảnh đường phố
- Công cụ: CVAT, xuất COCO 1.0

---

## 1. Mục tiêu

Nhãn dùng để huấn luyện mô hình phát hiện **người và phương tiện** trên ảnh đường phố
(ví dụ cho camera giao thông hoặc xe tự lái).

## 2. Lớp và thuộc tính

### Lớp (mỗi vật thuộc đúng một lớp)

| Lớp | Định nghĩa |Ví dụ|Phản ví dụ / dễ nhầm với|
|---|---|---|---|
| `car` | Xe 4 chỗ chở người |xe con,xe bán tải, xe tuk tuk|xe van nhỏ, xe tải nhỏ.|
| `truck` | Xe chở hàng,xe chở vật liệu| xe bồn, xe đầu kéo, xe tải con.| Xe bus, xe khách chở người|
| `van` | Xe thùng kín cỡ nhỏ, hình hộp chữ nhật | xe 16 chỗ, xe van chở hàng| xe bus,  car|
| `motorcycle` | Xe 2 bánh, có động cơ.|xe máy, xe phân khối lớn| xe đạp|
| `person` | Người. |người đang đứng, người đi bộ,người đi xe máy||
| `ignore_region` | Vùng không cần gán từng vật. |bầu trời.đường,cây||

### Thuộc tính

| Thuộc tính | Giá trị | Ý nghĩa |
|---|---|---|
| `occluded_level` | `0`, `1`, `2`, `3` | Mức bị che: 0 là thấy rõ, 1 là bị che ít, 2 là bị che nhiều, 3 là không rõ. |
| `truncated` | `no`, `yes` | Vật bị cắt ở mép ảnh. |
| `can_review` | tick / không | Tick khi bạn không chắc, để người khác xem lại. |

## 3. Quy tắc vẽ box

1. Gán box cho mọi người và phương tiện trong ảnh.
2. Box ôm sát vật.
3. Chọn đúng lớp và thuộc tính.
4.Gán box theo instance,mỗi vật 1 box
5.Vật ở xa bị nhoè, vật ngoài ảnh quá 80% hình dạng không cần gán nhãn
6. ca khó thì không cần gán
7.người ngồi trên xe máy là person, motorbike có người ngồi gán cả gán box motorbike cho người với xe

## 4. Ca khó

- xe bán tải chế , xe đạp, xe bus.
-
-

## 5. QC và phiên bản

- Mọi thay đổi làm đổi nhãn đều tăng phiên bản guideline.
- Ca mơ hồ: tick `can_review`.
