# Báo cáo Day 13 — Nguyen Dinh Phu, 2A202603333

## 1. Kết luận trước

- **Thông tin:** mã số 2A202603333 · task `D13-2A202603333` · frame s01f35, s01f56, s02f27, s02f46, s03f04, s03f33, s04f02, s04f21, s05f14, s05f40 · Windows 10, Brave (Chromium), CVAT v2.81.0 (Docker local) · MSSV ngồi cạnh: 2A202603322.
- **Tổng:** 132 cuboid (119 Car, 7 Cyclist, 5 Truck, 1 Bus), 91 hộp trong 30 m.
- **Frame ít nhất:** s02f46 (6 hộp): đường có rào chắn giữa, trong 50 m chỉ 6 xe rõ, còn lại là cây.
- **Selfcheck:** 11/132 hộp còn cờ, đều đã xem lại, lý do giữ ở mục 5.
- **Mạnh nhất:** hướng, kích thước xe con trong 30 m. **Yếu nhất:** vật 30–50 m (kích thước suy luận), đáy hộp cạnh nền cao/đảo giao thông, class xe hai bánh lẫn xe dựng.

Cách dùng CVAT:

- **Góc nhìn:** chuột trái xoay, chuột phải dời, con lăn phóng to; nút mũi tên, U/I/O/J/K/L nghiêng hoặc hạ camera.
- **Vẽ, chỉnh:** nút cuboid → label → **Shape** → nháy đúp lên cụm điểm; **Top** chỉnh tâm, dài, rộng, yaw; **Side/Front** chỉnh đáy, cao, không xoay.
- **Kiểm hướng:** **Appearance → Cuboid orientation** (vạch đỏ `+X` chỉ đầu xe), đối chiếu camera; cuboid trên 7 camera: `python scripts/lab.py preview` (`work/xem/`).

## 2. Đọc frame (Bước 2)

Ảnh: `t1-<frame>-3d`, `t1-<frame>-top` (có đánh dấu phía trước, +x, +y).

| Frame  | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m)  | Vật xa nhất đọc được (~ m)                                         | Loại vật thấy                                               | Vật phía sau / hai bên                                                                                                            | Cụm chưa đọc được                                                                        |
| ------ | ---------------------------------------------------------------------- | ------------------------------------------ | ------------------------------------------------------------------ | ----------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| s01f35 | Cạnh dưới (−y), khớp cam01; +x sang phải ảnh; +y lên trên              | Phẳng, ~1,75 m                             | ~49 m: xe cùng chiều phía trước (cam03 xác nhận)                   | Xe con, xe tải nhỏ, xe đạp/xe máy trên vỉa hè, cột đèn, cây | Trước-trái ~9 m: xe con trắng; sau ~9 m: xe con đi theo (cam09); sau-trái: xe con, xe tải (cam05); phải: xe đạp đỗ (cam07, cam08) | Cụm thấp sau-phải ~15–20 m: bồn cây hay xe đỗ                                            |
| s01f56 | Cạnh dưới (−y), hàng xe chờ đèn ở nửa dưới; +x sang phải; +y lên trên  | Phẳng, ~1,78 m; làn trái thấp hơn (~2,2 m) | ~37 m: xe cuối hàng chờ đèn; ~40 m: cụm người/xe hai bánh ở ngã tư | Xe con, người, xe đạp, giá biển báo, cây                    | Sau ~6 m: xe con (cam09); sát trái: xe con trắng (cam05, cam06); phải: người đi xe đạp (cam07)                                    | Vạch điểm ngang ~19 m phía trước: giá biển hay vạch dừng; chấm nhỏ > 35 m: người hay cột |

- **Chỉ camera:** màu, loại xe; tín hiệu giao thông (biển, đèn, vạch); đầu hay đuôi xe (LiDAR dễ nhầm 180°).
- **Chỉ LiDAR:** khoảng cách theo mét (~9 m trước-trái, ~9 m phía sau ở s01f35); độ cao mặt đường (~1,75–1,8 m); toàn cảnh 360°, kể cả vùng sát thân xe camera bị che.

