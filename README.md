# 🎓 Hệ thống Chatbot AI Hỗ trợ Tuyển sinh (AI Admissions Assistant)

[![Python](https://img.shields.io/badge/Python-3.9+-blue.svg)](https://www.python.org/downloads/)
[![Streamlit](https://img.shields.io/badge/Streamlit-1.30+-red.svg)](https://streamlit.io/)
[![Gemini API](https://img.shields.io/badge/Google%20GenAI-Gemini%202.5%20Flash%20Lite-orange.svg)](https://aistudio.google.com/)

Dự án tham gia **Cuộc thi "Sáng tạo với AI trong giáo dục" năm học 2025 - 2026**. 

Đây là hệ thống Chatbot ứng dụng Trí tuệ nhân tạo tạo sinh (Generative AI) giúp tự động hóa công tác tư vấn tuyển sinh và giải đáp thông tin trường học. Hệ thống hoạt động dựa trên cơ chế cung cấp ngữ cảnh động (tương tự mô hình RAG rút gọn), cho phép nhà trường cá nhân hóa tri thức của AI bằng cách tải lên các văn bản, kế hoạch và quy định nội bộ.

## 🚀 Tính năng cốt lõi

- **Trích xuất tri thức đa định dạng:** Hỗ trợ xử lý và trích xuất văn bản từ nhiều định dạng file: `.pdf`, `.docx`, `.xlsx`, `.pptx`, `.txt`.
- **Tóm tắt & Tiền xử lý dữ liệu:** Gọi API Gemini để tự động tóm tắt và trích xuất các thông tin quan trọng (thời gian, chỉ tiêu, thủ tục) từ tài liệu thô.
- **Cơ chế Human-in-the-loop:** Quản trị viên có quyền xem xét, chỉnh sửa hoặc xóa các bản tóm tắt của AI trước khi đưa vào bộ nhớ tri thức (`Shared Summaries Store`), đảm bảo kiểm soát hoàn toàn "ảo giác" (hallucination) của LLM.
- **Hỏi đáp theo ngữ cảnh (Context-aware Q&A):** Tự động ghép nối lịch sử trò chuyện (Session State) và cơ sở tri thức để tạo Prompt linh hoạt, giúp Chatbot trả lời chính xác, mạch lạc và từ chối trả lời các thông tin không có trong tài liệu.
- **Phân quyền người dùng:** Tích hợp cổng đăng nhập bảo vệ luồng quản trị tài liệu, tách biệt hoàn toàn với giao diện hỏi đáp của người dùng cuối.

## ⚙️ Kiến trúc hệ thống

1. **Frontend/Backend:** Được xây dựng nguyên khối (Monolith) bằng `Streamlit`.
2. **AI Model:** Sử dụng thư viện `google-genai` kết nối với mô hình `gemini-2.5-flash-lite` (tối ưu hóa độ trễ và chi phí).
3. **Data Parsing:** Xử lý cục bộ bằng `python-docx`, `openpyxl`, `python-pptx`. Riêng file PDF được định tuyến qua `Google Files API` để LLM đọc hiểu nguyên bản tốt hơn.
4. **State Management:** Lưu trữ tri thức chia sẻ dạng In-memory (Dict) cho toàn phiên máy chủ và lưu trữ lịch sử chat theo từng phiên trình duyệt (`st.session_state`).

## 💻 Hướng dẫn Cài đặt & Chạy cục bộ (Local Development)

### Yêu cầu môi trường
- Python 3.9 trở lên.
- API Key của Google Gemini (Lấy tại [Google AI Studio](https://aistudio.google.com/)).

### Các bước cài đặt

**Bước 1: Clone kho lưu trữ**
```bash
git clone [https://github.com/your-username/ai-admissions-assistant.git](https://github.com/your-username/ai-admissions-assistant.git)
cd ai-admissions-assistant
```

**Bước 2: Khởi tạo và kích hoạt môi trường ảo (Virtual Environment)**
```bash
python -m venv venv
# Trên Windows:
venv\Scripts\activate
# Trên macOS/Linux:
source venv/bin/activate
```

**Bước 3: Cài đặt thư viện phụ thuộc**
```bash
pip install -r requirements.txt
```
*(Lưu ý: File `requirements.txt` cần bao gồm: `streamlit`, `google-genai`, `python-docx`, `openpyxl`, `python-pptx`)*

**Bước 4: Cấu hình API Key**
Tạo thư mục `.streamlit` ở thư mục gốc của dự án, bên trong tạo file `secrets.toml` và khai báo API Key:
```toml
# File: .streamlit/secrets.toml
GEMINI_API_KEY = "ĐIỀN_API_KEY_CỦA_BẠN_VÀO_ĐÂY"
```

**Bước 5: Khởi chạy ứng dụng**
```bash
streamlit run app.py
```
Trình duyệt sẽ tự động mở ứng dụng tại địa chỉ `http://localhost:8501`.

## 🛠 Hướng dẫn Sử dụng (Dành cho Giám khảo kiểm thử)

1. **Giao diện Người dùng cuối:** Ngay khi truy cập, giám khảo có thể chat trực tiếp với AI. (Lưu ý: Nếu chưa upload tài liệu, AI sẽ chỉ trả lời các thông tin chung dựa trên System Instruction).
2. **Giao diện Quản trị viên (Cập nhật kiến thức):**
   - Tại thanh Sidebar bên trái, nhập thông tin đăng nhập mặc định (Hardcoded phục vụ kiểm thử):
     - **Tên đăng nhập:** `admin`
     - **Mật khẩu:** `admin`
   - Nhấn **Đăng nhập**. Giao diện Quản lý tài liệu sẽ hiện ra.
   - Tải lên các file tài liệu đính kèm (VD: Kế hoạch tuyển sinh.docx).
   - Đợi hệ thống tóm tắt, sau đó mở mục **📋 Nội dung**, kiểm tra lại văn bản tóm tắt và nhấn **💾 Lưu**.
   - Quay lại màn hình Chat chính và đặt câu hỏi để kiểm thử độ chính xác.

## 🌍 Triển khai (Deployment)

Dự án được tối ưu để triển khai nhanh chóng (One-click deploy) trên nền tảng **Streamlit Community Cloud**:
1. Đẩy mã nguồn dự án lên GitHub.
2. Truy cập [share.streamlit.io](https://share.streamlit.io/) và đăng nhập bằng GitHub.
3. Chọn **Create app** -> Trỏ tới kho lưu trữ GitHub của dự án.
4. Vào mục **Advanced settings** -> **Secrets**, dán cấu hình `GEMINI_API_KEY = "..."` vào.
5. Nhấn **Deploy**. Hệ thống sẽ tự động build và cung cấp public URL trong vòng 2 phút.

## 📝 Bản quyền và Cam kết
- Mã nguồn được phát triển phục vụ Cuộc thi "Sáng tạo với AI trong giáo dục 2025-2026".
- Giải pháp tuân thủ các nguyên tắc về Đạo đức AI, không lưu trữ trái phép dữ liệu cá nhân của người dùng (lịch sử chat tự hủy khi kết thúc phiên).
```
