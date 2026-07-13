Chốt phương án. Triết lý thiết kế cốt lõi của hệ thống này là **"Không rào cản" (Zero-friction)**: Người phát kích hoạt trong 1 chạm, người nhận kết nối trong 3 giây, hoạt động hoàn toàn offline mà không phụ thuộc vào bất kỳ hạ tầng đám mây nào.
Dưới đây là **Bản đặc tả kiến trúc cuối cùng (Final Architecture Specification)** cho ứng dụng chia sẻ khoảnh khắc real-time của bạn:
## 🎯 Triết Lý Sản Phẩm: "Nhanh - Chất Lượng - Đơn Giản"
 * **Nhanh (Độ trễ < 200ms):** Bỏ qua mô hình Server-Client truyền thống của WebSockets, ép luồng dữ liệu đi qua P2P (Peer-to-Peer) thuần túy.
 * **Chất lượng (60fps + Audio):** Sử dụng bộ mã hóa phần cứng (Hardware Acceleration) của chip thiết bị để truyền trực tiếp luồng phim/hình ảnh mà không làm nóng máy hay tốn pin.
 * **Đơn giản (Zero-configuration):** Tự động nhận diện thiết bị lân cận mà không cần nhập IP, không cần đăng nhập tài khoản.
## 🏗️ Tổng Hợp Kiến Trúc Kỹ Thuật Cuối Cùng
Hệ thống sẽ được xây dựng dựa trên 3 trụ cột công nghệ chính:
```
┌────────────────────────────────────────────────────────┐
│               ỨNG DỤNG LIVESHARE APP                   │
├───────────────────┬───────────────────┬────────────────┤
│ 1. KHÁM PHÁ (mDNS)│ 2. TRUYỀN TẢI     │ 3. CORE STREAM │
│ - Android: NSD    │    (WebRTC)       │ - Android: MP  │
│ - iOS: Bonjour    │ - Kết nối P2P UDP │ - iOS: ReplayKit│
└───────────────────┴───────────────────┴────────────────┘

```
### 1. Tầng Khám Phá Lân Cận (Discovery)
Sử dụng **mDNS** (Multicast DNS) tiêu chuẩn mạng quốc tế để vượt rào cản hệ điều hành:
 * **Android:** Sử dụng API NsdManager.
 * **iOS:** Sử dụng API NetService.
 * **Cơ chế:** Khi mở ứng dụng, thiết bị tự động "hét" lên mạng nội bộ (Wi-Fi LAN hoặc qua mạng Mesh Tailscale) để cập nhật danh sách thiết bị lân cận lên màn hình trong tích tắc.
### 2. Tầng Thu Thập Màn Hình & Âm Thanh (Core Engine)
Đảm bảo thu được cả hình ảnh và âm thanh hệ thống để cùng người thân xem phim:
 * **Android phát:** Kích hoạt MediaProjection để capture màn hình + AudioPlaybackCapture để lấy luồng âm thanh nội bộ của video.
 * **iOS phát:** Kích hoạt Broadcast Upload Extension thông qua framework ReplayKit để duy trì việc quay màn hình ngay cả khi người dùng thoát ứng dụng ra ngoài màn hình chính để mở phim.
### 3. Tầng Truyền Dẫn Thời Gian Thực (Transport)
 * Sử dụng **WebRTC** để thiết lập kết nối trực tiếp **Peer-to-Peer**. Dữ liệu hình ảnh mã hóa H.264/H.265 sẽ bắn trực tiếp qua sóng Wi-Fi từ máy này sang máy kia, không đi qua Internet.
 * **Mẹo tối ưu tối thượng cho iOS (Dạng MVP):** Để người thân dùng iOS không cần cài app phức tạp, máy phát (Android) có thể tự chạy một Signaling Server siêu nhẹ để máy iOS mở **Safari** lên quét mã QR là xem được luôn qua WebRTC native của trình duyệt.
## 🗺️ Luồng Trải Nghiệm Khách Hàng (UX) Hoàn Chỉnh
 1. **Kết nối:** Bạn và người thân bật Wi-Fi (hoặc kết nối chung mạng lưới Tailscale).
 2. **Khám phá:** Mở ứng dụng, danh sách thiết bị lân cận (ví dụ: *"Galaxy Tab S11 của Talon"*, *"iPhone của Mẹ"*) tự động hiển thị mà không cần quét thủ công.
 3. **Bắt đầu:** Ấn vào tên thiết bị -> Chấp nhận quyền hệ thống -> Khoảnh khắc của bạn lập tức được lan tỏa sang màn hình của người thân với chất lượng cao nhất và độ trễ gần như bằng không.
Bản blueprint đã sẵn sàng ngay tại thư mục Project/App của bạn. Bạn muốn bắt tay vào việc triển khai Module đầu tiên nào trước—**Module tự nhận diện thiết bị (mDNS)** bằng code mẫu hay **Module truyền dữ liệu WebRTC**?
