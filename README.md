# ✍️ IELTS Writing Task 2 Evaluation

Repo này triển khai hệ thống **chấm điểm + sinh feedback** cho IELTS Writing Task 2 bằng mô hình LLM, retrieval và agent workflow.

---

## 📁 Cấu trúc chính

- `score_training/` — notebook huấn luyện mô hình chấm điểm theo 4 tiêu chí IELTS.
- `score_inference/` — notebook chạy **scoring-only inference** (không feedback agent).
- `full_inference/` — notebook pipeline đầy đủ (**scoring + retrieval + feedback + orchestration**).
- `dataset/` — dữ liệu train/val/test và các bản augmented.
- `baseline/` — các thử nghiệm baseline với nhiều backbone khác nhau.
- `feature_engineering.ipynb`, `data_aug.ipynb`, `eda_*.ipynb` — tiền xử lý, tăng cường dữ liệu, EDA.

---

## 🚀 Notebook chính (đã cập nhật theo test_7 / LANGGRAPH_16)

### 1) Scoring-only inference: `score_inference/test_7_inference.ipynb`

**Mục tiêu**
- Load model export từ training (`export_meta.json`, `custom_heads.pt`) và dự đoán điểm:
  - **TR** (Task Response)
  - **CC** (Coherence & Cohesion)
  - **LR** (Lexical Resource)
  - **GRA** (Grammatical Range & Accuracy)
  - **Overall** (trung bình 4 tiêu chí, làm tròn band 0.5)

**Điểm kỹ thuật chính**
- Kiến trúc: `Qwen/Qwen2.5-3B-Instruct` + LoRA + custom multi-head/gating fusion.
- Kết hợp representation từ backbone + handcrafted features theo từng tiêu chí.
- Có hàm `predict_ielts(...)` cho 1 bài và `predict_dataframe(...)` cho batch DataFrame.

---

### 2) Full pipeline (LangGraph, bản tốt nhất):
`full_inference/test_7_inference_retriever_full_zero_mistral_tool_use_automatic_16_best_LANGGRAPH_A100.ipynb`

Đây là notebook nên ưu tiên khi cần chạy end-to-end vì đã tích hợp LangGraph và profile tối ưu cho A100.

Pipeline gồm:

1. **Setup + Auto runtime profile**
   - Tự nhận diện GPU (`A100` vs GPU khác) để bật/tắt fast mode.
   - Trên A100 có thể chạy full flow (feedback/revise/verify đầy đủ).

2. **Scoring model (Qwen + heads)**
   - Load model export + metadata như `max_length`, feature stats, fusion config.
   - Dự đoán TR/CC/LR/GRA và overall band.

3. **Retrieval grounding**
   - Build retrieval DB từ train CSV.
   - Embedding bằng `all-MiniLM-L6-v2`.
   - Có **quality-aware retrieval index** để lấy mẫu tham chiếu phù hợp hơn cho từng tiêu chí.

4. **Feedback model (Mistral)**
   - Dùng `Mistral-7B-Instruct-v0.3` cho phần giải thích/gợi ý cải thiện.
   - Có tool theo bước: predict → mismatch detect → retrieve → generate → verify → revise.

5. **LangGraph orchestration**
   - Dùng `StateGraph` để điều phối node/tool, routing điều kiện, retry loop.
   - Có cell topology để trực quan luồng graph.

6. **Demo UI**
   - Có phần giao diện demo bằng Gradio để nhập prompt/essay và nhận kết quả.

---

## 🔁 Các biến thể test_7 trong `full_inference/`

- `..._14.ipynb`: bản pipeline tool-use trước khi tích hợp LangGraph.
- `..._15_LANGGRAPH.ipynb`: bản chuyển sang LangGraph.
- `..._16_best_LANGGRAPH_A100.ipynb`: bản tối ưu/ổn định hơn cho full run trên A100 (khuyến nghị).

---

## ▶️ Luồng chạy khuyến nghị

1. `eda_hf.ipynb` / `eda_aug.ipynb`
2. `feature_engineering.ipynb`
3. `data_aug.ipynb`
4. notebook train trong `score_training/` (ví dụ `qwen_3b_test_8.ipynb`)
5. `score_inference/test_7_inference.ipynb` (kiểm tra scoring)
6. `full_inference/test_7_inference_retriever_full_zero_mistral_tool_use_automatic_16_best_LANGGRAPH_A100.ipynb` (chạy full pipeline)

---

## 📝 Ghi chú quan trọng

- README cũ tham chiếu notebook `test_8` không còn trong repo; hiện tại nên dùng nhánh notebook **test_7**.
- Nếu bạn chỉ cần chấm điểm nhanh: chạy `score_inference/test_7_inference.ipynb`.
- Nếu bạn cần phản hồi chi tiết có grounding + orchestration: chạy bản `LANGGRAPH_16` trong `full_inference/`.
