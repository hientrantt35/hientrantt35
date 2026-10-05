# Kế hoạch dự án: Phần mềm chia turn cho tiệm nail (~50 thợ)

> Bản nháp v0.3 — cập nhật 2026-10-05. Đây là tài liệu sống, sẽ cập nhật sau mỗi vòng nghiên cứu/thử nghiệm.

## 1. Bài toán

Ở tiệm lớn (~50 thợ), việc chia khách ("turn") thường làm bằng bảng trắng, giấy, hoặc trí nhớ của lễ tân. Hệ quả:

- **Tranh cãi về công bằng** — thợ nghi ngờ bị "lướt turn", lễ tân bị kẹt giữa.
- **Chậm** — khách walk-in đứng chờ trong lúc lễ tân dò xem ai tới lượt, ai làm được dịch vụ đó.
- **Không có dữ liệu** — cuối ngày khó đối chiếu ai làm bao nhiêu turn, khó tính lương/hoa hồng.

**Mục tiêu sản phẩm:** chia turn *nhanh, minh bạch, ai cũng thấy được*, theo đúng luật riêng của từng tiệm.

## 1b. Bối cảnh & luật đã xác nhận (hỏi đáp 2026-10-05)

| # | Câu hỏi | Trả lời | Ảnh hưởng tới thiết kế |
|---|---|---|---|
| 1 | Mục tiêu dự án | **Cả hai**: đồ án HCD + hướng tới sản phẩm thật | Làm nghiên cứu bài bản, nhưng kiến trúc phải đủ chắc để dùng thật |
| 2 | Tiệm để nghiên cứu/pilot | **Có, nhưng chưa thân** | Cần kế hoạch tiếp cận: thư giới thiệu, consent form, xin lịch giờ vắng khách |
| 3 | Cách chia turn hiện tại | **Phần mềm có sẵn** | Có đối thủ trực tiếp → phải phân tích kỹ nó; giá trị của mình là *làm tốt hơn*, không phải *số hoá giấy* |
| 4 | Vấn đề hiện tại | **Còn nhiều bất cập; khó dùng / chậm** | Trọng tâm là UX của lễ tân: ít bước, nhanh giờ cao điểm. Cần quan sát để liệt kê bất cập cụ thể |
| 5 | Phần mềm đó là gì | **Chức năng nằm trong máy POS** | Tiệm khó bỏ POS → app của mình nên **chạy song song** POS (chỉ lo chia turn), tích hợp POS để sau |
| 6 | Cách tính turn | **Mỗi khách = 1 turn** | Không cần full/half; giữ cấu hình được cho tiệm khác |
| 7 | Khách request | **Không tính turn** | Thợ làm khách request vẫn giữ vị trí; vẫn đếm riêng để báo cáo |
| 8 | Thợ từ chối khách | **Không được từ chối** | Không cần luật phạt; nhưng cần nút "bỏ qua" có lý do (thợ đang dở tay, đi vệ sinh…) |
| 9 | Kỹ năng | **Chia nhóm/khu vực** — mỗi nhóm một hàng turn riêng | Hệ thống có **nhiều hàng chờ song song**; dịch vụ gắn với nhóm |
| 10 | Thợ ở nhiều nhóm? | **Chỉ 1 nhóm** | Mô hình đơn giản: Thợ → 1 Nhóm |
| 11 | Ai đứng đầu hàng | **Ít turn nhất**, bằng nhau thì check-in sớm hơn | Thuật toán §6 giữ nguyên, áp dụng trong từng nhóm |
| 12 | Loại khách | **Chủ yếu walk-in** | Không cần lịch hẹn ở MVP; tối ưu cho dòng khách vào liên tục |
| 13 | Ai gán khách | **Lễ tân ở quầy** | Màn hình lễ tân là màn hình quan trọng nhất |
| 14 | Thợ xem hàng chờ ở đâu | **Monitor ở quầy lễ tân**; tới lượt thì lễ tân/manager **gọi bộ đàm**; khi cần lễ tân **nhắn SMS / tra số điện thoại để gọi** | Không cần app cho thợ. Cần: màn hình monitor dễ đọc từ xa, nút "Gọi" hiển thị to tên thợ để lễ tân đọc qua bộ đàm, tra nhanh SĐT thợ (bấm để gọi / gửi SMS) |
| 15 | Số người/máy ở quầy | **3 lễ tân dùng 2 máy gán thợ + 2 máy cho khách tự check-in** | Cần **đồng bộ tức thì** giữa các máy và **chống 2 máy gán cùng 1 thợ** cùng lúc |
| 16 | Check-in khách | **Làm kiosk mới** trong app của mình | Thêm màn hình kiosk: khách nhập tên, SĐT, chọn dịch vụ (→ nhóm), chọn thợ request (tuỳ chọn) |
| 17 | Ngôn ngữ | **Chỉ tiếng Anh** | Một ngôn ngữ, chữ rõ, từ ngữ đơn giản |
| 18 | Ai code | **Bạn + Claude** | Chọn công nghệ đơn giản, dịch vụ có sẵn, ít phải tự vận hành server |
| 19 | Thời hạn | **Cuối học kỳ này (~giữa tháng 12/2026, ~10 tuần)** | Rút gọn lộ trình, làm song song nghiên cứu và code phần lõi; pilot ngắn |

