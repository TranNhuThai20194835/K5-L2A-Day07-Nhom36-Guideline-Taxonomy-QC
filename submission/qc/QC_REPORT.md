# Báo cáo QC — Nhóm 36

> Số liệu lấy từ `submission/qc/iaa_summary.md`, `calibration_candidates.csv`, `calibration_decisions.csv` và `qc_report_auto.md`.
> Mọi tỉ lệ đều kèm mẫu số.
> <!-- TODO: điền mục 3.2 và 4.2 từ qc_report_auto.md sau khi kiểm xong phiếu qc_review.csv, rồi xoá dòng này -->

## 1. Lô

| Mục | Giá trị |
|---|---|
| Tên lô | day07-street-40 |
| Số ảnh | 40 |
| Thành viên (mã - MSHV) | 182 - Phạm Đức Thọ (⟪MSHV⟫), 184 - Trần Như Thái (2A202603184), 207 - Nguyễn Thái Dương (2A202603207), 213 - Lê Trung Tả (2A202603213) |
| Số lần gán mỗi ảnh (K) | 2 (chia bằng `split_assign.py --members 182,184,207,213`, seed 42; mỗi người 20 ảnh) |
| Phiên bản guideline lúc gán | v1.0 |
| Phiên bản guideline hiện tại | v1.1 (`guideline_v1.1_calibrated.md`) |

## 2. Mẫu QC

- Cỡ mẫu: **40 ảnh, 358 đối tượng** (35 ảnh ca khó + 5 ảnh ngẫu nhiên, seed 42), chạy `qc_sample.py --n 20 --seed 42` trên nhãn cuối `labels_calibrated.json`.
- Cách chọn: script lấy **mọi ảnh ca khó** (35 ảnh bị cờ ở Pha 4) cộng tối đa 20 ảnh ngẫu nhiên; lô chỉ còn 5 ảnh không bị cờ nên lấy cả 5.
  Kết quả là **kiểm 100% lô**, đúng quy tắc chuẩn "lô < 500 ảnh kiểm 100%", không phải nới lỏng.
- Người kiểm: chia 4 người, **mỗi người chỉ kiểm ảnh mình không gán** (đối chiếu `submission/qc/assignment.csv`), kiểm theo guideline v1.1; ngày 2026-11-17.

| Người kiểm | Ảnh | Số đối tượng |
|---|---|---|
| 182 - Phạm Đức Thọ | 001, 003, 007, 013, 017, 023, 026, 029, 034, 038, 040 | 89 |
| 184 - Trần Như Thái | 004, 009, 014, 016, 019, 021, 024, 027, 031, 036 | 89 |
| 207 - Nguyễn Thái Dương | 002, 006, 008, 010, 012, 015, 020, 022, 032, 033 | 90 |
| 213 - Lê Trung Tả | 005, 011, 018, 025, 028, 030, 035, 037, 039 | 90 |

## 3. Số đo

### 3.1 IAA (trước calibration, taxonomy v1.0)

| Chỉ số | Giá trị | Mẫu số | Ngưỡng | Đạt? |
|---|---|---|---|---|
| IoU trung bình (cặp box ghép được) | 0.826 | 233 cặp box | ≥ 0.85 | **Không** |
| Đồng thuận lớp | 95.3% (222/233) | 233 cặp box | ≥ 95% | Có (sát ngưỡng) |
| Box không ghép được (bỏ sót/thừa) | 138 (96 + 42) | 604 box tham gia so sánh (2 × 233 + 138) | — | — |
| Đồng thuận `occluded_level` | 48.5% (113/233) | 233 cặp box | — | — |
| Đồng thuận `truncated` | 92.3% (215/233) | 233 cặp box | — | — |
| Box bỏ qua vì nằm trong `ignore_region` | 71 | — | — | — |

Theo cặp người: chỉ cặp 184|213 đạt (IoU 0.880, lớp 100%, 58 cặp box); 5 cặp còn lại chưa đạt, thấp nhất 182|184 (IoU 0.774, lớp 87.5%, 24 cặp box).

**Kết luận IAA: chưa đạt** (IoU dưới ngưỡng) → bắt buộc calibration.

### 3.2 Tỉ lệ lỗi (mẫu QC, sau calibration)

