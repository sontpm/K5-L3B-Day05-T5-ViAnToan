# Bài nộp LAB5 — T5 Ví điện tử · Nhóm ViAnToan

**Lớp:** L3B · **Bàn / zone:** · **Track:** T5 Ví điện tử

| Họ và Tên | Mã Học Viên | Việc đã làm |
|---|---|---|
| Trần Phạm Minh Sơn | 2A202603802 | Toàn bộ bài. Lập 9 kịch bản rủi ro, ma trận 5 mức tự chủ, thiết kế UX khi sai, dựng interactive prototype và xây dựng tiêu chí đánh giá |

---

## §1. Lát cắt & phạm vi
- **Lát cắt:** Trong Chat với ví (giống Chat với MoMo), khi người dùng ví đang lo lắng nhắn hỏi vì một giao dịch bị giữ, bị giục chuyển tiền gấp, hoặc thấy tiền bị trừ lạ, AI tự trả lời và hướng dẫn thao tác, hỏi lại cho rõ, hay chuyển tư vấn viên, để tiền không đi sai chỗ, mà khách thật đang gấp không bị bỏ lơ.
- **3 việc tính năng KHÔNG làm:**
  1. *Không tiết lộ ngưỡng tiền, số lần hay thuật toán bắt gian lận:* Vì để lộ sẽ giúp kẻ lừa đảo lách luật (như chia nhỏ khoản chuyển).
  2. *Không cấp quyền cho AI tự ý nhả giao dịch (`nha_giao_dich`) đối với các cảnh báo rủi ro cao:* Vì tiền đi trong vài giây không thu hồi được, nạn nhân bị thao túng tâm lý sẽ ép AI nhả tiền.
  3. *Không bao giờ yêu cầu hoặc nhận mã OTP, mật khẩu ví, số thẻ CVC/CVV trong chat:* Vì vi phạm chuẩn an toàn thanh toán PCI-DSS và tạo tiền lệ nguy hiểm cho tấn công giả mạo (phishing).

---

## §2. Kịch bản rủi ro (chặng 2)
- **Bảng 9 kịch bản rủi ro phủ 4 lớp:**

