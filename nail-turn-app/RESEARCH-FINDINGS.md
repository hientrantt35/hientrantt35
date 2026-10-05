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

## P2. Một khách làm 2 dịch vụ thuộc 2 nhóm thợ khác nhau thì khó theo dõi

- **Chuyện gì xảy ra:** Khách làm cả chân (nhóm chân) và tay gắn tips (nhóm tay). Một lượt khách bị tách làm 2 phần, do 2 thợ ở 2 nhóm làm, và việc theo dõi trở nên rối.
- **Ai bị ảnh hưởng:** Lễ tân (theo dõi, gán thợ, tính turn), thợ của cả 2 nhóm, khách (có thể bị chờ lại giữa 2 phần).
- **Những chỗ có thể rối (giả thuyết, cần xác nhận):**
  - Không biết phần nào làm trước, làm song song hay lần lượt.
  - Làm xong phần 1, khách có bị "quên" ở hàng chờ của nhóm thứ 2 không, hay phải xếp lại từ đầu?
  - Thợ nào làm phần nào; mỗi nhóm có được tính 1 turn không.
  - Lúc nào lượt khách mới thật sự xong (để tính tiền/đóng ticket).
- **Quy trình thực tế (đã xác nhận):** Làm **lần lượt**, không song song.
  1. Thợ phần 1 làm xong → **mang phiếu giấy xuống quầy**, ghi tổng tiền dịch vụ phần 1.
  2. Phiếu **nằm chờ ở quầy** cho tới khi có thợ nhóm 2 rảnh.
  3. Có thợ rảnh → phiếu được **đưa tay** sang thợ phần 2.
- **Điểm đau rút ra:**
  - **Phiếu giấy là "nguồn sự thật" duy nhất** của lượt khách → dễ thất lạc, nhầm phiếu, khó biết phiếu đang ở đâu.
  - **Không có gì báo** khi thợ nhóm 2 rảnh → lễ tân phải tự nhớ phiếu đang chờ; khách ngồi không giữa 2 phần.
  - Thợ phần 1 phải rời chỗ đi xuống quầy chỉ để ghi tiền.
- **Gợi ý cho thiết kế:**
  - **Phiếu điện tử** cho mỗi lượt khách; phiếu giấy (nếu tiệm vẫn muốn) chỉ là bản phụ.
  - Lễ tân bấm **"Phần 1 xong" + nhập tiền phần 1** (1 màn hình, vài chạm) → phần 2 **tự vào đầu hàng chờ nhóm 2**.
  - Khi có thợ nhóm 2 rảnh, màn hình lễ tân **tự nhắc**: *"Gọi Tuấn — khách Anna phần Tay (đã chờ 6 phút)"*.
  - Có thể **gán trước** thợ phần 2 khi phần 1 sắp xong để thợ chuẩn bị, giảm thời gian khách ngồi chờ.
  - Ghi tiền theo từng phần → báo cáo doanh thu/turn theo từng thợ chính xác.
  - Chỉ số mới: **thời gian khách chờ giữa 2 phần**.
- **Gợi ý cho thiết kế (cấu trúc):**
  - Một lượt khách (Visit) gồm **nhiều phần dịch vụ**; mỗi phần nằm trong hàng chờ của nhóm mình, có thợ riêng, trạng thái riêng (chờ → đang làm → xong).
  - Thẻ khách trên màn hình lễ tân hiện **cả 2 phần với tiến độ**, vd. *"Chân: Mai ✓ xong · Tay: đang chờ — tới lượt Tuấn"*.
  - Khi phần 1 xong, phần 2 **tự động được ưu tiên/giữ chỗ** trong hàng nhóm kia — không bắt khách xếp lại từ đầu (cần xác nhận luật tiệm).
  - Mỗi phần tính turn cho nhóm của nó (cần xác nhận).
- **Ảnh hưởng tới kế hoạch:** Mô hình dữ liệu tách Visit → nhiều "service line"; thêm thẻ khách nhiều phần vào màn hình lễ tân.
- **Đã xác nhận thêm:** Mỗi thợ (mỗi phần) được tính **1 turn**. Phiếu **có bị thất lạc**; POS **in lại được** nên không mất dữ liệu, nhưng tốn thêm thao tác và thời gian.
- **Cần hỏi thêm:** Thợ có mang phiếu xuống ngay hay để lễ tân tự đến lấy?

## P3. Thợ làm xong phần của mình nhưng máy vẫn coi là "bận" vì khách chưa thanh toán

- **Chuyện gì xảy ra:** Với khách làm nhiều phần (P2), thợ phần 1 làm xong nhưng **cả lượt khách chưa thanh toán**, nên POS **không tự chuyển thợ đó sang rảnh (free)**. Thợ phải **tự đổi trạng thái trên monitor** ở quầy, hoặc **manager đổi giúp**.
- **Ai bị ảnh hưởng:** Thợ (có thể mất lượt nếu quên đổi), manager (bị gọi đổi giúp), lễ tân (gợi ý thợ sai), khách đang chờ (chờ lâu hơn dù có thợ rảnh).
- **Nguyên nhân gốc:** POS gắn trạng thái thợ với **thanh toán của cả phiếu**, trong khi thực tế thợ rảnh khi **phần việc của họ** xong.
- **Hệ quả có thể xảy ra (cần xác nhận):** Thợ quên đổi → hàng chờ hiển thị sai → thợ bị bỏ qua (thiệt turn) → mất niềm tin, tranh cãi.
- **Phát hiện phụ:** Monitor ở quầy **không chỉ để xem** — thợ **có chạm/thao tác** trên đó để đổi trạng thái.
- **Gợi ý cho thiết kế:**
  - Tách 2 khái niệm: **phần dịch vụ xong** (thợ rảnh ngay) và **phiếu thanh toán xong** (đóng lượt khách).
  - Bấm "Phần 1 xong" (P2) → thợ phần 1 **tự động về trạng thái rảnh** và trở lại hàng chờ, không cần đổi tay.
  - Vẫn cho thợ tự đổi trạng thái trên monitor (nghỉ, quay lại) bằng 1 chạm vào tên mình; mọi thay đổi ghi lịch sử.
  - Cảnh báo cho lễ tân nếu một thợ "bận" quá lâu bất thường (vd. lâu hơn thời lượng dịch vụ + 15 phút).
- **Ảnh hưởng tới kế hoạch:** Trạng thái thợ dựa trên **phần dịch vụ**, không dựa trên thanh toán; monitor có thao tác đơn giản cho thợ.
- **Cần hỏi thêm:** Bạn có thấy thợ quên đổi trạng thái và bị bỏ lượt không? Đổi trạng thái trên monitor có cần mã PIN/xác nhận gì không? Khách chỉ làm 1 dịch vụ thì thợ có cũng bị "kẹt bận" chờ thanh toán không?
