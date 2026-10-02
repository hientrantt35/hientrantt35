# Kế hoạch dự án: Phần mềm chia turn cho tiệm nail (~50 thợ)

> Bản nháp v0.1 — 2026-10-02. Đây là tài liệu sống, sẽ cập nhật sau mỗi vòng nghiên cứu/thử nghiệm.

## 1. Bài toán

Ở tiệm lớn (~50 thợ), việc chia khách ("turn") thường làm bằng bảng trắng, giấy, hoặc trí nhớ của lễ tân. Hệ quả:

- **Tranh cãi về công bằng** — thợ nghi ngờ bị "lướt turn", lễ tân bị kẹt giữa.
- **Chậm** — khách walk-in đứng chờ trong lúc lễ tân dò xem ai tới lượt, ai làm được dịch vụ đó.
- **Không có dữ liệu** — cuối ngày khó đối chiếu ai làm bao nhiêu turn, khó tính lương/hoa hồng.

**Mục tiêu sản phẩm:** chia turn *nhanh, minh bạch, ai cũng thấy được*, theo đúng luật riêng của từng tiệm.

## 2. Người dùng (giả thuyết — cần kiểm chứng)

| Vai trò | Nhu cầu chính | Bối cảnh dùng |
|---|---|---|
| **Lễ tân / quản lý ca** | Gán khách cho đúng thợ trong < 10 giây; xử lý ngoại lệ (khách request, thợ từ chối) | Tablet/máy tính ở quầy, rất bận giờ cao điểm |
| **Thợ nail** | Biết mình đứng thứ mấy, khi nào tới lượt; tin rằng hệ thống công bằng | Tay bận/đeo găng; xem màn hình TV hoặc điện thoại; nhiều người quen tiếng Việt hơn tiếng Anh |
| **Chủ tiệm** | Cấu hình luật turn; báo cáo cuối ngày/tuần; giảm mâu thuẫn | Điện thoại/máy tính, thường không ở tiệm cả ngày |
| **Khách** (gián tiếp) | Chờ ít, được đúng thợ mình muốn | Không dùng trực tiếp ở MVP |

## 3. Luật chia turn phổ biến (cần xác nhận với tiệm thật)

Đây là các biến thể hay gặp — **mỗi tiệm khác nhau, nên phần mềm phải cấu hình được**:

1. **Thứ tự vào hàng**: theo giờ check-in buổi sáng.
2. **Ai tới lượt**: thợ có *ít turn nhất* đi trước; bằng nhau thì ai *rảnh lâu nhất / check-in sớm hơn* đi trước.
3. **Full turn / half turn**: dịch vụ lớn (full set, pedi deluxe) = 1 turn; dịch vụ nhỏ (đổi màu, wax lông mày) = 0.5 turn. Một số tiệm tính theo **giá tiền** (vd. mỗi $35 = 1 turn).
4. **Khách request**: khách chỉ định thợ → thường *không tính* vào turn (hoặc tính riêng "request turn") để thợ không mất lượt.
5. **Kỹ năng**: chỉ gán cho thợ làm được dịch vụ đó (acrylic, gel-X, dip, pedicure, wax, mi…). Bỏ qua thợ không đủ kỹ năng nhưng *giữ nguyên vị trí* của họ.
6. **Đang bận / nghỉ trưa**: tạm rời hàng, quay lại vẫn giữ số turn.
7. **Từ chối khách**: tuỳ tiệm — mất lượt, bị cộng turn phạt, hoặc không sao.
8. **Khách nhiều dịch vụ / nhiều thợ**: 1 khách mani + pedi chia cho 2 thợ làm song song.
9. **Chỉnh tay**: quản lý có thể cộng/trừ turn, nhưng **mọi chỉnh sửa phải có lý do và lưu lịch sử**.

## 4. Phạm vi MVP (phiên bản đầu)

**Có:**
- Check-in / check-out thợ, đánh dấu nghỉ.
- Danh sách thợ + kỹ năng; danh mục dịch vụ + giá trị turn.
- Hàng chờ tự động sắp xếp; nút **"Gán khách"** gợi ý thợ tiếp theo đủ kỹ năng.
- Xử lý: request, từ chối, chia khách cho nhiều thợ, hoàn thành dịch vụ.
- **Màn hình TV** cho cả tiệm xem hàng chờ (minh bạch = giảm cãi nhau).
- Nhật ký (audit log) mọi thao tác, không xoá được.
- Báo cáo cuối ngày: turn/thợ, số request, số khách.
- Giao diện song ngữ **Việt / Anh**, chữ to, nút to.

**Chưa làm (v2+):**
- Đặt lịch hẹn online, app cho khách.
- Thông báo đẩy tới điện thoại thợ.
- Tích hợp POS / thanh toán / tip / tính lương.
- Nhiều chi nhánh.

