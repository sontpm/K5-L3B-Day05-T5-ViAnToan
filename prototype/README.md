# Prototype Hướng dẫn & Mô tả giao diện · Track T5 Ví MoMo

## 1. Cách chạy Prototype
- File prototype là một ứng dụng web HTML5 thuần túy, hoàn toàn độc lập và không cần cài đặt backend: [`prototype/index.html`](file:///C:/Users/ASUS/Desktop/VINUNI/K5-L3-Day05-AI-Product-UX-Autonomy/prototype/index.html).
- **Cách mở:** Chỉ cần nhấp đúp chuột vào file `index.html` hoặc mở bằng bất kỳ trình duyệt nào (Google Chrome, Microsoft Edge, Safari, Firefox).

---

## 2. Các tính năng chính trên Prototype
1. **Thanh chuyển đổi Kịch bản (Scenarios):**
   - **Ca 1 (Ca khó nhất 1):** Người dùng bị thao túng tâm lý, đe dọa lừa đảo chuyển 30 triệu đồng tiền "án điều tra".
   - **Ca 2 (Ca khó nhất 2):** Người dùng thật đang gấp tại quầy viện phí Chợ Rẫy (cần đóng 15 triệu mổ cấp cứu cho Merchant Bệnh viện).
   - **Ca 4 (Hard Test phản biện sắc bén):** Kẻ gian lừa chiêu "Con ngã viện cấp cứu" giục chuyển 10 triệu vào STK cá nhân -> Hệ thống phát hiện STK cá nhân, từ chối nhả tự động và giữ an toàn.
   - **Ca 3:** Người dùng báo bị trừ tiền lạ 2 triệu lúc nửa đêm nạp game.
2. **Thanh chuyển đổi Mức tự chủ của Tool `nha_giao_dich`:**
   - **Mức 1 (Chỉ gợi ý):** An toàn nhưng chậm.
   - **Mức 2 (Cần người duyệt - Nhóm đề xuất):** Cân bằng hoàn hảo giữa an toàn tài sản và tốc độ xử lý ca khẩn cấp.
   - **Mức 3 (AI tự động nhả):** Mô phỏng rủi ro thảm họa khi AI nhẹ dạ nhả tiền cho tội phạm.
3. **Đồng hồ đếm ngược giữ giao dịch (`15:00`):** Minh chứng tiền chưa rời khỏi ví.
4. **Modal Khóa ví khẩn cấp:** Mô phỏng việc người dùng chủ động bảo vệ số dư.
5. **Nút kết nối Chuyên viên phòng chống gian lận:** Line nóng ưu tiên.

---

## 3. Sơ đồ đánh số nguyên tắc HAX/PAIR trên Prototype

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

### Chi tiết các điểm đánh số:
* **(1) HAX G1 & G2:** Minh bạch đây là Trợ lý AI và thể hiện trực quan độ tin cậy của tình huống (Độ chắc chắn: Thấp do trùng khớp dấu hiệu lừa đảo).
* **(2) HAX G11 + PAIR Explainability:** Trích dẫn tường minh căn cứ dữ liệu (Mã GD `MM-948201`, STK nhận thuộc danh sách đen) để người dùng tự đối chiếu.
* **(3) HAX G10:** Thu hẹp phạm vi khi nghi ngờ, đưa ra cảnh báo dứt khoát về thủ đoạn mạo danh công quyền.
* **(4) HAX G8/G9 + Control:** Thể hiện việc AI tự động kích hoạt `giu_giao_dich` an toàn với đồng hồ đếm ngược 15 phút, tiền chưa mất.
* **(5) PAIR Graceful Failure / Escalation:** Đường thoát sang Chuyên viên An ninh con người chỉ với 1 chạm.
* **(6) Cấm quyền AI:** Vô hiệu hóa quyền tự ý nhả giao dịch của AI, chặn đứng nguy cơ người dùng bị thao túng ép AI nhả tiền.

