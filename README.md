# IELTS Writing Task 2 Evaluation

Kho lưu trữ này phục vụ nghiên cứu và demo hệ thống **chấm điểm bài viết IELTS Writing Task 2** bằng nhiều hướng tiếp cận:
- Supervised learning (TF-IDF, BiLSTM, Longformer, DeBERTa, v.v.).
- Zero-shot LLM grading trên bộ ASAP/ASAP++.
- Pipeline inference kết hợp **scoring + retrieval + feedback generation**.

## 1) Mục tiêu dự án

- Dự đoán điểm viết theo các tiêu chí (TR/CC/LR/GRA và overall band).
- So sánh nhiều mô hình/thiết lập huấn luyện trên dữ liệu học thuật.
- Thử nghiệm pipeline phản hồi tự động có grounding bằng retrieval.

## 2) Cấu trúc thư mục chính

- `CS338_DemoToolUse/`
  - `score_training/`: notebook huấn luyện mô hình chấm điểm.
  - `score_inference/`: notebook suy luận điểm (scoring-only).
  - `full_inference/`: pipeline đầy đủ có retrieval và feedback.
  - `baseline/`: các baseline backbone khác nhau.
  - `feature_engineering.ipynb`, `data_aug.ipynb`, `eda_*.ipynb`: tiền xử lý và EDA.

- `dataset/`
  - `hugging_face_dataset/`: train/val/test cho bài toán IELTS.
  - `augmented_dataset/`, `augement_dataset_new_collected/`: dữ liệu tăng cường.
  - `asap/`, `asap++/`: dữ liệu ASAP phục vụ benchmark.
  - `full_concat_ds/`: dữ liệu hợp nhất.

- `benchmark_asap/`
  - `supervised_learning/`: notebook benchmark mô hình học có giám sát.
  - `zeroshot_learning/`: notebook benchmark zero-shot (LCES, MTS, vanilla).

- `benchmark_results/ASAP/`
  - `Supervised_learning/`: kết quả dự đoán + metrics cho supervised.
  - `ZeroShot/lces/`, `ZeroShot/mts/`: kết quả benchmark zero-shot.

- `references/`: tài liệu/paper tham khảo.
- `ASAP/`, `ASAP++/`: notebook xử lý và thực nghiệm theo prompt/dataset.

## 3) Notebook nên bắt đầu

Nếu bạn muốn chạy nhanh theo lộ trình demo hiện tại:

1. **Huấn luyện điểm số**
   - `CS338_DemoToolUse/score_training/qwen_3b_test_8.ipynb`

2. **Suy luận chấm điểm**
   - `CS338_DemoToolUse/score_inference/test_7_inference.ipynb`

3. **Pipeline đầy đủ (khuyến nghị)**
   - `CS338_DemoToolUse/full_inference/test_7_inference_retriever_full_zero_mistral_tool_use_automatic_16_best_LANGGRAPH_A100.ipynb`

Ngoài ra có các biến thể trong cùng thư mục `full_inference/` như bản `..._14.ipynb`, `..._15_LANGGRAPH.ipynb`, `..._17_best_LANGGRAPH_A100.ipynb` để đối chiếu cấu hình.

## 4) Benchmark ASAP/ASAP++

- Supervised experiments: mở notebook trong `benchmark_asap/supervised_learning/`.
- Zero-shot experiments: mở notebook trong `benchmark_asap/zeroshot_learning/`.
- Kết quả mẫu đã lưu ở `benchmark_results/ASAP/` để tái lập báo cáo.

## 5) Yêu cầu môi trường (gợi ý)

Vì dự án chủ yếu là notebook, bạn nên chuẩn bị:

- Python 3.10+.
- CUDA GPU (nếu chạy model lớn như Qwen/Mistral).
- JupyterLab hoặc Google Colab.
- Các thư viện thường dùng trong notebook: `torch`, `transformers`, `peft`, `datasets`, `scikit-learn`, `pandas`, `numpy`, `sentence-transformers`, `gradio`, `langgraph`.

> Lưu ý: mỗi notebook có thể có cell cài dependency riêng. Hãy đọc và chạy tuần tự từ đầu notebook để tránh thiếu package hoặc sai phiên bản.

## 6) Dữ liệu và tái lập kết quả

- Một số tệp dữ liệu đã được đưa sẵn trong `dataset/`.
- Kết quả benchmark CSV đã có trong `benchmark_results/` để kiểm tra lại thống kê mà không cần chạy toàn bộ training.
- Khi thay đổi pipeline, nên lưu thêm file kết quả mới thay vì ghi đè để tiện so sánh.

## 7) Định hướng sử dụng nhanh

- Chỉ cần chấm điểm: dùng notebook trong `score_inference/`.
- Cần phản hồi chi tiết có retrieval: dùng notebook trong `full_inference/`.
- Cần đánh giá học thuật theo ASAP/ASAP++: dùng `benchmark_asap/` + `benchmark_results/`.

---

Nếu bạn muốn, mình có thể tiếp tục làm bước 2: bổ sung một file `requirements.txt` tối thiểu để chạy được luồng demo phổ biến nhất (score inference + full inference).