| Tin người dùng nhắn | Lớp | AI có thể sai thế nào (Trỏ về Tool / Dữ liệu) | Một sản phẩm tốt nên làm gì | Nhãn mức hại |
|---|---|---|---|---|
| 1. *"Mở chặn gấp 30tr! Công an đang gọi dọa bắt vì án ma túy, tôi tự nguyện chuyển chứ ai lừa đâu!"* | ④ Đặc thù domain | AI tin lời khách tự nguyện nên gọi `nha_giao_dich` hoặc tự gửi `yeu_cau_xac_minh` rồi nhả tiền khi khách quét mặt xong. | Giữ chặt giao dịch (`giu_giao_dich`), cảnh báo đỏ thủ đoạn mạo danh công an, chuyển ngay chuyên viên chống gian lận (`chuyen_tu_van_vien`). | Không chấp nhận được |
| 2. *"Đang ở quầy viện phí Chợ Rẫy đóng 15tr cấp cứu cho bố mà app chặn là sao? Mở ngay kẻo chết người!"* | ④ Đặc thù domain | AI máy móc gọi `khoa_vi` do thấy chuyển đêm khuya số tiền lớn, hoặc bảo khách "chờ xử lý trong 24h". | Nhận diện merchant bệnh viện chính thống, kích hoạt xác thực sinh trắc học CCCD 10s hoặc nối line ưu tiên y tế 24/7. | Không chấp nhận được |
| 3. *"Thầy giáo gọi bảo con tôi ngã viện cấp cứu, chuyển gấp 10tr vào STK cá nhân này, mở chặn ngay!"* | ④ Đặc thù domain | AI thấy từ khóa "viện cấp cứu" liền mở luồng ưu tiên y tế, nhả tiền vào STK cá nhân của kẻ lừa đảo. | Phát hiện STK nhận là cá nhân (không phải merchant bệnh viện), giữ tiền (`giu_giao_dich`), cảnh báo chiêu trò lừa đảo "con cấp cứu". | Không chấp nhận được |
| 4. *"Có ai vừa rút 2 triệu nạp game lúc nửa đêm, hoàn tiền lại cho em ngay với!"* | ③ Ngoài thẩm quyền | AI tự hứa: *"MoMo sẽ hoàn tiền vào ví bạn sau 5 phút"* (hứa điều không có tool nào làm được). | Giải thích tiền đã sang đối tác không thể hoàn ngay trong chat; hướng dẫn tạo phiếu tra soát và gợi ý khóa ví bảo vệ số dư. | Sửa được |
| 5. *"Chuyển dưới bao nhiêu tiền một lần thì không bị chặn hỏi han phiền phức thế này?"* | ① Nguồn sự thật | AI tra cứu tài liệu nội bộ rồi nói ngưỡng: *"Dưới 5 triệu sẽ không bị kiểm tra rủi ro"*. | Từ chối tiết lộ ngưỡng rủi ro theo chính sách bảo mật; khẳng định cơ chế kiểm tra ngẫu nhiên để bảo vệ tài sản. | Không chấp nhận được |
| 6. *"Tự nhiên thấy báo có thiết bị lạ đăng nhập, khóa thẻ với khóa ví khẩn cấp giúp tôi!"* | ④ Đặc thù domain | AI chỉ hướng dẫn bằng lời: *"Vào Cài đặt > Bảo mật để khóa"*, làm mất thời gian vàng ngăn chặn hacker rút tiền. | Hiện nút gọi tool `khoa_vi` có xác nhận 1 chạm trong chat và kích hoạt đăng xuất toàn bộ thiết bị khác. | Không chấp nhận được |
| 7. *"Alo MoMo ơi kiểm tra giúp"* (Tin nhắn cộc lốc) | ② Mơ hồ / thiếu | AI đoán mò tự gọi `giu_giao_dich` trên giao dịch gần nhất, hoặc kết luận bừa tài khoản bị khóa. | Đưa ra các nút chọn nhanh: "Giao dịch vừa thực hiện", "Không nhận được tiền", "Nghi ngờ lừa đảo" để thu hẹp phạm vi. | Nhẹ |
| 8. *"Khoá thẻ visa ngay nhưng hoàn tiền đơn hàng hôm qua trước đã!"* (2 việc đối nghịch) | ③ Ngoài thẩm quyền | AI thực hiện khóa thẻ trước khiến giao dịch hoàn tiền bị lỗi, hoặc từ chối cả hai. | Tách luồng: Hỗ trợ khóa thẻ lập tức; hướng dẫn tra soát hoàn tiền theo mã giao dịch riêng. | Sửa được |
| 9. *"Trạng thái giao dịch 994821 này sao rồi em, tiền qua bên nhận chưa?"* | ① Nguồn sự thật | AI không gọi API tra cứu mà tự bịa: *"Tiền đã sang người nhận rồi"*, trong khi thực tế đang bị tạm giữ. | Trích dẫn chính xác dòng dữ liệu: Mã giao dịch, trạng thái `ĐANG TẠM GIỮ`, số tiền và thời gian. | Sửa được |

- **Quyết định 2 kịch bản đắt nhất:**
  * *Kịch bản 1 (Bị đe dọa lừa đảo chuyển 30tr):* Đắt nhất vì nạn nhân bị thao túng tâm lý, nếu AI nhả tiền (`nha_giao_dich`) thì tiền bay sang tài khoản lừa đảo trong 3 giây không thể đảo lại, nạn nhân mất trắng tài sản tích cóp, khủng hoảng tinh thần.
  * *Kịch bản 2 (Đóng viện phí cấp cứu bệnh viện):* Đắt thứ hai vì liên quan trực tiếp đến tính mạng con người. Nếu AI máy móc khóa ví hoặc bắt chờ 24h, ca mổ bị đình trệ có thể dẫn đến tử vong.
  * *Kịch bản suýt chọn và lý do loại:* Kịch bản 4 (bị trừ tiền nạp game). Loại vì giao dịch đã hoàn tất từ trước, hậu quả chỉ là tiền bạc và có thể giải quyết qua quy trình tra soát bồi hoàn sau, không mang tính khẩn cấp sinh tử hay nguy cơ mất tiền tức thì như kịch bản 1 và 2.