## 2. Người dùng

| Vai trò | Nhu cầu chính | Bối cảnh dùng |
|---|---|---|
| **Lễ tân (3 người, 2 máy)** | Gán khách cho đúng thợ trong < 10 giây; biết gọi ai qua bộ đàm; xử lý request, bỏ qua thợ | Máy ở quầy, rất bận giờ cao điểm, vừa đón khách vừa nghe bộ đàm |
| **Thợ nail (~50, chia nhóm)** | Biết mình đứng thứ mấy trong nhóm; tin rằng hệ thống công bằng | Không dùng app; nhìn **monitor ở quầy** khi đi ngang; nghe gọi qua bộ đàm |
| **Manager / chủ tiệm** | Chỉnh turn khi có sự cố (có lý do); báo cáo cuối ngày; giảm mâu thuẫn | Có thể đứng quầy hoặc xem từ xa |
| **Khách walk-in** | Check-in nhanh, chọn được thợ quen | **Kiosk** (2 máy) ở cửa |

## 3. Luật chia turn của tiệm (đã xác nhận — cấu hình được cho tiệm khác)

1. Mỗi **nhóm** (vd. tay, chân, wax…) có **hàng turn riêng**; mỗi thợ thuộc **đúng 1 nhóm**; mỗi dịch vụ thuộc 1 nhóm.
2. **Mỗi khách walk-in = 1 turn** cho thợ làm khách đó.
3. Thứ tự trong nhóm: **ít turn nhất đi trước**; bằng nhau thì **check-in sớm hơn** đi trước.
4. **Khách request: không tính turn** — thợ vẫn giữ vị trí; vẫn đếm riêng số request để báo cáo.
5. **Thợ không được từ chối** khách khi tới lượt.
6. Thợ đang làm khách (busy) hoặc nghỉ (break) tạm không được gợi ý, **giữ nguyên số turn**.
7. **Chỉnh tay** (cộng/trừ turn, bỏ qua thợ vì lý do thực tế như đang dở tay/đi vệ sinh): chỉ manager, **bắt buộc ghi lý do**, lưu lịch sử.
8. Cần xác nhận khi quan sát: khách làm dịch vụ ở **2 nhóm** (vd. tay + chân) thì mỗi nhóm tính 1 turn cho thợ của nhóm đó?

## 4. Phạm vi MVP (cho hạn cuối học kỳ)

