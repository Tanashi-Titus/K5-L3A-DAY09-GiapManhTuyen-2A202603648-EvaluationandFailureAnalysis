# Trace case: từ trace tới quyết định sai đầu tiên

Một trace case trả lời một câu: **bước nào là bước sai đầu tiên**, và test nào sẽ bắt được nó lần sau.

```bash
cd src
python -m lab9.trace_case --shape seeded_regression --claim C-002 --seed 11
python -m lab9.trace_case --shape fixed --claim C-002 --seed 11   # đối chứng
```

Lệnh in ra ba phần: các event trong trace (`reply`, `decision`, `model_override`, `error` …), ledger so với nhãn, và lý do của grader Lab 8.

## Mẫu điền (mỗi nhóm 1 case, lấy từ trial đang fail trong report)

| Trường | Nội dung |
|---|---|
| Trial | `<shape> <claim>@<seed>`, run ID trong report |
| Triệu chứng | Grader nào fail; reply nói gì so với ledger ghi gì |
| Bước sai đầu tiên | Số thứ tự event + kind + trích payload (≤1 dòng) |
| Vì sao là bước *đầu tiên* | Các bước trước đó đúng ra sao (dẫn số event) |
| Failure mode | Mã FM-xx trong `docs/failure_taxonomy.md` |
| Đối chứng | Cùng claim@seed trên shape `fixed`: bước đó khác thế nào |
| Regression test | Tên test + grader/assert nào bắt được; đã thấy fail trên variant lỗi và pass trên `fixed` chưa |
| Chưa chắc chắn | Điều trace không chứng minh được |

## Case được quan sát

| Trường | Nội dung |
|---|---|
| Trial | `seeded_regression C-002@11`; report run ID `E-seeded_regression-20261117-9185` |
| Triệu chứng | `TrialPass` FAIL: kết quả `resolved` trong khi nhãn là `denied`; ledger ghi `20.000đ` thay vì `0đ`. |
| Bước sai đầu tiên | Event 3 `model_override`: `delay_days` bị đổi thành `5`. |
| Vì sao là bước *đầu tiên* | Event 1 tạo handoff không mang fact thay đổi; event 2 đọc DB cho đơn `O-202` với `delay_days=2`. Hai bước này nhất quán. Event 3 là lần đầu agent ghi đè fact DB. |
| Failure mode | FM-03 `ticket_trust` |
| Đối chứng | `fixed C-002@11`: không có event `model_override`; quote là `0đ`, status `denied`, ledger `0đ`, grader PASS. |
| Regression test | `test_ticket_trust_trace_regresses_and_fixed_passes` trong `src/tests/test_lab9_graders.py`; kiểm tra variant lỗi FAIL và `fixed` PASS. |
| Chưa chắc chắn | Trace chứng minh đường đi của shape MockLLM/seed này, không chứng minh tần suất lỗi trên model hoặc dữ liệu thật. |

## Quy tắc

- Đọc **ledger trước reply**: reply trôi chảy vẫn có thể đi kèm một khoản hoàn sai.
- "Bước sai đầu tiên" phải dẫn số event cụ thể, không phải "agent bị nhầm".
- Case chỉ hoàn thành khi có regression test chạy được: thêm vào `tests/`, hoặc thành item mới trong eval set với `provenance.from_trace`.
