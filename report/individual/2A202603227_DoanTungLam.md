# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Doãn Tùng Lâm
- MSSV: 2A202603227
- Email: 26ai.lamdt2@vinuni.edu.vn
- Lớp: 2A-LAB-E402
- Link repo bài làm: https://github.com/doantunglam/K5-L2-Day02-DoanTungLam-2A202603227-ObjectDetection
- Link Colab đã chạy (local): [notebooks/runs/detect/Day2-Lab/outputs/yolo26_runs/](../../notebooks/day2_annotation_to_yolo.ipynb).

## 2. Schema và mini guideline

Dẫn tới [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Tóm tắt ba ca mơ hồ đã quyết định, cách áp dụng schema và phần cần giảng viên làm rõ.

- `drive_008`: Xe tải ben màu đỏ chở đống đất/vật liệu cao → Gán nhãn `truck`
- `drive_037` Xe bồn (xe xitec) chuyên dụng màu xanh lam có kích thước vừa → Gán nhãn `truck`
- `drive_042`: Chiếc xe tải hạng nhẹ chở hàng hóa cồng kềnh (các bao tải lớn) chất cao vượt quá đầu xe → Gán nhãn `truck`

## 3. Annotation và self-QC

- Số file nhãn train: 50
- Kết quả checker và lỗi đã xử lý: `python3 tools/check_yolo_labels.py` → "ĐẠT cấu trúc: 50/50 file nhãn hợp lệ".
- Danh sách ít nhất 10 ảnh đã rà bằng mắt: `drive_002`, `drive_005`, `drive_006`, `drive_010`, `drive_013`, `drive_016`, `drive_022`, `drive_027`, `drive_031`, `drive_042`
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa: Không có
- Commit khóa nhãn ban đầu: `caee6d8`

## 4. Gold QC và rework

| Chỉ số              |                          Trước sửa | Sau sửa nếu có |
| ------------------- | ---------------------------------: | -------------: |
| TP                  |                                405 |       Không có |
| FP                  |                                347 |       Không có |
| FN                  |                                248 |       Không có |
| Precision           |                             0.5386 |       Không có |
| Recall              |                             0.6202 |       Không có |
| F1                  |                             0.5765 |       Không có |
| mean_iou_of_matches |                             0.8545 |       Không có |
| passed              | true (yêu cầu đổi thành F1 >= 0.5) |       Không có |

Kết quả cuối: [`outputs/annotation_evaluation.json`](../../outputs/annotation_evaluation.json).

## 5. Kết quả YOLO

- Runtime: Python 3.12.6, Ultralytics 8.4.208, Torch 2.15.0+cpu; chạy trên CPU (AMD Ryzen 9 7945HX with Radeon Graphics), không dùng GPU.
- Cấu hình: pretrained `yolo26n.pt`, 50 epochs, `imgsz=640`, seed `20260909`, device `cpu`. Không thay đổi tham số so với cấu hình trong notebook.
- Best checkpoint: [best.pt](assets/yolo26_runs/vehicle_annotation/weights/best.pt). Các tham số chạy được lưu tại [args.yaml](assets/yolo26_runs/vehicle_annotation/args.yaml); kết quả huấn luyện tại [results.csv](assets/yolo26_runs/vehicle_annotation/results.csv).
- Các chỉ số tổng hợp và đường dẫn checkpoint cũng được lưu trong [model_eval_metrics.json](../../notebooks/Day2-Lab/outputs/model_eval_metrics.json).

| Chỉ số trên 10 ảnh eval | Giá trị |
| ----------------------- | ------: |
| Precision               |   0.448 |
| Recall                  |   0.267 |
| mAP50                   |   0.277 |
| mAP50–95                |   0.204 |

Kết quả theo từng lớp từ cell `best_model.val(...)`:

| Lớp   | Instances | Precision | Recall |  mAP50 | mAP50–95 |
| ----- | --------: | --------: | -----: | -----: | -------: |
| car   |        94 |     0.698 |  0.702 |  0.738 |    0.521 |
| truck |         5 |     0.809 |  0.200 |  0.279 |    0.235 |
| bus   |         2 |     0.000 |  0.000 | 0.0171 |  0.00853 |
| van   |        12 |     0.283 |  0.167 | 0.0727 |   0.0506 |

Ảnh dự đoán của 10 ảnh eval nằm trong [assets/eval_predictions/](assets/eval_predictions/); biểu đồ và ảnh đánh giá nằm trong [assets/yolo26_runs/](assets/yolo26_runs/). Ví dụ, [drive_051.jpg](assets/eval_predictions/drive_051.jpg) cho thấy dự đoán trên ảnh có xe buýt được gán nhãn. Kết quả tổng hợp của lớp `bus` là recall 0/2; box dự đoán không được tính là phát hiện đúng lớp theo đánh giá, có thể do bỏ sót, sai lớp hoặc không khớp box đủ ngưỡng IoU. Xem thêm [ma trận nhầm lẫn](assets/yolo26_runs/vehicle_eval/confusion_matrix.png) và [ma trận chuẩn hóa](assets/yolo26_runs/vehicle_eval/confusion_matrix_normalized.png).

### Trả lời bốn câu hỏi cuối notebook

1. **`bus` có recall thấp nhất: 0.000.** Tập eval có 2 instance bus. Có thể xem ví dụ ảnh [drive_051.jpg](assets/eval_predictions/drive_051.jpg); ma trận nhầm lẫn cho thấy bus không có dự đoán đúng được tính trong đánh giá. Dự đoán trực quan và kết quả đánh giá có thể khác nhau vì đánh giá còn yêu cầu đúng lớp và box đạt ngưỡng IoU.
2. Nếu mAP50 cao nhưng mAP50–95 thấp, mô hình có thể nhận diện được lớp/vật thể nhưng box chưa khớp đủ sát ở các ngưỡng IoU cao hơn. Cần rà box có bao hết phần xe nhìn thấy và bám mép xe không, đồng thời tránh lấy phần nền, bóng hoặc hàng hóa. Ở lần chạy này, cả mAP50 (0.277) và mAP50–95 (0.204) đều thấp; không nên diễn giải đây là trường hợp mAP50 cao.
3. Ma trận nhầm lẫn cho thấy có nhầm lẫn lớp: 4/5 truck bị dự đoán thành car; 11/12 van bị dự đoán thành bus; 2 instance bus không có dự đoán bus đúng. Điều này gợi ý nên rà các ranh giới `car`/`truck`/`bus`/`van` theo mini guideline, nhất là tiêu chí cabin tách biệt của truck và kích thước/kiểu thân xe bus so với van. Đây là lỗi dự đoán của mô hình, chưa đủ để kết luận nhãn ground truth sai; cần kiểm tra trực tiếp ảnh và annotation trước khi sửa nhãn.
4. Không dùng 10 ảnh eval để train để tránh mô hình học các ảnh dùng để đánh giá, làm kết quả kém khách quan. Notebook dùng split eval làm validation trong lúc huấn luyện và chọn `best.pt`; vì vậy đây không phải test set độc lập.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm             | Lỗi quan sát                                                                                                                            | Cách đã thử                                                                                                                   | Kết quả/còn thiếu                                                                                                                                   |
| -------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| Huấn luyện/đánh giá        | Chạy trên CPU nên thời gian huấn luyện 50 epoch khoảng 428 giây; recall tổng thể còn thấp (0.267), đặc biệt bus (0.000) và van (0.167). | Giữ nguyên split train/eval; dùng `best.pt` đánh giá trên eval, xem ma trận nhầm lẫn, ảnh dự đoán và các đường cong theo lớp. | Pipeline chạy xong và lưu kết quả. Cần rà lại ảnh/box/class ở các nhầm lẫn; không dùng eval để train và không tự sửa nhãn chỉ dựa trên lỗi dự đoán. |
| Kiểm soát dữ liệu đánh giá | Notebook dùng eval làm validation để đánh giá và chọn checkpoint.                                                                       | Ghi rõ vai trò của split trong báo cáo, không diễn giải eval như test độc lập.                                                | Kết quả không đại diện cho một test set độc lập; cần một tập test riêng nếu muốn ước lượng khách quan hơn.                                          |

## 7. Tự đánh giá theo rubric

Đối chiếu [RUBRIC.md](../../RUBRIC.md):

| Tiêu chí                                        | Tự đánh giá                                                                                                                                                                                                                                            |
| ----------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Định dạng YOLO và số file                       | Đã có 50 file nhãn train và checker báo 50/50 file hợp lệ; đây là kiểm tra cấu trúc, không tự xác nhận mọi box/class đều đúng.                                                                                                                         |
| Đầy đủ vật thể, hình học box và nhất quán class | Đã self-QC các ảnh ghi ở mục 3 và có mini guideline/các case mơ hồ; vẫn cần rà lại các ca bỏ sót, box và ranh giới lớp được gợi ý bởi kết quả eval.                                                                                                    |
| Mini guideline                                  | Có định nghĩa bốn lớp, quy tắc box và ba quyết định cho ca mơ hồ tại [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md).                                                                                                                                     |
| Gold-set annotation QC                          | Kết quả hiện có F1=0.5765 và mean IoU=0.8545. Mean IoU đạt ngưỡng 0.70, nhưng F1 thấp hơn ngưỡng rubric 0.80; do đó chưa đạt tiêu chí QC theo rubric và cần rework/đánh giá lại.                                                                       |
| Pipeline YOLO và phân tích                      | Notebook đã train, đánh giá và lưu metrics/ảnh; không đưa eval vào train. Tuy nhiên eval được dùng làm validation để chọn `best.pt`, không phải test độc lập. Lần chạy lưu trong notebook là CPU; không có link Colab đã chạy được cung cấp ở báo cáo. |

**Tổng kết:** đã hoàn thành pipeline và có kết quả để phân tích, nhưng chưa đạt ngưỡng F1 của gold-set theo rubric. Cần tiếp tục rà soát/rework annotation dựa trên ảnh và quy tắc, sau đó chạy lại gold-set evaluation; không thể kết luận phần annotation đã đạt chỉ từ việc checker hợp lệ.

## 8. Công cụ AI và tài liệu tham khảo

- Công cụ AI: Copilot được dùng để hỗ trợ tổng hợp kết quả notebook và diễn đạt các phần phân tích trong báo cáo. Các số liệu và nhận xét được đối chiếu với output đã lưu, bảng metrics, ảnh eval và rubric trong repository.
- Tài liệu trong repository: [notebook Day 2](../../notebooks/day2_annotation_to_yolo.ipynb), [RUBRIC.md](../../RUBRIC.md), [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md) và [model_eval_metrics.json](../../notebooks/Day2-Lab/outputs/model_eval_metrics.json).
- Nguồn ngoài repository: không sử dụng nguồn ngoài để viết phần báo cáo này.