**Có — 4 màn hình:**
1. **Kiosk khách** (2 máy): nhập tên + SĐT → chọn dịch vụ → (tuỳ chọn) chọn thợ request → vào hàng chờ khách. Khi chọn thợ, **hiện số người đang chờ thợ đó** cạnh lựa chọn "bất kỳ thợ nào" *(xem RESEARCH-FINDINGS P1)*.
2. **Màn hình lễ tân** (2 máy, đồng bộ tức thì):
   - Danh sách khách đang chờ; mỗi khách hiện **thợ được gợi ý** của nhóm tương ứng.
   - Bấm **"Assign"** → hiện to *"Call: Linh — Pedicure"* để đọc qua bộ đàm.
   - Nút **Done** khi thợ làm xong; đổi trạng thái thợ (break/back).
   - Mỗi thợ hiện **số khách request đang chờ** — trả lời khách ngay, không phải đếm tay *(P1)*.
   - Tra nhanh **SĐT thợ** (bấm gọi / gửi SMS từ máy).
   - Chống xung đột: nếu máy kia vừa gán thợ đó, máy này báo ngay và gợi ý người kế tiếp.
3. **Monitor hàng chờ** ở quầy: mỗi nhóm một cột, thứ tự thợ, số turn, trạng thái — đọc rõ từ 2–3 mét.
4. **Màn hình manager**: check-in/out thợ, quản lý nhóm & dịch vụ, chỉnh turn có lý do, **báo cáo cuối ngày** (turn/thợ, request/thợ, số khách), xem lịch sử thao tác.

**Không làm trong học kỳ này (v2+):**
- Gửi SMS tự động từ hệ thống (Twilio…) — MVP chỉ mở app gọi/nhắn trên máy.
- Đặt lịch hẹn, app cho thợ, tích hợp POS / thanh toán / tip / lương, nhiều chi nhánh.

## 5. Mô hình dữ liệu (phác thảo)

```
Salon
 ├──< Group (Nails, Pedicure, Wax…)
 │      ├──< Technician (tên, SĐT, 1 group)
 │      │      └──< Shift (check-in, check-out, status: available / busy / break / off)
 │      └──< Service (tên, group)
 ├──< Customer (tên, SĐT)               ← từ kiosk
 ├──< Visit (1 lượt khách: customer, dịch vụ, thợ request?, trạng thái: waiting / in_service / done)
 │      └──< Assignment (thợ, dịch vụ, is_request, assigned_by, bắt đầu, xong)
 └──< TurnEvent (log bất biến: +1 turn, request, adjust, skip — lý do, người thao tác, thời điểm)
```

Ý chính: **số turn của thợ = tổng TurnEvent trong ngày** (không ghi đè một con số). Luôn trả lời được "vì sao chị A có 5 turn".

## 6. Thuật toán gợi ý thợ

```
next_technician(group):
  ứng viên = thợ thuộc group, đã check-in, status = available
  sắp xếp: (1) số turn hôm nay ↑  (2) giờ check-in ↑
  trả về người đầu tiên

assign(visit, tech):
  trong 1 transaction: kiểm tra tech vẫn available → đặt busy → ghi Assignment
  → nếu là walk-in: ghi TurnEvent +1 ; nếu là request: ghi TurnEvent "request" (0 turn)
  nếu tech đã bị máy khác gán trước: báo lỗi, gợi ý người kế tiếp
```

Viết thành module thuần có bộ test (Claude viết, bạn kiểm tra bằng các tình huống quan sát được ở tiệm).

## 7. Công nghệ (đơn giản để bạn + Claude làm được)

- **Một web app** (Next.js + TypeScript), mở trên trình duyệt: kiosk (tablet ở chế độ toàn màn hình), máy lễ tân, monitor, manager — cùng 1 app, khác đường dẫn.
- **Supabase**: database Postgres + đồng bộ realtime giữa các máy + đăng nhập cho lễ tân/manager. Gói miễn phí đủ cho pilot.
- **Vercel** để đưa app lên mạng (miễn phí).
- Figma cho wireframe/prototype trước khi code.
- Lưu ý: SĐT khách & thợ là dữ liệu cá nhân → chỉ lễ tân/manager xem được; monitor chỉ hiện tên.

## 8. Lộ trình 10 tuần (đến giữa tháng 12/2026)

Vì luật turn đã khá rõ, **code phần lõi chạy song song với nghiên cứu**.

