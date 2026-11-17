# Guideline gán nhãn — v1.1 (sau calibration) — Nhóm 36

- Phiên bản: **v1.1** (bản v1.0 thống nhất + các quy tắc chốt sau calibration)
- Dựa trên: `guideline_v1.0_agreed.md`, `submission/qc/iaa_summary.md`, `submission/qc/calibration_candidates.csv`, `submission/qc/calibration_decisions.csv`
- Loại nhãn: bounding box trên ảnh đường phố · CVAT · COCO 1.0
- Ngày: 17/11/2026
- Taxonomy: không đổi lớp và thuộc tính (vẫn 6 lớp như v1.0) <!-- CHỐT: nếu nhóm thêm lớp (vd. bus, tuk_tuk) thì sửa dòng này và tăng version trong taxonomy.json -->

> **Vì sao có v1.1.** Lần gán đầu theo v1.0 **chưa đạt IAA**: IoU trung bình 0.826 (ngưỡng 0.85),
> cùng lớp 95.3% (ngưỡng 95%), nhưng **cùng `occluded_level` chỉ 48.5%**. Có 376 ca bất thường trên 35/40 ảnh:
> 138 bỏ sót/thừa, 122 lệch thuộc tính, 102 IoU thấp, 11 lệch lớp, 3 cờ `can_review`.
> Các mục đánh dấu **[MỚI v1.1]** là quy tắc thêm vào để xử lý đúng các nguồn lệch này.

---

## 1. Mục tiêu

Nhãn dùng để huấn luyện mô hình phát hiện **người và phương tiện thật đang có mặt trên đường**
(camera giao thông, xe tự lái). Thứ gì mô hình không nên học là người/xe thật (hình in, tượng, ma-nơ-canh, phản chiếu) thì không gán.

## 2. Lớp và thuộc tính

### 2.1 Lớp (mỗi vật thuộc đúng một lớp)

| Lớp | Định nghĩa | Không phải lớp này |
|---|---|---|
| `car` | Ô tô con chở người: sedan, hatchback, SUV, MPV, taxi, bán tải cỡ nhỏ | Xe có thùng hàng rời cabin cỡ lớn → `truck` |
| `truck` | Xe tải, xe chở hàng có thùng hàng **rời cabin**, hoặc xe cỡ lớn chở hàng (kể cả đầu kéo + rơ-moóc nối liền) | Bán tải nhỏ dùng chở người → `car` |
| `van` | Xe thùng kín cỡ nhỏ, **cabin liền thùng**, thân cao hình hộp (xe 7–16 chỗ, xe giao hàng thùng kín nhỏ) | SUV/MPV thân thấp → `car` |
| `motorcycle` | Xe máy, mô tô, xe tay ga (2 bánh có động cơ) | |
| `person` | Người thật, kể cả người ngồi trên xe máy (box riêng) | Tượng, ma-nơ-canh, hình người trên áp phích |
| `ignore_region` | Vùng chứa vật không tách được hoặc quá nhỏ (xem 3.5–3.6) | Vật nhìn rõ, tách được → gán box riêng |

**[MỚI v1.1] Phân biệt car / van / truck** (8/11 ca lệch lớp nằm ở đây: car↔van 4, car↔truck 4).
Hỏi theo thứ tự, dừng ở câu đầu tiên trả lời "có":
1. Có **thùng hàng tách rời cabin** (nhìn thấy khe/vách giữa cabin và thùng) hoặc xe cỡ lớn chở hàng? → `truck`
2. Thân xe **cao, hình hộp, cabin liền khoang chở** (nóc gần như phẳng kéo dài tới đuôi)? → `van`
3. Còn lại → `car`

Vẫn phân vân sau 3 câu → chọn theo câu trả lời gần nhất và tick `can_review`.

**[MỚI v1.1] Phương tiện ngoài 6 lớp** (các ca v1.0 mục 4 còn bỏ ngỏ):

| Loại | Quyết định v1.1 |
|---|---|
| Xe tuk-tuk / xe ba bánh chở khách | **CHỐT:** ☐ `motorcycle` ☐ `car` ☐ thêm lớp mới `tuk_tuk` |
| Xe ba gác, xe máy chế chở hàng | **CHỐT:** ☐ `motorcycle` ☐ `truck` |
| Xe buýt | **CHỐT:** ☐ `truck` (xe cỡ lớn) ☐ `van` ☐ thêm lớp `bus` |
| Xe đạp | **CHỐT:** ☐ không gán ☐ `motorcycle` ☐ thêm lớp `bicycle` |

