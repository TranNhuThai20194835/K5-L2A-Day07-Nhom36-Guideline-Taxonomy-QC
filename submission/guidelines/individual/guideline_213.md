# Guideline gán nhãn — <phiên bản> — <MSHV - Họ tên | Nhóm>

> Copy file này (hoặc `guideline_v0_starter.md`) rồi viết. Một guideline đủ gồm **5 phần**. Câu nào của v0
> làm hai người đọc ra hai cách khác nhau thì phải viết lại cho một cách hiểu duy nhất.

- Phiên bản: 0
- Ngày: 11/16/2026
- Người viết / duyệt: Lê Trung Tá

## 1. Mục tiêu
Mô hình nào dùng nhãn này, để làm gì, sai ở đâu thì nguy hiểm.

## 2. Lớp và thuộc tính
### Lớp
| Lớp | Định nghĩa (một câu) | Ví dụ | Phản ví dụ / dễ nhầm với |
|---|---|---|---|
| car | phương tiện giao thông | img_012.jpg | img_038.jpg |

Vật **không thuộc lớp nào** thì làm gì (không gán? vùng ignore? lớp mới?).


Vật không thuộc lớp nào thì không gán


### Thuộc tính
Mỗi thuộc tính: tên, **các mức cố định**, nghĩa từng mức, **ngưỡng cụ thể** (ví dụ che bao nhiêu % thì mức nào).


Với mỗi thuộc tính sẽ có các mức bị che khuất là (0,1,2,3), 0 là dưới 10%, 1 là từ 10-40%, 2 là từ 40-80%, 3 là trên 80%.


## 3. Quy tắc vẽ box
Box ôm sát phần nhìn thấy; vật bị che; vật bị cắt mép ảnh. Nhưng với phản chiếu / hình in / tượng / đồ chơi; xe chủ (ego); vật quá nhỏ; đám đông; một vật có nhiều phần (người và xe) thì sẽ không vẽ bbox.

## 4. Ca khó
| Ảnh | Tình huống | Quyết định | Lý do |
|---|---|---|---|
| img_027.jpg | đồ vật có hình dánh giống ô tô nhưng không phải ô tô | quyết định là không gán nhãn | lý do là vì thuộc tính của hình ảnh không phải ô tô. |
| img_023.jpg | có sự xuất hiện của xe xích lô | không gán | không có nhãn cho loại xe này |
| img_022.jpg | xe máy 3 bánh có thùng ghế ngồi bên cạnh | gán cả xe và thùng | là loại xe đã được chỉ định nhãn nhưng trường hợp đặc biệt |
| img_019.jpg | xe ô tô tự chế | có gán nhãn | là ô tô có thể di chuyển |
| img_017.jpg | bức tượng | không gán | không có nhãn cho vật thể này |
| img_016.jpg | xe ba gác | gán | Là xe máy tự chế |
| img_010.jpg | xe bán hàng rong | có gán |  |
| img_002.jpg | xe tải chở ô tô |  | Lý do |
| img_038.jpg | của hàng đồ chơi ô tô | không gán | không phải phương tiện di chuyển |
Câu hỏi nào chưa chốt: gắn `can_review` và ghi lên trưởng nhóm. Không tự đoán.

## 5. QC và phiên bản
Ngưỡng QC áp dụng, ai quyết ca mơ hồ, lịch sử thay đổi:

| Phiên bản | Thay đổi | Lý do (ca nào) |
|---|---|---|
| | | |