| Frame  | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có)                                                                             |
| ------ | ------------------------------------------------ | -------------------------------------------------------------------------------------------- |
| s02f27 | Cạnh dưới (−y)                                   | Rào chắn giữa đường tạo vạch điểm dài, dễ nhầm thành xe; intensity tối đa ~98 (seq 01: ~242) |
| s02f46 | Cạnh dưới (−y)                                   | Lá cây dày lẫn xe đỗ; xe đỏ trước-trái                                                       |
| s03f04 | Cạnh dưới (−y)                                   | Kẹt xe, xe sát nhau dễ dính cụm; tường rào công trường bên trái che hết                      |
| s03f33 | Cạnh dưới (−y)                                   | Xe trắng sát trước-phải, xe máy phía trước, xe buýt đỏ phía sau xa                           |
| s04f02 | Cạnh dưới (−y)                                   | Ngã tư rộng; xe van đen sát trước-phải che một phần                                          |
| s04f21 | Cạnh dưới (−y)                                   | Qua vạch người đi bộ; nhiều xe đi ngang                                                      |
| s05f14 | Cạnh dưới (−y)                                   | SUV đỏ sẫm sát trước-phải; ngã tư phía sau                                                   |
| s05f40 | Cạnh dưới (−y)                                   | Xe buýt sát trái phía trước che phần lớn bên trái                                            |

## 3. Ba cuboid có hướng dẫn (Bước 3)

Ảnh: `t2-a-car`, `t2-b-bus-truck`, `t2-c-ped-cyclist`.

| Vật                              | Frame, idx                                                  | Class   | Mũi tên yaw đúng hướng?        | Đáy chạm đường?                                                      | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
| -------------------------------- | ----------------------------------------------------------- | ------- | ------------------------------ | -------------------------------------------------------------------- | ----------------------------- | -------------------------- |
| (a) xe con gần                   | s01f35, 1                                                   | Car     | Có, 277,2° (cùng chiều, ~9 m)  | Có, −1,68 m                                                          | Có                            | Có, 4,46 × 1,88 × 1,46 m   |
| (b) xe buýt hoặc xe tải          | s01f35, 8                                                   | Truck   | Có, 91,2° (ngược chiều, ~15 m) | Có, −1,46 m (cờ "chìm" do selfcheck đo mặt đường trên dải phân cách) | Có                            | Có, 5,87 × 2,23 × 2,41 m   |
| (c) người đi bộ hoặc xe hai bánh | s03f33, 2 (frame 1, 2 không có xe hai bánh có người lái rõ) | Cyclist | Có, 274,1° (cùng chiều, ~19 m) | Có, −1,76 m                                                          | Có                            | Có, 2,11 × 0,82 × 1,43 m   |

Vòng hiệu chuẩn (task chung `D13-HC485087`, theo yêu cầu coach tự chọn ngẫu nhiên 3 vật):

- Frame s05f48: Truck (xe van xanh sát phải, 4,63 m, 5,19 × 2,03 × 2,28 m, 269,1°); Car (trước-trái bên kia dải phân cách, 9,16 m, 90,5°); Car (sau-trái, 29,57 m, 23 điểm, 85,5°). Số đo lấy từ prelabel CenterPoint, không chỉnh tay; file `work/hieuchuan/D13-HC485087-2A202603333-bangA.csv` đã gửi coach.
- Điểm dễ lệch: class xe van (cửa sổ hai bên, có thể là Car hoặc Bus thay vì Truck: UNCERTAIN_CLASS); kích thước và đáy xe 29,57 m chỉ suy luận từ 23 điểm; hướng xe ngược chiều (90°).
- Kết quả so với các bạn và cách hiểu cả lớp chốt: chưa có phản hồi từ coach khi viết báo cáo.

Bỏ qua vì không đủ cơ sở:

- s01f56, ngã tư trước-phải ~40–43 m: cụm người/xe hai bánh, 10–30 điểm, không rõ có người lái (UNCERTAIN_CLASS, LOW_VISIBILITY).
- s04f21, trước-phải ~43 m: cụm 14 điểm cạnh rào chắn (LOW_VISIBILITY).
- s01f35, vỉa hè phải ~17 m: giá xe đạp dựng, không người lái.

## 4. Bốn hiện tượng chất lượng (Bước 4)

Nguyên nhân là giả thuyết; cả bốn lấy từ frame được giao.