Nếu thêm lớp mới: sửa `submission/taxonomy.json` (tăng `version`), chạy `python scripts/make_cvat_labels.py`, ghi vào bảng lịch sử ở mục 5.

### 2.2 Thuộc tính

| Thuộc tính | Giá trị | Quy tắc |
|---|---|---|
| `occluded_level` | `0` `1` `2` `3` | Xem 2.3 |
| `truncated` | `no` / `yes` | `yes` khi một phần vật nằm **ngoài khung ảnh**; box vẽ sát mép ảnh. Bị vật khác che **không** tính là truncated |
| `can_review` | tick / không | Tick khi không chắc lớp, mức che, vị trí box hoặc gặp ca chưa có trong guideline |

### 2.3 [MỚI v1.1] Cách xác định `occluded_level`

Lệch thuộc tính là nguồn lệch lớn thứ hai (120 ca lệch `occluded_level`; trong đó **48 ca lệch 2–3 mức**, tức không phải chỉ do mốc ranh giới).

**Ngưỡng (mốc ranh giới thuộc về mức cao hơn):**

| Mức | Phần vật bị **vật khác** che |
|---|---|
| 0 | dưới 10% |
| 1 | từ 10% đến **dưới** 40% |
| 2 | từ 40% đến **dưới** 80% |
| 3 | từ 80% trở lên |

**Cách ước lượng:**
1. Hình dung **toàn bộ vật** nếu không bị che (chỉ để ước lượng, box vẫn vẽ theo phần nhìn thấy, mục 3.1).
2. Ước lượng phần bị **vật khác** che trên tổng thể đó. Phần nằm ngoài khung ảnh **không** tính vào occluded (đã có `truncated`).
3. Mẹo cho `person`: chia người thành 3 phần gần bằng nhau: **đầu–vai / thân / chân**.
   Che 1 phần ≈ mức 1–2, che 2 phần ≈ mức 2, chỉ còn thấy đầu ≈ mức 3.
   <!-- CHỐT: nhóm xác nhận mẹo này hoặc thay bằng cách khác -->
4. Người ngồi sau bàn, sau xe, trong xe (thấy qua kính): kính trong suốt **không** tính là che; khung xe, cửa, bàn thì tính.
   <!-- CHỐT: nhóm xác nhận quy tắc kính -->
5. Phân vân giữa hai mức liền nhau → chọn mức **cao hơn** và tick `can_review`.
   <!-- CHỐT: chọn mức cao hơn hay thấp hơn -->

**Ảnh mẫu cho từng mức** (nhóm điền sau khi xem `work/viz/`):
- Mức 0: img_0__ · Mức 1: img_0__ · Mức 2: img_0__ · Mức 3: img_0__

## 3. Quy tắc vẽ box

1. **Ôm sát phần nhìn thấy**: cạnh box chạm điểm ngoài cùng **nhìn thấy được** của vật. Không đoán phần khuất.
   Vật bị che ở **giữa** (vẫn thấy hai bên) → box vẫn bao trọn từ điểm ngoài cùng bên này đến bên kia; hai box được phép chồng nhau.
2. Không gồm bóng đổ, phản chiếu. Gương chiếu hậu, ăng-ten nhỏ thì bỏ qua nếu làm box phình rõ rệt.
3. **Người đi xe máy**: hai box riêng, một `motorcycle` cho xe và một `person` cho **mỗi** người trên xe.
   **[MỚI v1.1]** Box `person` bao từ đỉnh đầu (gồm mũ bảo hiểm) đến điểm thấp nhất nhìn thấy của người (chân/giày), **không** gồm xe. Box `motorcycle` bao toàn bộ xe (bánh, ghi-đông, thân xe), phần bị người che vẫn nằm trong box nếu thấy được hai bên.
4. Xe kéo rơ-moóc/container nối liền: một box `truck`.
5. **[MỚI v1.1] Vật nhỏ — ngưỡng 20 px**: vật cao **dưới 20 px** không gán box riêng; gộp vào `ignore_region` (đúng quy tắc `min_box_height_px = 20` trong `taxonomy.json`).
   Vật cao **từ 20 px** và còn nhận ra được là người/xe → **phải gán box riêng**.
