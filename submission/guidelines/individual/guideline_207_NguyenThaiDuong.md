# Guideline gán nhãn — 207 - Nguyễn Thái Dương (bản cá nhân)

- Phiên bản: v0.1-207 (sửa từ v0)
- Loại nhãn: bounding box trên ảnh đường phố châu Á · CVAT · COCO 1.0

## 1. Mục tiêu
Nhãn dùng để huấn luyện mô hình phát hiện **người và phương tiện thật đang tham gia giao thông** trên ảnh đường phố.
[Vật nào KHÔNG muốn mô hình học là xe/người? Ví dụ: tượng, ma-nơ-canh, hình in…]

## 2. Lớp và thuộc tính

### Lớp
| Lớp | Định nghĩa | Ví dụ (ảnh) | Phản ví dụ / dễ nhầm |
|---|---|---|---|
| `car` | [...] | [img_0xx] | [...] |
| `truck` | [...] | | |
| `van` | [...] | | |
| `motorcycle` | [...] | | |
| `person` | [...] | | |
| `ignore_region` | [...] | | |

[Có cần thêm lớp không (bus, bicycle, tuk-tuk…)? Hay gộp vào lớp có sẵn? Ghi lý do.
Phép thử: mô hình có phải đoán ra điều này không? Có → lớp. Không → thuộc tính.]

### Thuộc tính
| Thuộc tính | Giá trị | Ngưỡng đo được |
|---|---|---|
| `occluded_level` | 0 / 1 / 2 / 3 | 0: che < [..]% · 1: [..]–[..]% · 2: [..]–[..]% · 3: > [..]% |
| `truncated` | no / yes | yes khi [...] |
| `can_review` | tick / không | tick khi [...] |

## 3. Quy tắc vẽ