## 5. Mô hình dữ liệu (phác thảo)

```
Salon ──< Technician >──< Skill
  │            │
  │            └──< Shift (check-in, check-out, trạng thái: available/busy/break/off)
  ├──< Service (tên, thời lượng, giá, turn_value, skill yêu cầu)
  ├──< Ticket (1 lượt khách) ──< Assignment (thợ, dịch vụ, is_request, bắt đầu, xong)
  ├──< TurnEvent (log bất biến: +turn, -turn, skip, adjust, lý do, người thao tác)
  └── TurnRules (cấu hình luật của tiệm: cách tính turn, luật request, luật từ chối…)
```

Ý chính: **số turn của thợ = tổng các TurnEvent trong ngày**, không lưu một con số rồi sửa đè. Nhờ vậy luôn truy vết được "vì sao chị A có 4.5 turn".

## 6. Thuật toán gán thợ (phác thảo)

```
next_technician(service, rules):
  ứng viên = thợ đang check-in, trạng thái available, có skill của service
  sắp xếp theo:
    1. tổng turn hôm nay (tăng dần)
    2. thời điểm rảnh gần nhất (sớm hơn trước)
    3. giờ check-in (sớm hơn trước)
  trả về ứng viên đầu tiên (lễ tân vẫn có thể chọn người khác + ghi lý do)
```

Thuật toán sẽ được viết thành một module thuần, có bộ test với các tình huống thật thu thập từ tiệm.

## 7. Công nghệ đề xuất

- **Web app dạng PWA** (chạy trên tablet, TV, điện thoại; không cần lên App Store).
- Frontend: React / Next.js. Backend + realtime: Supabase (Postgres) — 50 thợ, vài thiết bị cùng lúc là rất nhẹ.
- Cần **chịu được mất wifi tạm thời** ở quầy lễ tân (lưu cục bộ rồi đồng bộ lại).
- Thiết kế: Figma (prototype để test với lễ tân trước khi code).

## 8. Lộ trình theo quy trình HCD

| Giai đoạn | Thời gian (ước) | Việc chính | Đầu ra |
|---|---|---|---|
| **0. Khám phá** | 1–2 tuần | Quan sát 1–2 tiệm giờ cao điểm; phỏng vấn chủ tiệm, 2 lễ tân, 5–8 thợ; chụp lại bảng turn/giấy đang dùng | Ghi chú thực địa, danh sách luật turn thực tế, pain points |
| **1. Định nghĩa** | 1 tuần | Persona, journey map lễ tân & thợ, đặc tả luật turn, chỉ số thành công | Tài liệu yêu cầu, bộ test case luật turn |
| **2. Thiết kế** | 2 tuần | Flow, wireframe → prototype Figma; test usability với lễ tân (5 người) | Prototype đã kiểm chứng |
| **3. Xây MVP** | 4–6 tuần | Module luật turn + test; màn hình lễ tân; màn hình TV; báo cáo | Bản chạy được |
| **4. Pilot** | 2 tuần | Chạy song song với cách cũ ở 1 tiệm; thu feedback hằng ngày | Danh sách sửa lỗi, quyết định go/no-go |
| **5. Mở rộng** | sau đó | Thông báo cho thợ, lịch hẹn, POS, nhiều chi nhánh | — |

## 9. Chỉ số thành công (đề xuất)

- Thời gian lễ tân gán 1 khách: **< 10 giây**.
- Số lần tranh cãi về turn/tuần: giảm **≥ 50%** so với trước pilot.
- Thời gian chờ trung bình của khách walk-in: giảm.
- ≥ 80% thợ đánh giá "công bằng" hoặc "rất công bằng" sau 2 tuần.

## 10. Rủi ro

- **Luật ngầm** không ai nói ra (vd. ưu tiên thợ thâm niên) → phải quan sát, không chỉ phỏng vấn.
- **Lễ tân không kịp bấm** giờ cao điểm → thao tác tối đa 1–2 chạm.
- **Thợ không tin máy** → màn hình TV công khai + audit log.
- **Wifi yếu** → chế độ offline cho quầy lễ tân.

## 11. Câu hỏi mở (cần trả lời trước giai đoạn 2)

1. Đã có tiệm cụ thể để nghiên cứu/pilot chưa? Tiệm đang chia turn bằng cách nào?
2. Turn tính theo **số lượt**, **full/half**, hay theo **giá tiền**?
3. Khách request có tính turn không? Thợ từ chối khách thì sao?
4. Có lịch hẹn trước không, hay chủ yếu walk-in?
5. Thiết bị sẵn có: tablet ở quầy? TV? Thợ có dùng smartphone trong giờ làm không?
6. Mục tiêu của dự án: sản phẩm thật để bán/dùng, hay đồ án/portfolio cho chương trình HCD (hay cả hai)?