6. Đám đông, hàng xe đỗ sát: gán từng vật tách được; chỉ phần thật sự không tách nổi mới vào `ignore_region`. Không khoanh `ignore_region` lên vật nhìn rõ.
7. Hình xe/người trên áp phích, biển quảng cáo, màn hình, phản chiếu trong kính: **không gán**. Xe đỗ thật vẫn gán.
8. **[MỚI v1.1] Không bỏ sót** (138 ca bỏ sót/thừa, nhiều nhất là `person` 72 ca, `car` 37, `motorcycle` 28; **105/138 box liên quan cao ≥ 50 px**, tức là vật rõ, không phải vật li ti):
   - Trước khi sang ảnh khác, quét ảnh theo **4 góc + nền phía sau**: người ngồi trong quán, người phía sau xe, xe đỗ ở xa, xe máy dựng bên đường.
   - Người/xe bị cắt ở mép ảnh vẫn gán (kèm `truncated = yes`).
9. **[MỚI v1.1] Xe chở xe** (ví dụ xe tải chở ô tô): mỗi xe thật nhận diện được có **box riêng**: xe chở là một box, mỗi xe được chở là một box.
   <!-- CHỐT: nhóm xác nhận (ca nêu trong guideline_213 mục 4) -->
10. **[MỚI v1.1] Vật nhoè do chuyển động**:
    **CHỐT:** ☐ vẫn gán nếu nhận ra là người/xe, box bao vùng nhoè chính ☐ cho vào `ignore_region` ☐ không gán
11. **[MỚI v1.1] Tượng, ma-nơ-canh, xe trưng bày đứng yên trong cửa hàng**:
    **CHỐT:** tượng/ma-nơ-canh ☐ không gán · xe trưng bày ☐ gán ☐ không gán

## 4. Ca khó (đã chốt ở calibration)

| Ảnh | Tình huống | Quyết định v1.1 | Quy tắc |
|---|---|---|---|
| img_005, img_011, img_016, img_025 | Cùng một xe: car vs van | __ | 2.1 car/van/truck |
| img_008, img_015, img_028, img_037 | Cùng một xe: car vs truck | __ | 2.1 car/van/truck |
| img_008 | Một vật bị gán person ở người này, motorcycle ở người kia (do box người và xe chồng nhau) | Người và xe là 2 box riêng (kiểm tra lại cả hai box có đủ không) | 3.3 |
| img_008 | Cùng một xe: motorcycle vs truck | __ | 2.1 phương tiện ngoài 6 lớp (xe ba gác?) |
| img_004 (và các ảnh có tuk-tuk) | Xe tuk-tuk | __ | 2.1 phương tiện ngoài 6 lớp |
| img_002 | Xe tải chở ô tô | __ | 3.9 |
| img_005, img_037 | Ca được tick `can_review` | __ | |
| (điền thêm) | | | |

## 5. QC và phiên bản

- Mọi thay đổi làm đổi nhãn đều tăng phiên bản guideline.
- Ca mơ hồ: tick `can_review`, đưa ra họp; quyết định ghi vào `submission/qc/calibration_decisions.csv`.
- Ngưỡng IAA: IoU TB ≥ 0.85, đồng thuận lớp ≥ 95%.

| Phiên bản | Ngày | Thay đổi | Lý do (ca / số liệu) |
|---|---|---|---|
| v0 | — | Bản giảng viên | — |
| v1.0 | 16/11 | Định nghĩa lớp, ngưỡng occluded 10/40/80%, 7 quy tắc vẽ box | Hợp nhất guideline cá nhân 182, 184, 207, 213 |
| v1.1 | 17/11 | Cây phân biệt car/van/truck; quy ước mốc ranh giới và cách ước lượng occluded; ngưỡng 20 px cho ignore_region; quy tắc chống bỏ sót; box person/motorcycle; xe chở xe; vật nhoè; tượng/xe trưng bày; phương tiện ngoài 6 lớp | Calibration: IoU 0.826, occluded khớp 48.5%, 138 bỏ sót/thừa, 11 lệch lớp (35/40 ảnh) |
