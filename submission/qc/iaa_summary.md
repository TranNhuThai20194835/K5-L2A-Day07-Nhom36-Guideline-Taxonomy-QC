# Tóm tắt IAA (taxonomy v1.0)

- Ảnh được so sánh (có >=2 người gán): **40**; số người gán: 4
- IoU trung bình và % cùng lớp **chỉ tính trên các cặp box đã ghép** (IoU >= 0.5, ghép 1-1 không phân biệt lớp). Box chưa ghép được thống kê riêng ở cột bỏ sót/thừa.
- Mẫu số: 233 cặp box ghép (tổng mọi cặp người gán, ảnh chung).

| Cặp | Ảnh | Cặp box ghép | IoU TB | Cùng lớp | Cùng occluded_level | Cùng truncated | Chưa ghép (A/B) | Bỏ qua do ignore | Đạt IAA |
|---|---|---|---|---|---|---|---|---|---|
| **TỔNG** | 40 | 233 | 0.826 | 95.3% | 48.5% | 92.3% | 96/42 | 71 | CHƯA ĐẠT |
| 182|184 | 6 | 24 | 0.774 | 87.5% | 62.5% | 100.0% | 4/4 | 5 | CHƯA ĐẠT |
| 182|207 | 9 | 61 | 0.819 | 98.4% | 39.3% | 88.5% | 27/4 | 7 | CHƯA ĐẠT |
| 182|213 | 5 | 36 | 0.806 | 88.9% | 36.1% | 88.9% | 28/9 | 0 | CHƯA ĐẠT |
| 184|207 | 5 | 33 | 0.815 | 93.9% | 57.6% | 97.0% | 15/5 | 2 | CHƯA ĐẠT |
| 184|213 | 9 | 58 | 0.880 | 100.0% | 51.7% | 89.7% | 17/3 | 29 | ĐẠT |
| 207|213 | 6 | 21 | 0.807 | 95.2% | 57.1% | 100.0% | 5/17 | 28 | CHƯA ĐẠT |

Ngưỡng IAA: IoU TB >= 0.85, đồng thuận lớp >= 95%.

Số ca bất thường: 376 ({'bo_sot_thua': 138, 'iou_thap': 102, 'sai_thuoc_tinh': 122, 'co_can_review': 3, 'sai_lop': 11}), trên 35 ảnh cần calibration.
