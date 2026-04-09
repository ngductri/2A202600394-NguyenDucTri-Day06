# Individual reflection — Nguyê ̃n Văn A (AI20K001)
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

## 4. Đóng góp khác- Test prompt với 10 triệu chứng khác nhau, ghi log kê ́t quả vào 
prompt-tests.md- Giúp Châu debug eval metrics — ban đâ ̀u chi ̉ có "accuracy" chung,
sau tách ra precision cho từng khoa
## 5. Điê ̀u học được
Trước hackathon nghĩ precision và recall chi ̉ là metric kỹ thuật.
Sau khi thiê ́t kê ́ AI triage mới hiê ̉u: chọn recall cao hơn cho khoa 
câ ́p cứu
(bỏ sót nguy hiê ̉m hơn false alarm) nhưng precision cao hơn cho khoa 
chuyên sâu
(gợi ý sai gây lãng phí thời gian bệnh nhân). Metric là product 
decision,
không chi ̉ engineering decision.
## 6. Nê ́u làm lại
Sẽ test prompt sớm hơn — ngày đâ ̀u chi ̉ viê ́t SPEC, đê ́n trưa D6 mới 
bắt đâ ̀u test prompt.
Nê ́u test sớm từ tô ́i D5 thì có thê ̉ iterate thêm 2-3 vòng, prompt sẽ 
tô ́t hơn nhiê ̀u.
## 7. AI giúp gì / AI sai gì- **Giúp:** dùng Claude đê ̉ brainstorm failure modes — nó gợi ý 
được "drug interaction"
mà nhóm không nghĩ ra. Dùng Gemini đê ̉ test prompt nhanh qua AI 
Studio.- **Sai/mislead:** Claude gợi ý thêm feature "đặt lịch khám" vào 
chatbot —
nghe hay nhưng scope quá lớn cho hackathon. Suýt bị scope creep 
nê ́u không dừng lại.
Bài học: AI brainstorm tô ́t nhưng không biê ́t giới hạn scope