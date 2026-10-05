# MVP spec — thông tin để build bản đầu

> Thu thập qua hỏi đáp 2026-10-05. Bổ sung cho `PLAN.md` (§3 luật turn) và `RESEARCH-FINDINGS.md` (P1–P5).

## Quyết định đã chốt

| # | Câu hỏi | Trả lời | Cách build |
|---|---|---|---|
| M1 | Các nhóm thợ | ~~3 nhóm~~ → **sửa lại: 2 nhóm** (xác nhận sau):<br>• **Pedi & Mani** (nhóm chân): làm chân **và cả tay** nếu khách *không* gắn móng dài / acrylic / tips / hard gel.<br>• **Enhancements** (nhóm tay): **chỉ** làm tay có gắn móng (acrylic, tips, hard gel). | Nhóm được **suy ra từ dịch vụ**, không bắt khách chọn nhóm: Feet + Hands tự nhiên → Pedi & Mani; Hands có extension → Enhancements. Nhiều dịch vụ cùng nhóm gộp thành 1 phần (vd. "Pedicure + Gel manicure", 1 thợ, 1 turn — *cần xác nhận*). Tạm dùng 28 / 22 thợ |
| M2 | Khách làm 2 nhóm: phần nào trước | **Nhóm nào có thợ rảnh trước** | Cả 2 phần vào hàng chờ của 2 nhóm cùng lúc; phần nào có thợ rảnh trước thì gợi ý gán trước. Khi 1 phần đang làm, phần kia **bị khoá** (khách không thể ở 2 chỗ) cho tới khi phần đầu xong |
| M3 | Thợ rảnh, có cả khách request mình đang chờ và mình đang đứng đầu hàng walk-in | **Khách request trước** | Khi thợ rảnh: nếu có khách request thợ đó → gợi ý khách request (0 turn, thợ giữ vị trí). Walk-in chuyển cho người kế tiếp trong hàng |
| M4 | Thợ check-in buổi sáng | **Tự bấm trên monitor** | Monitor có chế độ "Check in": chọn tên → nhập PIN |
| M5 | PIN khi thao tác trên monitor | **Có, PIN 4 số** cho check-in, nghỉ, quay lại | Mỗi thợ có PIN; sai PIN thì không đổi; mọi thao tác ghi lịch sử |
| M6 | Thợ vào trễ có 0 turn (sẽ nhảy lên đầu hàng) | **Manager chỉnh tay** | Không tự động bù turn. Khi thợ check-in trễ, hệ thống **nhắc manager**: "Hoa check-in lúc 1:05pm, 0 turn — nhóm đang trung bình 4 turn. Chỉnh turn?" → manager nhập số + lý do |

| M7 | Ai được chỉnh turn / đổi trạng thái giùm thợ | **Manager — và manager chính là lễ tân** (3 người ở quầy). Chỉ họ được chỉnh | 2 vai trò: **Manager** (đăng nhập ở máy quầy: gán, xong, chỉnh turn có lý do) và **Thợ** (chỉ check-in / nghỉ / quay lại bằng PIN) |
| M8 | Kiosk khách nhập gì | **Tên + SĐT, chọn dịch vụ, chọn thợ request** (tuỳ chọn) | Xem M10 về mức chi tiết dịch vụ |
| M9 | Menu dịch vụ | **Dùng menu mẫu** | Claude tạo menu mẫu phổ biến cho 3 nhóm; chỉnh sau khi có menu thật |
| M10 | Có ghi tiền không | **Không ghi tiền.** Chỉ cần chọn **nhóm dịch vụ** làm cơ sở chia turn. Khách quen thường không hỏi giá; khi hỏi thì **manager đứng hỗ trợ check-in** trả lời. Manager thường **đứng cạnh kiosk** để check-in nhanh và đảm bảo chọn **đúng nhóm** | Kiosk chọn theo **nhóm** (Tay / Chân / Wax-Mi), mỗi nhóm có ví dụ dịch vụ bên dưới để dễ chọn đúng; **không hiện giá**. Kiosk phải dùng tốt cả khi **manager bấm giùm** (nhanh, ít bước) |
| M11 | Thiết bị | **Màn cảm ứng** (2 máy quầy) + **iPad** (2 kiosk) | Thiết kế touch-first: nút ≥ 48px, không dựa vào hover/chuột |
| M12 | Monitor cho thợ | **Chung máy lễ tân** — thợ bấm trực tiếp trên máy gán thợ | Máy quầy có **thanh hàng chờ thợ** luôn hiện; thợ chạm tên → **bảng PIN nổi lên** → xong tự đóng, **không làm mất màn hình lễ tân đang dùng**. Sau pilot cân nhắc thêm 1 tablet riêng cho thợ |
| M13 | Báo cáo cuối ngày | **Turn mỗi thợ** (walk-in + request), **lịch sử chỉnh sửa**, **tổng khách & thời gian chờ**, + một mục khác (đang hỏi lại) | Trang báo cáo theo ngày; số liệu thời gian chờ dùng luôn cho case study |
| M14 | Có tách danh sách *Waiting* / *In service* không | **Không tách** (góp ý của bạn): tách ra làm khó thấy nhanh khách nào đang request ai | **Một danh sách theo giờ check-in** (giống POS, lễ tân đã quen). Khách đang làm hiện gọn & nhạt hơn, khách chờ nổi bật (thời gian chờ + nút Assign). Bộ lọc **All / Waiting / Requests**; *Requests* **gom theo thợ được request** (vd. "Linh · 2 waiting · busy now") |

## Tóm tắt luồng chính (để build)

1. **Sáng:** thợ chạm tên trên máy quầy → PIN → vào hàng nhóm của mình (0 turn).
2. **Khách đến:** kiosk (thường có manager hỗ trợ) → tên + SĐT → chọn nhóm (1 hoặc nhiều) → tuỳ chọn thợ request (thấy số người đang chờ thợ đó — P1).
3. **Gán thợ:** máy quầy gợi ý: thợ rảnh có khách request → làm khách request trước (0 turn); còn lại → thợ ít turn nhất, bằng nhau thì check-in sớm hơn. Bấm **Assign** → hiện to tên thợ để gọi bộ đàm.
4. **Khách 2 nhóm:** phần nào có thợ rảnh trước làm trước; phần kia khoá cho tới khi phần đầu xong; bấm **Phần xong** → thợ đó rảnh ngay (P3), phần còn lại tự lên đầu hàng nhóm kia (P2).
5. **Xong:** bấm **Done** khi thợ dẫn khách xuống tính tiền → khách rời danh sách chính (P4). Thanh toán vẫn trên POS.
6. **Thợ vào trễ:** hệ thống nhắc manager chỉnh turn (M6).
7. **Cuối ngày:** báo cáo turn, lịch sử chỉnh sửa, khách & thời gian chờ. Sáng hôm sau reset.

## Còn cần hỏi
- M13: mục báo cáo "khác" là gì? *(để sau — hiện tập trung vào màn hình manager trong giờ mở cửa)*

## Prototype
- `prototype/front-desk.html`: màn hình thao tác của manager/lễ tân trong giờ mở cửa (dữ liệu mẫu).
