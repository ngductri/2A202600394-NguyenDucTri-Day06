# Individual reflection — NGUYỄN ĐỨC TRÍ (2A202600394)
## 1. Role
Thiết kế chatbot và thiết lập agent.
## 2. Đóng góp cụ thể
Tôi đã trực tiếp xây dựng hệ thống AI Agent đặt xe thông minh tích hợp đa phương thức, sử dụng mô hình Gemini API kết hợp cùng framework LangGraph để quản lý luồng hội thoại dưới dạng đồ thị trạng thái (State Machine). Tôi đã thiết kế cấu trúc System Prompt tối ưu theo định dạng chuẩn (<persona>, <rules>, <tools_instruction>, <constraints>), giúp Agent có khả năng suy luận logic và thực hiện Function Calling chính xác. Điểm nhấn kỹ thuật nằm ở việc triển khai các vòng lặp điều kiện tự động (validation loops), buộc Agent phải trích xuất thực thể và kiểm tra tính đầy đủ của dữ liệu đầu vào (điểm đi/điểm đến) trước khi kích hoạt công cụ tra cứu. Output của em sẽ format theo {user_id,origin,destination} 
## 3. SPEC mạnh/yê ́u- Mạnh nhâ ́t: failure modes — nhóm nghĩ ra được case "triệu chứng 
1. Điểm mạnh nhất: Kiến trúc hệ thống và Tư duy xử lý dữ liệu
Điểm sáng nhất nằm ở cách bạn phân lớp (Layering) cực kỳ khoa học giữa AI Agent (The Interpreter) và Backend (The Orchestrator). Việc sử dụng LLM như Gemini chỉ để "bóc tách thực thể và chuẩn hóa JSON" thay vì phó mặc toàn bộ logic cho AI là một hướng đi rất chuyên nghiệp, giúp hệ thống ổn định và dễ kiểm soát (Deterministic). Bên cạnh đó, việc kết hợp linh hoạt giữa dữ liệu tĩnh (users.json cho Home/Work) và dữ liệu động (Geopy/OSRM cho các địa điểm lạ) cho thấy tư duy tối ưu hóa tài nguyên: ưu tiên dùng dữ liệu có sẵn để tăng tốc độ phản hồi và chỉ gọi API bên ngoài khi thực sự cần thiết.

2. Điểm yếu nhất: Trải nghiệm tương tác và Xử lý độ trễ (Latency)
Điểm yếu lớn nhất nằm ở luồng phản hồi (Feedback Loop) và tốc độ xử lý thực tế. Chuỗi xử lý từ Whisper (STT) -> Gemini (NLU) -> Geopy (Geocoding) -> OSRM (Routing) -> App là một quy trình rất nặng, có thể gây độ trễ từ 3-5 giây, làm mất đi cảm giác "siêu tốc" mà bạn kỳ vọng. Ngoài ra, spec đang quá tập trung vào "Happy Path" (khi người dùng nói đủ thông tin) mà thiếu đi cơ chế Slot-filling (AI hỏi lại khi thiếu điểm đến hoặc loại xe) và thiếu thành phần TTS (Text-to-Speech) để phản hồi bằng giọng nói, khiến trải nghiệm "Assistant" bị nửa vời khi người dùng vẫn phải nhìn và chạm vào màn hình quá nhiều.

3. Đánh giá chi tiết các thành phần kỹ thuật
Về mặt công nghệ, việc lựa chọn FastAPI và Expo là sự kết hợp hiện đại, phù hợp cho một prototype cần tốc độ phát triển nhanh. Hệ thống Mapping (Geopy + OSRM) là một lựa chọn thông minh để thay thế các dịch vụ trả phí đắt đỏ như Google Maps trong giai đoạn thử nghiệm, dù độ chính xác của địa chỉ tại Việt Nam có thể là một thách thức. Phần Data giả lập (users.json, services.json) được tổ chức tốt, giúp tách biệt dữ liệu người dùng và bảng giá dịch vụ, tạo điều kiện thuận lợi cho việc mở rộng quy mô hoặc tích hợp database thật sau này. Tuy nhiên, phần AI Agent cần được bổ sung một System Prompt chặt chẽ để đảm bảo kết quả JSON trả về luôn đồng nhất, tránh làm gãy logic của Backend.