| Hiện tượng                           | Frame / tên ảnh / nguồn     | Nguyên nhân (giả thuyết)                                                    | Thấy gì trên point cloud                                                                                                    | Hệ quả khi gán (theo guideline)                                                                            |
| ------------------------------------ | --------------------------- | --------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| 1. Bóng che                          | s05f40, `t4-1-bong-che`     | Xe buýt ~12,5 m sát trái (~3 m) chắn tia LiDAR                              | Sau xe buýt gần như trống; idx 5 (~29 m) 41 điểm, idx 7 (~35 m) 18 điểm, so với ~200 và ~70 ở cùng tầm                      | Bật occluded cho idx 5, 7; hộp bao phần suy luận được; vùng trống là chưa quan sát, không phải đường trống |
| 2. Thưa ở xa                         | s01f35, `t4-2-thua-o-xa`    | Góc giữa các tia cố định, càng xa khe giữa tia càng lớn                     | idx 7 (~47 m) 49 điểm, chỉ đuôi và nóc                                                                                      | Thấy ~15–20% thể tích nên vẽ theo bảng 4.3 (4,5 × 1,9 × 1,45 m); thưa hơn thì bỏ, ghi LOW_VISIBILITY       |
| 3. Phản xạ thấp / ít điểm bất thường | s04f02, `t4-3-phan-xa-thap` | Sơn tối phản xạ ít năng lượng laser                                         | Xe xám tối idx 2 (~15 m) intensity 0,46; xe bạc idx 3 (~16 m) 1,4; biển báo ~7 m ~32; số điểm không ít hơn (705 so với 585) | Chỉ ghi giả thuyết; không nới, thu hộp theo intensity                                                      |
| 4. Điểm lạ                           | s02f27, `t4-4-diem-la`      | Phản xạ nhiều lần (multipath) làm quãng đo dài hơn thực tế, chưa kiểm chứng | ~22 điểm sau-trái ~29 m ở z ≈ −13,8 m (~12 m dưới mặt đường), intensity tới 98; 8 điểm trước-phải ~24 m ở z ≈ −11,1 m       | Không gán thành vật                                                                                        |

## 5. Số từ cuboid (Bước 5)

Nguồn: `submission/D13-2A202603333-bangA.csv`, `points_in` từ `-tukiem.csv`. Mặt đường ~−1,7 m; lệch hơn 0,3 m là lơ lửng hoặc chìm.

| Cuboid (frame, idx, class) | z        | h       | z đáy = z − h/2 | dist_m  | r = √(x²+y²+z²) | yaw_deg | Nhận xét (lơ lửng / chìm / chạm đường)                                        |
| -------------------------- | -------- | ------- | --------------- | ------- | --------------- | ------- | ----------------------------------------------------------------------------- |
| s01f35, 1, Car             | −0,952 m | 1,460 m | −1,682 m        | 9,13 m  | 9,18 m          | 277,2°  | Chạm đường (hở 0,09 m)                                                        |
| s03f33, 1, Car             | −0,932 m | 1,615 m | −1,740 m        | 4,76 m  | 4,85 m          | 268,9°  | Chạm đường (hở 0,03 m)                                                        |
| s02f46, 1, Car             | −1,243 m | 1,570 m | −2,028 m        | 11,31 m | 11,38 m         | 87,2°   | Chạm đường; làn ngược chiều thấp hơn (~−2,05 m); trước khi sửa lơ lửng ~0,2 m |

- **(a)** Gần nhất: s03f04 idx 1, `dist_m` 3,83 m, r 3,96 m. Xa nhất trong vùng chấm: s04f21 idx 36, `dist_m` 49,79 m, r 49,80 m. s01f35 idx 2 có `dist_m` 50,36 m (đủ dài 4,4 m thì tâm vượt 50 m), không được chấm. Ở gần r > `dist_m` rõ vì z ≈ −1 m; ở xa gần bằng nhau.
- **(b)** Xe con s01f35 idx 1: 4,46 × 1,88 × 1,46 m (sedan 3,8–5,0 × 1,70–1,90 × 1,40–1,60). Xe máy có người s03f33 idx 2: 2,11 × 0,82 × 1,43 m (1,8–2,3 × 0,70–0,95 × 1,40–1,80). Không chiều nào ngoài khoảng.
- **(c)** Cùng chiều: s01f56 idx 3, 271,1° (≈ 270°). Ngược chiều: s01f35 idx 8, 91,2° (≈ 90°).

