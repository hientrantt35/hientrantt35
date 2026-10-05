# Research findings — quan sát thực tập 3 ngày tại tiệm

> Nguồn: quan sát trực tiếp khi thực tập tại tiệm (3 ngày). Mỗi vấn đề ghi: chuyện gì xảy ra → ai bị ảnh hưởng → cách xử lý tạm hiện nay → gợi ý cho thiết kế.

## P1. Khách hỏi "còn bao nhiêu người nữa tới lượt mình?" trước khi quyết định có request thợ không

- **Chuyện gì xảy ra:** Lúc check-in và muốn request một thợ, khách thường hỏi còn bao nhiêu khách nữa mới tới lượt. Họ dùng câu trả lời để cân nhắc: chờ thợ quen, hay ai làm cũng được miễn là nhanh.
- **Ai bị ảnh hưởng:** Lễ tân (mất thời gian, bị ngắt việc), khách (phải chờ câu trả lời, quyết định thiếu thông tin).
- **Cách xử lý tạm hiện nay:** Lễ tân mở danh sách khách và **đếm tay** số khách đang request thợ đó.
- **Vì sao đáng chú ý:** Câu hỏi lặp lại nhiều lần mỗi ngày, ngay ở điểm khách quyết định. Hệ thống đã có dữ liệu này nhưng không hiển thị.
- **Gợi ý cho thiết kế:**
  - Kiosk: khi khách chọn thợ request, hiện ngay *"2 người đang chờ Linh"*, đặt cạnh lựa chọn *"Bất kỳ thợ nào: không phải chờ / 1 người đang chờ"* để khách tự quyết.
  - Màn hình lễ tân: mỗi thợ có con số "số khách request đang chờ" luôn hiển thị, không cần đếm.
  - Nếu sau này có thời lượng dịch vụ, có thể ước tính **thời gian chờ** (vd. "~40 phút") thay vì chỉ số người.
- **Ảnh hưởng tới kế hoạch:** Thêm vào MVP: hàng chờ request theo từng thợ + hiển thị số người chờ ở kiosk và màn hình lễ tân.
- **Cần hỏi thêm:** Khách request xếp hàng theo thứ tự đến? Khách có đổi ý (chuyển sang "ai cũng được") sau khi đã check-in không?