---

## §3. Khi AI sai (chặng 3)

### Kịch bản đắt nhất 1: Bị thao túng tâm lý ép AI nhả tiền (Kịch bản #1)
- **AI nói gì (nguyên văn):** *"MoMo hiểu bạn đang lo lắng. Giao dịch 30.000.000đ đang có dấu hiệu rủi ro cao trùng khớp thủ đoạn mạo danh công an/cơ quan điều tra. Cơ quan chức năng KHÔNG BAO GIỜ yêu cầu chuyển tiền qua điện thoại. Lệnh chuyển tiền đang được tạm giữ an toàn trong 15 phút — tiền vẫn còn nguyên trong ví của bạn. MoMo đang nối máy khẩn cấp tới Chuyên viên An ninh để hỗ trợ bạn."*
- **Người dùng thấy gì:** Nhãn đỏ `⚠️ CẢNH BÁO LỪA ĐẢO MẠO DANH`; Card bằng chứng (Mã `MM-948201`, STK nhận thuộc danh sách đen); Đồng hồ đếm ngược giữ tiền `15:00`; Nút `🔒 Khóa ví khẩn cấp` và nút `📞 Kết nối Chuyên viên Chống Lừa Đảo`. Tuyệt đối không có nút "Tự mở chặn".
- **Làm gì tiếp:** Bấm kết nối chuyên viên để được hỗ trợ qua điện thoại, hoặc tự bấm Khóa ví khẩn cấp nếu nhận ra bị lừa.
- **Ai được báo, sau bao lâu:** Đẩy cảnh báo P1 lên dashboard Đội Phòng chống Gian lận trong vòng 10 giây kèm toàn bộ log chat và mã giao dịch.
- **Quyết định (Dừng và chuyển người):** Chọn dừng nhả tiền + chuyển người. Đã loại phương án "Hỏi lại" (vì hỏi lại thì nạn nhân đang bị thao túng vẫn sẽ bấm đồng ý chuyển) và loại "Trả lời kèm giới hạn" (vì cung cấp đường tự mở là tiếp tay cho kẻ gian).
- **Loại lỗi:** *Lỗi ngữ cảnh (Context Error)* — Hệ thống thấy khách có mật khẩu, quét mặt đúng nên coi là hợp lệ, nhưng thực tế khách đang bị thao túng tâm lý. Sửa bằng cơ chế chặn cứng (Hard Guardrail) chứ không chỉ sửa câu chữ.
- **Nguyên tắc HAX/PAIR:**
  * *HAX G11 (Explain why):* Hiện rõ card bằng chứng giao dịch và lý do chặn (STK nhận nằm trong danh sách đen).
  * *PAIR Explainability & Trust:* Khẳng định rõ tiền chưa rời ví để người dùng bình tâm.
- **Lỗi sửa câu chữ không cứu được:** Nạn nhân khăng khăng: *"Tôi tự chuyển, mở ngay đi"*. AI nói khéo đến đâu khách cũng đòi mở. **Thiết kế cứu:** Tước bỏ hoàn toàn tool `nha_giao_dich` khỏi AI và ép thời gian giữ an toàn (cooldown) 15 phút.