| Cuboid (frame, idx, class) | dist_m  | Số điểm (`points_in`) |
| -------------------------- | ------- | --------------------- |
| s04f21, 2, Car             | 6,96 m  | 2011                  |
| s04f02, 3, Car             | 16,35 m | 585                   |
| s04f02, 10, Car            | 25,19 m | 233                   |
| s02f46, 5, Car             | 38,29 m | 86                    |

- **Quy luật:** xa gấp ~2,3 lần còn ~1/3 số điểm, gấp ~5,5 lần còn ~1/23: giảm nhanh hơn tỉ lệ nghịch với khoảng cách.
- **So với slide:** trung vị xe con không bị che 2011 / 631 / 234 / 86 / 56 (0–10 … 40–50 m), slide 1968 / 718 / 214 / 72: khớp.

Đã sửa sau `preview` / `selfcheck`:

- s02f46 idx 1: đáy −1,83 → −2,03 m (39 điểm dưới đáy).
- s04f02 idx 7: đáy −0,95 → −1,10 m.
- s05f14 idx 13 (xe ben): yaw 295° → 270°, 6,8 × 2,5 × 3,35 m, hạ đáy ~0,45 m.
- s01f35 idx 7: lệch 90° (dài 1,79 < rộng 2,36 m) → 270°, 4,5 × 1,9 m.
- Bật occluded cho xe bị che (s05f40, s03f04, s01f56); xoá hộp xe hai bánh xa không đủ cơ sở.

Cảnh báo giữ nguyên:

- s03f33 idx 3, 4, 7 (lơ lửng 0,7–1,0 m): xe đỗ trên nền cao, đáy trùng điểm thấp nhất của xe.
- s01f35 idx 4, s03f04 idx 6 (lơ lửng 0,5–0,6 m): trên vỉa hè/bồn cây, đáy khớp mặt vỉa hè.
- s01f35 idx 8, s04f21 idx 12, 13, s05f14 idx 9 (chìm 0,4–0,64 m): selfcheck đo mặt đường trên dải phân cách/đảo giao thông cạnh xe; đỉnh xe ~−0,4 m, nâng đáy theo cờ thì hộp chỉ còn cao ~0,7 m.
- s01f56 idx 11 (2 điểm): bị hàng xe phía trước che, camera tele vẫn thấy; occluded.
- s05f14 idx 14 (38 điểm sát mép): điểm của hàng rào cây bên cạnh.

Đổi kiểm với 2A202603322: bạn chọn hai con số ở mục 5 và đối chiếu với bảng A mở trên máy (chỉ xem màn hình): cả hai khớp, không phải sửa.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc

(a)

- **Mét, không phải pixel:** model 3D cần vị trí, kích thước, hướng trong không gian thật để tính khoảng cách, tốc độ, va chạm; xe con luôn dài ~4,5 m dù ở 5 m hay 45 m, còn số pixel đổi theo khoảng cách.
- **Quy tắc cho vật thưa:** vài chục điểm không đủ đo hình dạng; thiếu quy tắc chung (~15–20% thể tích, bảng 4.3, LOW_VISIBILITY) thì nhãn mâu thuẫn giữa người gán, model học nhầm hoặc "đoán" vật từ nhiễu.

(b) Dùng AI, có sự cho phép của giảng viên:

- **Công cụ:** Claude Code (đọc, nghiên cứu repo, soạn template báo cáo).

(c) Chưa chắc:

- s01f35: ba hộp Cyclist tự vẽ trong bãi xe đạp dựng, chưa rõ có người trên xe (UNCERTAIN_CLASS).
- s04f21 idx 22: SUV phía sau bị che, 9 điểm trong hộp; vị trí ngang ước lượng theo camera.
- s05f14 idx 9: phần thấy được cao ~0,6 m, đáy thật có thể thấp hơn −1,24 m.
- Kích thước xe 30–50 m suy luận theo bảng 4.3, sai số có thể trên 15%.
- Nguyên nhân ở mục 4 (điểm lạ, intensity thấp) mới là giả thuyết.