## 4. Đóng góp khác
- Test Mô hình Agent với nhiều cases khác nhau vào prompt_test.md
- Support kiểm thử và thực nghiệm các mô hình Speech to text như là Gemini
- Debug phần connect giữa BackEnd và Agent.

## 5. Điều học được sau buổi Hackathon
Điều giá trị nhất mà em gặt hái được sau buổi Hackathon vừa qua chính là kỹ năng làm việc nhóm và tư duy hệ thống trong một dự án thực tế.

Là một người mới bắt đầu và chưa có nhiều kinh nghiệm 'thực chiến', ban đầu em còn khá bỡ ngỡ với quy trình phát triển sản phẩm (Production Flow). Em từng gặp khó khăn trong việc định hình rõ ràng các thành phần: từ việc xác định dữ liệu đầu vào (input), định dạng đầu ra (output) cho đến cách kết nối nhịp nhàng giữa Backend và Frontend.

Tuy nhiên, quá trình xây dựng AI Agent tại Hackathon đã giúp em thay đổi hoàn toàn tư duy. Em nhận ra rằng để phát triển một hệ thống End-to-End hoàn chỉnh, một kỹ sư AI không chỉ tập trung vào mô hình mà còn cần có kiến thức nền tảng về cả Backend lẫn Frontend. Điều này giúp em biết cách thiết kế format dữ liệu chuẩn xác để Agent có thể tiếp nhận thông tin từ hệ thống và phản hồi giao diện một cách tối ưu nhất.

Đặc biệt, đây cũng là cơ hội tuyệt vời để em áp dụng các kiến thức chuyên sâu đã học như:
LangGraph: Để thiết kế các luồng xử lý phức tạp.
Function Calling & Tool Calling: Giúp Agent có khả năng tương tác với các công cụ bên ngoài.
Vòng lặp (Looping mechanism): Đảm bảo Agent thực hiện tác vụ liên tục cho đến khi thỏa mãn điều kiện đầu ra.
Vượt qua những bối rối ban đầu, buổi Hackathon đã giúp em kết nối các 'mảnh ghép' kiến thức rời rạc thành một bức tranh toàn cảnh về quy trình xây dựng sản phẩm thực tế.

## 6. Nếu làm lại, đổi gì ?
Nếu được làm lại em mong muốn xây dựng 1 system đơn giản hơn thay vì phải sử dụng backend, frontend bằng cách sử dụng Streamlit. Tại vì em nhận thấy nhóm em chưa có kinh nghiệm nhiều để xử lý việc kết nối giữa FrontEnd -> Agent -> Backend làm cho công việc của BackEnd trở nên nặng nề hơn nhiều nên là tụi em sẽ sửa chữa bằng cách sử dụng một thư việc nào đó để giảm gánh nặng xử lý hơn. Ngoài ra em mong muốn sẽ có thời gian để thực nghiệm và research nhiều mô hình speech to text và agent tốt hơn cho ra kết quả tốt hơn không bị ảo giác hay bị sai kết quả nữa.
## 7. AI giúp gì / AI sai gì
AI giúp rất nhiều:
- Giúp việc code trở nên nhanh hơn và có thể chính xác hơn và dễ dàng hơn.
- Nâng cao và mở rộng tư duy vận hành một end-to-end system.
- Define vấn đề và debate mindset.

AI sai gì:
- Generate code và tự động xóa sửa code nếu không biết prompt đúng làm mất logic code ban đầu
- AI có thể khẳng định đã thực hiện xong một tác vụ ở Backend (như "Đã đặt hàng thành công") trong khi thực tế Tool đó vừa trả về lỗi. (Nếu không bắt lỗi "error handling" kỹ).
- Sau một vòng lặp dài, AI có thể quên mất mục tiêu ban đầu của người dùng và đưa ra câu trả lời đi chệch hướng.
- Làm cho người dùng không hiểu code, lạm dụng AI. Ví dụ một lỗi sơ đẳng không tìm thấy path của file system_prompt.txt lại bị AI sửa quá lên làm sai lại càng sai, bug lại càng nhiều bug.