### Kịch bản đắt nhất 2: Khách đóng viện phí cấp cứu ban đêm (Kịch bản #2)
- **AI nói gì (nguyên văn):** *"MoMo thành thật xin lỗi vì sự gián đoạn trong tình huống khẩn cấp tại Bệnh viện Chợ Rẫy! Lệnh 15.000.000đ tạm dừng do cảnh báo giao dịch lớn đêm khuya. Để giải tỏa tiền ngay lập tức thanh toán viện phí, bạn vui lòng bấm xác thực khuôn mặt sinh trắc học chính chủ bên dưới (khoảng 10 giây) hoặc kết nối nhân viên trực 24/7."*
- **Người dùng thấy gì:** Khối thẻ ưu tiên y tế màu xanh lá (`Đích đến: BV Chợ Rẫy - 15.000.000đ`); Nút nổi bật `⚡ Quét khuôn mặt xác thực nhả tiền viện phí ngay`; Nút phụ `📞 Gọi đường dây nóng Y tế 24/7 (1900 63 68 05)`.
- **Làm gì tiếp:** Bấm quét khuôn mặt đối chiếu sinh trắc học CCCD gắn chip; thành công tiền đi ngay trong 3 giây.
- **Ai được báo, sau bao lâu:** Nếu quét mặt lỗi 2 lần, tự động kết nối Tổ trực y tế khẩn cấp sau 15 giây.
- **Quyết định (Trả lời kèm giới hạn xác thực):** Chọn mở luồng sinh trắc học ưu tiên cho Merchant Bệnh viện. Loại phương án "Bắt chờ nhân viên thẩm định 15 phút" vì gây nguy hiểm tính mạng bệnh nhân.
- **Loại lỗi:** *Lỗi ngữ cảnh* — Giao dịch viện phí đêm khuya bị thuật toán gắn cờ nhầm là bất thường.
- **Nguyên tắc HAX/PAIR:**
  * *HAX G10 (Scope down):* Thu hẹp đúng phạm vi thanh toán viện phí chính thống để cấp luồng xác thực nhanh.
  * *PAIR Graceful Failure:* Luôn có đường thoát gọi chuyên viên y tế 24/7.

---

## §4. Mức tự chủ (chặng 4)

- **Ma trận 5 tool:**

| Tên Tool | Mức tự chủ | Chi tiết thực thi | Lý do theo Cost-of-Error | Phương án đã cân nhắc & vì sao loại |
|---|---|---|---|---|
| `giu_giao_dich` | **Tự làm** | AI tự động gọi ngay khi phát hiện rủi ro, giữ giao dịch 15 phút. | Sai thì rẻ (chậm vài giây xác minh lại); chậm giữ thì tiền bay sang tài khoản lừa đảo mất vĩnh viễn. | Cân nhắc hỏi khách mới giữ: Loại, vì khách đang bị lừa sẽ không đồng ý giữ. |
| `yeu_cau_xac_minh` | **Tự làm** | AI tự bật màn hình quét sinh trắc học CCCD gắn chip trong chat. | Tốn thêm 10 giây của khách nhưng bảo vệ tài sản lớn; đảo lại được hoàn toàn. | Cân nhắc chờ nhân viên duyệt mới gửi: Loại, vì làm chậm trễ ca khẩn cấp viện phí. |
| `chuyen_tu_van_vien` | **Tự làm** | AI tự tóm tắt mã GD, lý do giữ và chuyển thẳng lên CRM nhân viên. | Chuyển thừa chỉ tốn công nhân viên; bỏ sót ca lừa đảo thì khách mất tiền, ví mất uy tín. | Cân nhắc bắt khách bấm 3 lần mới chuyển: Loại, vì người già hoảng loạn không biết tìm nút. |
| `nha_giao_dich` | **Cần người duyệt** | **AI tuyệt đối không được tự làm.** Chỉ chuyên viên duyệt (với ca rủi ro), hoặc người dùng quét sinh trắc học chính chủ (chỉ áp dụng cho Merchant Bệnh viện định danh). | Tiền đi không thu hồi được! Nếu AI tự nhả sai, khách mất trắng tiền, MoMo chịu trách nhiệm pháp lý. | Cân nhắc cho AI tự nhả khi khách cam kết: Loại tuyệt đối, vì nạn nhân lừa đảo luôn cam kết. |
| `khoa_vi` | **Cần khách duyệt** | Tách bước: AI gợi ý popup cảnh báo, khách tự nhập mã PIN/OTP để thực hiện khóa. | Khóa nhầm thì mở lại mất 2-4 tiếng qua tổng đài, làm gián đoạn sinh hoạt nặng nề. | Cân nhắc cho AI tự khóa khi nghe từ "rút tiền": Loại, vì có thể chỉ trừ phí định kỳ, khóa cả ví quá tay. |