| Cách đếm | Lỗi | Mẫu số | Tỉ lệ |
|---|---|---|---|
| Theo **đối tượng** | ⟪số đối tượng lỗi⟫ | 358 đối tượng trong mẫu | ⟪⟫ % |
| Theo **ảnh** (ngưỡng Release ≤ 2%) | ⟪số ảnh có ≥ 1 lỗi⟫ | 40 ảnh trong mẫu | ⟪⟫ % |

## 4. Lỗi

### 4.1 Trước calibration (376 ca bất thường trên 35/40 ảnh, từ `calibration_candidates.csv`)

| Hạng | Loại | Số ca | Ảnh ví dụ | Nguyên nhân gốc |
|---|---|---|---|---|
| 1 | Bỏ sót / thừa | 138 / 376 | img_001, img_002 | **Người gán bỏ sót** là chính: 105/138 box liên quan cao ≥ 50 px (vật rõ, không phải vật li ti); nhiều nhất `person` 72, `car` 37, `motorcycle` 28. Một phần do guideline v1.0 chưa có ngưỡng 20 px cho vật nhỏ. |
| 2 | Sai thuộc tính | 122 / 376 (120 là `occluded_level`) | img_001 | **Guideline mơ hồ**: v1.0 chỉ có % khoảng, không nói mốc ranh giới, không có cách ước lượng; 48/120 ca lệch 2–3 mức. |
| 3 | Sai vị trí / kích thước (IoU thấp) | 102 / 376 | img_001, img_003 | Người gán vẽ lỏng/chặt khác nhau; v1.0 chưa nói rõ box người trên xe máy gồm những gì. |
| — | Sai lớp | 11 / 376 | img_005 (car/van), img_028 (car/truck) | **Guideline mơ hồ** ở ranh giới car/van/truck (8/11 ca). |

**Lỗi lặp lại cùng một kiểu:** có — (1) lệch `occluded_level` xuất hiện ở mọi cặp người (đồng thuận 36–63%); (2) bỏ sót `person` ở nền/phía sau lặp lại trên nhiều ảnh.

### 4.2 Sau calibration (mẫu QC, từ `qc_review.csv`)

| Hạng | Loại lỗi | Số lỗi | Ảnh ví dụ | Nguyên nhân gốc |
|---|---|---|---|---|
| 1 | ⟪⟫ | ⟪⟫ | ⟪img_0xx⟫ | ⟪⟫ |
| 2 | ⟪⟫ | ⟪⟫ | ⟪⟫ | ⟪⟫ |
| 3 | ⟪⟫ | ⟪⟫ | ⟪⟫ | ⟪⟫ |

Có lỗi lặp lại cùng một kiểu không? ⟪có/không, kiểu nào⟫

## 5. Calibration

- Số ảnh bị cờ: **35 / 40**. Số dòng quyết định: **376** (một dòng mỗi ca, đủ 35/35 ảnh bị cờ) — xem `calibration_decisions.csv`.
- Ba quyết định quan trọng nhất:
  1. Lệch `occluded_level` (120 ca) → mốc ranh giới thuộc mức cao hơn (10% / 40% / 80%), mẹo chia người 3 phần đầu-vai/thân/chân, kính tính là che, phân vân chọn mức cao hơn + `can_review` → v1.1 mục 2.3.
  2. Bỏ sót/thừa (138 ca) → vật cao < 20 px gộp `ignore_region`, từ 20 px và nhận ra được thì bắt buộc gán; quét 4 góc + nền trước khi sang ảnh → v1.1 mục 3.5, 3.8.
  3. Lệch lớp car/van/truck (8 ca) và phương tiện ngoài 6 lớp → 3 câu hỏi phân biệt theo thứ tự; xe buýt → `car`; tuk-tuk và xe đạp không gán; xe ba gác → `motorcycle` → v1.1 mục 2.1.
- Quy tắc mới đã vào guideline (v1.1): thêm cả các quy tắc xe chở xe (mỗi xe một box), vật nhoè → `ignore_region`, tượng/ma-nơ-canh không gán, xe trưng bày vẫn gán. Taxonomy **không đổi** (không thêm lớp).
- Nguyên nhân bất đồng chính: **guideline mơ hồ** (occluded, car/van/truck, phương tiện lạ) cộng **người gán bỏ sót** vật rõ ở nền ảnh. Không thấy dấu hiệu cả nhóm cùng hiểu sai một quy tắc, nhưng script không phát hiện được loại này (mục 6).

