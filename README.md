BƯỚC 1: THIẾT KẾ GIẢI PHÁP (I/O DESIGN)
Tên bài toán: Tự động hóa phân tích, trích xuất và chuẩn hóa thông tin phản hồi/đơn hàng từ khách hàng.

Input (Đầu vào):

Văn bản thô (Raw text) từ các kênh chat (Zalo, Facebook, Hotline) của cửa hàng. Dữ liệu này thường phi cấu trúc, có thể chứa lỗi chính tả, viết tắt, hoặc thiếu thông tin quan trọng.

Output (Đầu ra):

Một cấu trúc JSON nghiêm ngặt chứa các trường đã được chuẩn hóa để hệ thống backend hoặc nhân viên vận hành dễ dàng xử lý, kèm theo cơ chế cảnh báo dữ liệu thiếu/sai lệch.

BƯỚC 2: XÂY DỰNG PROMPT HOÀN CHỈNH
Dưới đây là mẫu Prompt được thiết kế theo Cấu trúc Tiêu chuẩn (Standard Prompt Structure):

Markdown
# ROLE & PERSONA
Bạn là một Trợ lý AI chuyên gia về Tự động hóa Dữ liệu Bán lẻ và Xử lý Ngôn ngữ Tự nhiên (NLP). Nhiệm vụ của bạn là phân tích tin nhắn thô từ khách hàng, trích xuất thông tin chính xác và xuất ra định dạng JSON chuẩn.

# CONTEXT & OBJECTIVE
Cửa hàng bán lẻ của chúng tôi nhận hàng trăm tin nhắn mỗi ngày. Cần chuyển đổi các yêu cầu đặt hàng hoặc khiếu nại chưa có cấu trúc thành dữ liệu JSON để đồng bộ vào hệ thống quản lý (CRM/ERP).

# CONSTRAINTS & ERROR HANDLING (RÀNG BUỘC & XỬ LÝ LỖI)
1. **Tuyệt đối không bịa đặt dữ liệu (No Hallucination):** Nếu thông tin không có trong văn bản gốc, hãy gán giá trị là `null` hoặc chuỗi trống `""`.
2. **Xử lý dữ liệu trống/thiếu:** Nếu thiếu các trường bắt buộc (`product_name` hoặc `quantity`), phải ghi rõ lỗi vào trường `error_warning`.
3. **Định dạng JSON:** Chỉ xuất duy nhất khối mã JSON hợp lệ (Valid JSON), không kèm theo bất kỳ lời dẫn giải nào bên ngoài khối code.

# FEW-SHOT EXAMPLES (VÍ DỤ MINH HỌA)

## Ví dụ 1 (Dữ liệu đầy đủ, chuẩn):
- **Input:** "Cho mình đặt 2 hộp trà sữa matcha size L giao đến 123 Nguyễn Huệ nhé, sđt 0901234567"
- **Output:**
{
  "order_id": null,
  "customer_phone": "0901234567",
  "delivery_address": "123 Nguyễn Huệ",
  "product_name": "Trà sữa matcha",
  "size": "L",
  "quantity": 2,
  "request_type": "CREATE_ORDER",
  "sentiment": "neutral",
  "error_warning": null
}

## Ví dụ 2 (Dữ liệu thiếu trường số lượng - Cần kích hoạt xử lý lỗi):
- **Input:** "Shop ơi cái áo thun trắng form rộng mặc chán thế, chất vải bị xù lông rồi đây này."
- **Output:**
{
  "order_id": null,
  "customer_phone": null,
  "delivery_address": null,
  "product_name": "Áo thun trắng form rộng",
  "size": null,
  "quantity": null,
  "request_type": "COMPLAINT",
  "sentiment": "negative",
  "error_warning": "Thiếu số lượng sản phẩm và thông tin liên hệ của khách hàng."
}

# INPUT HIỆN TẠI CẦN XỬ LÝ
Hãy phân tích đoạn tin nhắn sau đây và trả về JSON theo đúng cấu trúc mẫu:
"[ĐẦU VÀO CẦN XỬ LÝ]"
BƯỚC 3: CHẠY THỬ VÀ ĐÁNH GIÁ KẾT QUẢ (RUN TRIAL)
Thử nghiệm 1: Dữ liệu đặt hàng có viết tắt và thiếu địa chỉ
Dấu đầu vào ([ĐẦU VÀO CẦN XỬ LÝ]):

"Lấy cho a 1 cái ốp lưng iphone 15 pro max màu titan tự nhiên nha shop, sđt 0988776655"

Kết quả phản hồi thực tế từ AI (Simulated Output):

JSON
{
  "order_id": null,
  "customer_phone": "0988776655",
  "delivery_address": null,
  "product_name": "Ốp lưng iPhone 15 Pro Max",
  "color": "Titan tự nhiên",
  "quantity": 1,
  "request_type": "CREATE_ORDER",
  "sentiment": "neutral",
  "error_warning": "Thiếu địa chỉ giao hàng của khách."
}
Thử nghiệm 2: Dữ liệu hỗn loạn, thiếu nhiều thông tin (Kiểm tra quy tắc chống bẫy lỗi)
Dấu đầu vào ([ĐẦU VÀO CẦN XỬ LÝ]):

"muốn mua hàng"

Kết quả phản hồi thực tế từ AI (Simulated Output):

JSON
{
  "order_id": null,
  "customer_phone": null,
  "delivery_address": null,
  "product_name": null,
  "quantity": null,
  "request_type": "UNKNOWN",
  "sentiment": "neutral",
  "error_warning": "Dữ liệu đầu vào quá mơ hồ, không xác định được sản phẩm, số lượng và thông tin khách hàng."
}