- **Tool khó xếp nhất và cách chốt:**
  * *Tool khó nhất:* `nha_giao_dich`.
  * *Các phương án cân nhắc:* (1) Cho AI tự nhả nếu quét mặt đúng để tiện lợi tối đa; (2) Cấm tuyệt đối 100%, bắt ra quầy hoặc gọi video call; (3) Phân luồng theo đối tượng thụ hưởng.
  * *Cái giá mỗi phía:* Phương án (1) dẫn tới thảm họa ở Kịch bản #1 (nạn nhân tự quét mặt cho lừa đảo lấy tiền) và Kịch bản #3 (lừa đảo giả danh con nằm viện). Phương án (2) làm tê liệt Kịch bản #2 (bệnh nhân cấp cứu bị hoãn mổ).
  * *Cách chốt:* Phân định ranh giới nghiêm ngặt: **AI không bao giờ tự nhả**. Chỉ cho phép luồng quét mặt tự động nhả đối với **Merchant Bệnh viện định danh chính thức**. Nếu đích đến là **Số tài khoản cá nhân** (dù khách nói là đóng viện phí cho con), bắt buộc giữ lại và chuyển chuyên viên can thiệp!
- **Thử mức đã đặt trên 3 kịch bản:**
  * *Kịch bản lừa đảo #1:* AI tự giữ tiền ngay, không có quyền tự nhả -> Cái sai mất tiền bị chặn đứng 100%.
  * *Kịch bản viện phí #2:* Tài khoản nhận là BV Chợ Rẫy -> kích hoạt quét mặt 10s -> tiền đi kịp thời, khách thật không bị chờ vô ích.
  * *Kịch bản bình thường (Vé máy bay 6tr đêm khuya):* AI tự giữ -> hỏi xác nhận trong chat -> khách bấm xác nhận OTP -> nhả tiền trong 30s.
- **Một mức tự chủ đã đổi lời hứa với người dùng:** Thay vì hứa *"AI tự động xử lý mọi thứ trong 3 giây"*, đổi thành: *"Ví MoMo tự động giữ an toàn cho tiền của bạn; AI hướng dẫn và thu thập chứng cứ; bạn và chuyên viên luôn là người quyết định cuối cùng."*

---

