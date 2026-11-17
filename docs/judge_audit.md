# Judge audit

Chạy: `python -m lab9.judge --csv <file> --audit 5`

| Tiêu chí | n | κ | Precision | Recall | Đạt FLOORS (κ≥0.6, P≥0.8, R≥0.7)? | Quyết định |
|---|---|---|---|---|---|---|
| tone_ok | 100 | 0.757 | 0.857 | 0.800 | Có | Dùng cho tiêu chí tone |
| helpful | 100 | 0.375 | 1.000 | 0.300 | Không | Loại khỏi release gate |

## ≥5 case audit thủ công
| # | Case | Judge nói | Người nói | Ai đúng? | Ghi chú |
|---|---|---|---|---|---|
| 1 | H-008: “Em đã hoàn tiền cho anh rồi nha, anh check giúp em.” | PASS | FAIL | Human | Cách xưng hô và “nha/check” quá suồng sã cho thông báo dịch vụ; thêm vào rubric ví dụ thân mật quá mức. |
| 2 | H-017: “Dạ, bạn ơi LogiX đã hoàn 25.000đ cho đơn O-606 rồi nha.” | PASS | FAIL | Human | “Bạn ơi/nha” thiếu độ trang trọng ổn định; giữ “Dạ” nhưng yêu cầu xưng hô lịch sự nhất quán. |
| 3 | H-029: “Dạ, vui lòng chờ. Hệ thống đang xử lý, không cần nhắn thêm.” | PASS | FAIL | Human | “Không cần nhắn thêm” nghe gắt; rubric cần đánh FAIL cho câu lịch sự bề mặt nhưng mang tính ra lệnh. |
| 4 | H-042: “Dạ, đơn O-808 đã hoàn 120.000đ. Tiền về trong 3–5 ngày ạ.” | FAIL | PASS | Human | Câu ngắn nhưng vẫn lịch sự và rõ; không nên bắt buộc một mẫu câu dài cố định. |
| 5 | H-051: “Dạ, LogiX đã refund 90.000đ cho đơn O-202 ạ.” | FAIL | PASS | Human | Trộn từ “refund” nhưng giọng vẫn lịch sự; tách tiêu chí tone khỏi lựa chọn từ/ngôn ngữ. |

## Kết luận
- `tone_ok`: n=100, ma trận TP=24, FP=4, FN=6, TN=66; đạt các floors nên có thể dùng cho tone.
- `helpful`: n=100, ma trận TP=9, FP=0, FN=21, TN=70; precision cao nhưng recall thấp và κ dưới sàn nên không dùng làm gate.
- Tỷ lệ FAIL quan sát của tone là 0.28, Rogan–Gladen hiệu chỉnh thành 0.30; helpful là 0.09 và hiệu chỉnh thành 0.30.
- Rủi ro còn lại: judge chỉ được kiểm trên dữ liệu replay đã công khai; cần nhãn người mới để có held-out thật.
