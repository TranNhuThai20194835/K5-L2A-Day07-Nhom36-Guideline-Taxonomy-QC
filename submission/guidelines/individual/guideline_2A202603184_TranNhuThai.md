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

| Lớp | Định nghĩa |
|---|---|
| `car` | Ô tô con: sedan, hatchback, SUV, taxi, xe bán tải loại nhỏ |
| `truck` | Xe tải, xe chở hàng có thùng hàng rời cabin hoặc cỡ lớn |
| `van` | Xe thùng kín cỡ nhỏ, cabin liền thùng |
| `motorcycle` | Xe máy, mô tô, xe tay ga |
| `person` | Người (cả người ngồi trên xe máy) git |
| `ignore_region` | Vùng không cần gán từng vật. |

### Thuộc tính

| Thuộc tính | Giá trị | Ý nghĩa |
|---|---|---|
| `occluded_level` | `0`, `1`, `2`, `3` | 
• 0: thấy rõ toàn bộ hoặc gần như toàn bộ (bị che dưới khoảng 10%).
• 1: bị che ít (khoảng 10% đến 40%), vẫn nhận ra hình dáng.
• 2: bị che nhiều (khoảng 40% đến 80%), chỉ thấy một phần chính.
• 3: gần như không rõ (trên khoảng 80%), chỉ đoán được là vật gì. |
| `truncated` | `no`, `yes` | 
• yes: một phần vật nằm ngoài khung ảnh. Box vẽ đến sát mép ảnh.
• no: vật nằm trọn trong ảnh.
|
| `can_review` | tick / không | Tick khi mình không chắc về lớp, mức che, vị trí box hoặc ca mơ hồ nào khác |

## 3. Quy tắc vẽ box

1.  Ôm sát phần nhìn thấy: cạnh box chạm vào điểm ngoài cùng của vật. Với vật bị che, chỉ vẽ phần nhìn thấy, không đoán phần khuất.
2.  Không gồm bóng đổ, phản chiếu, gương chiếu hậu thò ra xa nếu làm box phình ra rõ rệt. Gương hoặc ăng-ten nhỏ thì bỏ qua.
3.  Người đi xe máy: vẽ hai box riêng, một motorcycle cho xe và một person cho mỗi người ngồi trên xe. Hai box được phép chồng lên nhau.
4.  Xe kéo rơ-moóc hoặc container: coi đầu kéo và thùng là một xe truck nếu nối liền, một box.
5.  Vật ở xa, rất nhỏ: vẫn gán nếu còn nhận ra được. Nếu chỉ còn vài điểm ảnh, không nhận ra là gì, bỏ qua hoặc đưa vào ignore_region.
6.  Nhóm đông, dính vào nhau (đám đông, hàng xe máy đỗ sát): ưu tiên gán từng vật nhìn thấy được. Phần không tách nổi cho vào ignore_region (mục 5).
7.  Xe đỗ, xe trong bãi, xe ở biển quảng cáo: xe đỗ thật vẫn gán. Hình xe trên áp phích, biển quảng cáo hoặc màn hình thì không gán.

## 4. Ca khó

Xe tuk-tuk ở ảnh 4 không biết nên gán là class car, truck hay motorcycle
Người ngồi trên xe máy thì có nên gán riêng người vá xe không hay gán chung là motorcycle
trong ảnh có xuất hiện xe bus thì gán nhãn là car hay van hay không cần gán nhãn
Vật trưng bày là Motorcycle thì có gán nhãn không

## 5. QC và phiên bản

- Mọi thay đổi làm đổi nhãn đều tăng phiên bản guideline.
- Ca mơ hồ: tick `can_review`.