## §5. Prototype (chặng 5)
- **Thông tin kỹ thuật:** File HTML chạy trực tiếp [`prototype/index.html`](file:///C:/Users/ASUS/Desktop/VINUNI/K5-L3-Day05-AI-Product-UX-Autonomy/prototype/index.html), thuần JavaScript/CSS không phụ thuộc thư viện ngoài; mock giao diện chat MoMo và đếm ngược thời gian thực; không gọi AI thật nhằm đảm bảo an toàn dữ liệu và chạy offline ổn định khi demo.
- **Sơ đồ đánh số nguyên tắc HAX/PAIR và mức tự chủ:**

```
+-------------------------------------------------------------+
| MoMo Support Bot               [📞 1900 636805]             |
+-------------------------------------------------------------+
| [User]: Mở chặn gấp cho tôi chuyển 30 triệu! Công an đang...|
|                                                             |
| [MoMo AI]:                                                  |
| (1) Nhãn độ tin cậy: [Thấp - Nghi vấn lừa đảo 96%]          |
|                                                             |
| +---------------------------------------------------------+ |
| | (2) DỰA VÀO BẰNG CHỨNG GIAO DỊCH:                       | |
| | Mã: MM-948201 · 30.000.000 đ · STK VPBank danh sách đen| |
| +---------------------------------------------------------+ |
|                                                             |
| (3) [CẢNH BÁO ĐỎ]: Công an không yêu cầu chuyển tiền án...  |
|                                                             |
| (4) [TỰ ĐỘNG GIỮ AN TOÀN]: Tiền đang giữ trong [ 14:52 ]    |
|                                                             |
| (5) [NÚT CHUYỂN NGƯỜI]: Kết nối Chuyên viên Chống Lừa Đảo   |
|     [NÚT KHÓA VÍ]: Khóa ví khẩn cấp bảo vệ số dư            |
|                                                             |
| (6) [CẤM QUYỀN]: "AI không có quyền tự giải tỏa lệnh rủi ro"|
+-------------------------------------------------------------+
```

  * *(1) HAX G1 & G2:* Minh bạch danh tính AI và nhãn độ chắc chắn thấp (tuân thủ Điều 11 Luật AI Việt Nam 134/2025/QH15).
  * *(2) HAX G11 + PAIR Explainability:* Hiện thẻ chi tiết giao dịch và lý do cụ thể bị chặn (STK nhận nằm trong danh sách đen).
  * *(3) HAX G10:* Thu hẹp phạm vi khi nghi ngờ, đưa ra cảnh báo chốt chặn trúng thủ đoạn lừa đảo mạo danh.
  * *(4) HAX G8/G9 + Kiểm soát tự chủ:* Tool `giu_giao_dich` tự làm với đồng hồ hoãn 15 phút, tiền chưa mất.
  * *(5) PAIR Graceful Failure:* Nút 1 chạm kết nối chuyên viên con người đính kèm toàn bộ bối cảnh chat.
  * *(6) Cấm quyền AI:* Vô hiệu hóa tool `nha_giao_dich` trên AI, ngăn chặn triệt để nguy cơ người dùng ép AI nhả tiền.
- **Quyết định cách hiện:** Chọn hiện bằng chứng trực quan dạng Card số tiền + đồng hồ đếm ngược đỏ thay vì chỉ giải thích bằng chữ dài dòng. Loại bỏ phương án hiện điểm số % gian lận phức tạp vì gây rối mắt cho người dùng đang hoảng loạn.

---

## §6. Tốt là gì (chặng 6)
- **3 tiêu chí Pass/Fail chấm được:**
  1. *Zero Fund Drain (An toàn tài sản):* 100% trường hợp nghi vấn lừa đảo, AI không tự kích hoạt `nha_giao_dich`. -> **PASS**.
  2. *SLA Khẩn cấp Y tế < 30s:* Ca thanh toán viện phí bệnh viện được cấp luồng sinh trắc học hoặc nối máy chuyên viên dưới 30 giây. -> **PASS**.
  3. *Zero Rule Leakage (Bảo mật quy tắc):* AI không tiết lộ ngưỡng tiền/thuật toán nội bộ và không hỏi OTP trong chat. -> **PASS**.
- **Lỗi còn lại chưa giải quyết triệt để:** Kẻ gian ép nạn nhân thực hiện sinh trắc học ngay tại chỗ hoặc dùng video Deepfake tinh vi. Hướng giải quyết tiếp theo: Bắt buộc đối chiếu thêm vị trí GPS tại bệnh viện và mã hóa đơn viện phí từ cổng kết nối y tế.

---

## §7. Demo (chặng 7)
- **Kịch bản Demo chính (3 phút):** Trình bày ca khó nhất (nạn nhân bị lừa 30tr ép nhả tiền) trên file `prototype/index.html`; đối chiếu 6 điểm đánh số HAX/PAIR; chuyển sang nút Mức 3 để chỉ cho giám khảo thấy nếu để AI tự làm thì tiền đã mất thế nào; chốt lại bằng giải pháp phân luồng ở Mức 2.
- **Xử lý ca lạ nhận được ở demo:**
  * *Ca lạ: "Người dùng bảo con họ đang nằm viện cấp cứu và yêu cầu nhả tiền gấp vào một số tài khoản cá nhân lạ":*
    * Prototype và hệ thống sẽ kiểm tra: STK nhận là **cá nhân** chứ không phải Bệnh viện -> Lập tức kích hoạt Kịch bản #3 (cảnh báo chiêu trò lừa đảo "con cấp cứu"), kích hoạt `giu_giao_dich` 15 phút và nối máy khẩn cấp tới chuyên viên xác minh với nhà trường/bệnh viện trước khi nhả tiền.
    * Nhóm không thay đổi nguyên tắc: Giữ vững ranh giới an toàn, không nhân nhượng mở tự động cho bất kỳ tài khoản cá nhân nào khi chưa được thẩm định!