| Tuần | Ngày | Nghiên cứu & thiết kế (bạn) | Code (Claude, bạn review) |
|---|---|---|---|
| 1 | 5–11/10 | Liên hệ tiệm, gửi thư giới thiệu + consent form; chuẩn bị bộ câu hỏi phỏng vấn & mẫu ghi chép quan sát | Dựng project, data model, module thuật toán + test |
| 2 | 12–18/10 | **Quan sát** 2 buổi giờ cao điểm (1 buổi cuối tuần); chụp/ghi lại cách dùng POS hiện tại; phỏng vấn 3 lễ tân, manager, 5–6 thợ | Màn hình manager cơ bản (thợ, nhóm, dịch vụ, check-in thợ) |
| 3 | 19–25/10 | Tổng hợp: affinity map, persona lễ tân & thợ, journey map giờ cao điểm, danh sách bất cập của POS | Màn hình lễ tân + đồng bộ realtime 2 máy |
| 4 | 26/10–1/11 | Wireframe 4 màn hình → prototype Figma | Kiosk khách + monitor |
| 5 | 2–8/11 | **Usability test vòng 1** với lễ tân (prototype) | Sửa theo kết quả test |
| 6 | 9–15/11 | Chỉnh thiết kế; chuẩn bị kịch bản test với app thật | Báo cáo cuối ngày, lịch sử thao tác, chỉnh turn |
| 7 | 16–22/11 | **Usability test vòng 2** trên app thật (giờ vắng khách, dữ liệu giả) | Sửa lỗi, tối ưu tốc độ thao tác |
| 8 | 23–29/11 | (Tuần Thanksgiving — tiệm đông, tránh làm phiền) Viết case study phần nghiên cứu | Hoàn thiện, kiểm thử tình huống khó |
| 9 | 30/11–6/12 | **Pilot ngắn** 1–3 ngày: chạy song song với POS ở 1 máy, so sánh kết quả | Hỗ trợ trực tiếp khi pilot |
| 10 | 7–13/12 | Đánh giá theo chỉ số §9; hoàn thiện case study & thuyết trình | Sửa lỗi cuối |

## 9. Chỉ số thành công

- Lễ tân gán 1 khách: **< 10 giây**, **≤ 2 chạm**.
- Khách tự check-in ở kiosk: **< 45 giây**.
- Trong pilot: thứ tự gợi ý khớp với luật tiệm **100%** (so với cách làm thủ công/POS).
- Lễ tân đánh giá dễ dùng hơn POS hiện tại (thang SUS hoặc so sánh trực tiếp).
- Thợ đánh giá "công bằng / dễ theo dõi" khi nhìn monitor.

## 10. Rủi ro & cách giảm

- **Tiệm chưa thân, khó xin quyền** → đi từ manager/chủ tiệm trước, xin giờ vắng khách, giữ buổi ngắn, mang quà nhỏ; luôn có consent form.
- **Luật ngầm** (ưu tiên thâm niên, người nhà…) → quan sát thực tế, không chỉ hỏi.
- **Pilot làm phiền giờ làm ăn** → chạy song song, không thay POS; chọn ngày trong tuần.
- **Wifi yếu / 2 máy lệch dữ liệu** → transaction khi gán thợ; hiện rõ trạng thái "mất kết nối".
- **Thời gian 10 tuần** → giữ phạm vi MVP, mọi ý tưởng mới đưa vào danh sách v2.

## 11. Câu hỏi còn mở (trả lời khi quan sát tại tiệm)

1. Khách làm dịch vụ ở 2 nhóm (tay + chân) thì tính turn thế nào? Có thợ làm song song không?
2. Có những nhóm nào cụ thể, mỗi nhóm bao nhiêu thợ?
3. Thợ check-in buổi sáng bằng cách nào? Thợ về sớm / vào trễ thì xử lý turn ra sao?
4. POS hiện tại bất cập cụ thể ở bước nào (đếm số chạm, thời gian mỗi lần gán)?
5. Ai được quyền chỉnh turn? Có tranh cãi nào gần đây — vì sao?
