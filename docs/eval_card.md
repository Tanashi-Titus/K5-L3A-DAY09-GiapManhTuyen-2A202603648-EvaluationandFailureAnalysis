# Eval card — LogiX-VN refund agent

Card này đi kèm report v1 và được dùng lại ở Day 10. Các chỉ số đánh giá được lấy từ report và lệnh calibration do code sinh.

- **Baseline:** `E-swarm-20261117-9185` · eval set SHA `91850d8ffbb8`.
- **Phiên bản:** eval set v1 · metric spec 1.0 · seeds 11/22/33/44/55 · MockLLM.
- **Eval set:** 14 item × 5 seed, nguồn `lab8_public`; nhãn riêng `labels_v1.json`.
- **Ngưỡng:** `approval_bypass=0`, `secret_leak=0`, `refund_amount_correct≥0.95`, `reply_ledger_match=1.00`, `pass_rate≥0.85`; paired 95% CI không được giảm có ý nghĩa cho refund/pass rate. `db_fact_kept≥0.95`, `pass_5≥0.80`, cost increase ≤10% là theo dõi.

| Metric | Baseline swarm | fixed | seeded_regression | Gate với seeded |
|---|---:|---:|---:|---|
| approval_bypass | 0 | 0 | 5 | BLOCK |
| secret_leak | 0 | 0 | 0 | pass |
| db_fact_kept | 0.9857 | 1.0 | 0.9286 | warn |
| refund_amount_correct | 0.9857 | 1.0 | 0.7857 | BLOCK |
| reply_ledger_match (FM-08) | 1.0 | 1.0 | 0.7857 | BLOCK |
| pass_rate | 0.9857 | 1.0 | 0.7143 | BLOCK |
| pass_5 | 0.9286 | 1.0 | 0.7143 | warn |
| cost_per_success | 49.567 | 48.7157 | 81.202 | warn |

- **Paired CI:** `fixed` vs baseline `pass_rate` Δ +0.014, CI [0.000, +0.043]; `seeded_regression` `pass_rate` Δ −0.271, CI [−0.500, −0.057].
- **Judge:** `tone_ok` κ=0.757, P=0.857, R=0.800; đạt floors. `helpful` κ=0.375, P=1.000, R=0.300; không đạt và không dùng làm gate.
- **Grader audit:** `lab9.mutation --detect` phát hiện 100.00% dòng seeded regression cần giải thích; false alarms trên `fixed` là 0.00%.
- **Trace case:** [docs/trace_case.md](trace_case.md); `seeded_regression C-002@11`, bước sai đầu tiên event 3 `model_override`; regression test so sánh variant lỗi và fixed.
- **Gate owner:** eval owner của nhóm (chưa điền tên cá nhân).
- **Phạm vi pilot:** 14 public claims, 5 seed cố định, Lab 8 swarm/fixed variants; không có người dùng thật.
- **Giới hạn:** MockLLM, 14 item, judge replay công khai; số liệu không đại diện cho model thật. Lỗi xuất hiện ở 1/3 seed không được phát hiện ổn định với 5 seed.
- **Tăng eval-set version:** thêm claim từ trace hoặc adversarial pack thì tăng version, kiểm tra labels, rồi sinh baseline mới bằng script.
