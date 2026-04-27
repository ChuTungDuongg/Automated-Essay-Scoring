# ✍️ IELTS Writing Task 2 Evaluation

Repo này triển khai hệ thống **chấm điểm + sinh feedback** cho IELTS Writing Task 2 bằng mô hình LLM, retrieval và agent workflow.

---

## 📁 Cấu trúc chính

- `score_training/` — notebook huấn luyện mô hình chấm điểm.
- `full_inference/` — notebook pipeline inference đầy đủ (scoring + retrieval + feedback agent).
- `dataset/` — dữ liệu dùng cho train/val/test.
- `baseline/` — các thử nghiệm baseline với nhiều backbone khác nhau.
- `feature_engineering.ipynb`, `data_aug.ipynb`, `eda_*.ipynb` — tiền xử lý, tăng cường dữ liệu, EDA.

---

## 🚀 Notebook quan trọng

### 1) Training: `score_training/qwen_3b_test_8.ipynb`

**Mục tiêu**
- Huấn luyện mô hình chấm 4 tiêu chí IELTS:
  - **TR** (Task Response)
  - **CC** (Coherence & Cohesion)
  - **LR** (Lexical Resource)
  - **GRA** (Grammatical Range & Accuracy)

**Thiết kế**
- Backbone: `Qwen/Qwen2.5-3B-Instruct` + LoRA.
- Input: prompt + essay (định dạng examiner).
- Kết hợp thêm handcrafted features theo từng tiêu chí rồi fusion với representation của mô hình.

**Dữ liệu chính**
- `ielts_train_aug_df.csv`
- `ielts_evals_aug_df.csv`
- `ielts_test_locked_df.csv`

**Cấu hình đáng chú ý**
- `max_length=1536`, `batch_size=6`, `grad_accum=4`
- `lr=4e-5`, `epochs=10`, `weight_decay=0.01`
- Chọn checkpoint tốt nhất theo `eval_mean_qwk`

---

### 2) Full inference: `full_inference/test_8_inference_retriever_full_zero_mistral_tool_use_automatic_2.ipynb`

Pipeline end-to-end gồm:

1. **Predict scores**
   - Load scoring model (Qwen + LoRA + custom heads).
   - Dự đoán TR/CC/LR/GRA + Overall.

2. **Retrieval grounding**
   - Tạo retrieval DB từ tập essay train.
   - Embedding bằng `all-MiniLM-L6-v2`.
   - Lấy bài tương tự để làm ngữ cảnh cho feedback.

3. **Tool-using feedback agent**
   - Dùng `Mistral-7B-Instruct-v0.3` cho phần giải thích/gợi ý cải thiện.
   - Tool flow gồm các bước như: predict, detect mismatch, retrieve, generate, verify, revise.

4. **Verify & revise loop**
   - Kiểm tra feedback theo tiêu chí và evidence.
   - Nếu chưa đạt thì sửa cục bộ theo tiêu chí lỗi trong giới hạn retry.

5. **Demo UI**
   - Có cell Gradio để chạy demo end-to-end.

---

## 🛠️ Notebook hỗ trợ

- `feature_engineering.ipynb`: xử lý dữ liệu, tạo feature ngôn ngữ/semantic, chuẩn bị instruction text.
- `data_aug.ipynb`: hợp nhất và chuẩn hóa dữ liệu, tạo các file augmented cho train/eval/test.
- `eda_aug.ipynb`, `eda_hf.ipynb`: EDA cho các nguồn dữ liệu khác nhau.
- `data_augmentation.ipynb`: thử nghiệm thêm các chiến lược augment.

---

## ▶️ Luồng chạy khuyến nghị

1. `eda_hf.ipynb` / `eda_aug.ipynb`
2. `feature_engineering.ipynb`
3. `data_aug.ipynb`
4. `score_training/qwen_3b_test_8.ipynb`
5. `full_inference/test_8_inference_retriever_full_zero_mistral_tool_use_automatic_2.ipynb`

---

## 📝 Ghi chú

- Repo chứa nhiều notebook thử nghiệm; 2 notebook ưu tiên để tái tạo hệ thống là:
  - `score_training/qwen_3b_test_8.ipynb`
  - `full_inference/test_8_inference_retriever_full_zero_mistral_tool_use_automatic_2.ipynb`
- Nếu chỉ cần demo nhanh, chạy notebook full inference trước.