## 6. Script kiểm được gì, không kiểm được gì

- **Script đã kiểm:**
  - `validate_labels.py`: tên file đúng mẫu, lớp thuộc taxonomy, giá trị thuộc tính hợp lệ, box nằm trong ảnh, box cao < 20 px (cảnh báo), đủ ảnh được giao. Cả 4 file nhãn: 0 lỗi (1 cảnh báo box 17 px ở 184, img_039).
  - `qc_compare.py`: IoU, đồng thuận lớp/thuộc tính, bỏ sót/thừa **giữa hai người** cùng gán một ảnh; loại box nằm trong `ignore_region`.
  - `qc_report.py`: hai cách đếm tỉ lệ lỗi và cây gợi ý Hold → Rework → Release.
- **Script không kiểm được, nhóm dùng mắt người:**
  - Box có **ôm sát vật thật** không (script chỉ so hai người với nhau): người kiểm mở `work/review/` xem từng box.
  - Vật mà **cả hai người cùng bỏ sót** hoặc **cùng gán sai lớp**: không tạo bất đồng nên không vào `calibration_candidates.csv`. Mẫu QC ngẫu nhiên được chọn để bắt loại lỗi này.
  - Gán đúng **quy tắc ngữ nghĩa** (hình in trên biển quảng cáo, tượng, xe trưng bày, tuk-tuk): script không biết vật là thật hay hình in; người kiểm đối chiếu với v1.1.
  - `consensus_draft.json` lấy **trung bình toạ độ** hai box: không đảm bảo ôm sát, nên ảnh bị cờ được sửa tay trên CVAT thay vì dùng thẳng.

## 7. Quyết định

**REWORK** — người tổng hợp: 207 - Nguyễn Thái Dương, ngày 2026-11-17.

Lý do (theo cây Hold → Rework → Release):
1. **Guideline chưa rõ / dữ liệu hỏng?** Không Hold: các ca mơ hồ của v1.0 (occluded, car/van/truck, tuk-tuk, xe buýt, vật nhoè, tượng) đã được chốt thành quy tắc trong v1.1; không có ảnh hỏng (cả 40 ảnh mở được, đủ trong `labels_calibrated.json`).
2. **> 2% ảnh lỗi, lỗi lặp lại, hoặc IAA dưới ngưỡng?** Có:
   - IAA lần gán đầu **dưới ngưỡng**: IoU 0.826 < 0.85 (233 cặp box). Đây là điều kiện Rework độc lập với kết quả kiểm mẫu.
   - Có **lỗi lặp lại cùng kiểu** trước calibration: lệch `occluded_level` 120/376 ca, bỏ sót `person` 72/138 ca bỏ sót/thừa.
   - Tỉ lệ ảnh lỗi trong mẫu sau calibration: ⟪x / 40 = y%⟫ (mục 3.2) — chỉ cần ≥ 1 ảnh lỗi là đã vượt ngưỡng 2% (1/40 = 2.5%).
3. **Kết luận: Rework.** Nhãn sau calibration đã sửa các ca bất đồng, nhưng v1.1 đổi nhiều quy tắc (occluded, ngưỡng 20 px, phương tiện ngoài 6 lớp) và **chưa được đo lại IAA** trên quy tắc mới, nên chưa đủ bằng chứng để Release.

Việc cần làm tiếp:
| Việc | Người làm |
|---|---|
| Sửa các lỗi ghi trong `qc_review.csv` trên CVAT, xuất lại, chạy lại `build_final.py` | Người gán ảnh tương ứng (tra `assignment.csv`) |
| Rà toàn bộ `occluded_level` theo cách ước lượng v1.1 mục 2.3 (lỗi lặp lại lớn nhất) | Cả 4 người, mỗi người trên ảnh mình gán |
| Vòng 2: hai người gán lại độc lập ~10 ảnh theo v1.1, chạy `qc_compare.py` để đo lại IAA | 2 người ít ảnh lỗi nhất |
| Chạy lại `qc_sample.py --force` → kiểm → `qc_report.py`; Release khi ảnh lỗi ≤ 2% và IAA vòng 2 đạt | Người kiểm QC (không phải người gán) |
