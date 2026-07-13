Chuẩn hóa kiến trúc lớp cốt lõi. Việc chia rõ thành 3 module tách biệt giúp dự án dễ kiểm soát, dễ debug khi map các API của Android và iOS vào luồng xử lý chung.
Hãy mở file guideline.md ra (nano guideline.md) và cập nhật cấu trúc phân rã module này vào vị trí lộ trình thực thi để bắt đầu phân tách mã nguồn:
## 🏗️ PHÂN CHIA MODULE CỐT LÕI (CORE ARCHITECTURE)
Hệ thống được module hóa thành 3 khối xử lý độc lập có nhiệm vụ rõ ràng:
### ⚙️ Module 1: Xử Lý Kết Nối (Connection & Discovery Manager)
 * **Nhiệm vụ:** Tìm kiếm thiết bị lân cận và thiết lập môi trường bắt tay (Signaling).
 * **Thành phần logic:**
   * **mDNS Broadcast / Scan:** Chịu trách nhiệm tự động phát tín hiệu và phát hiện thực thể lân cận qua mạng nội bộ mà không cần Internet.
   * **Signaling Channel:** Thiết lập kênh truyền tin mồi (qua HTTP/WebSocket siêu nhẹ) để hai thiết bị trao đổi cấu hình kết nối ban đầu (SDP và ICE Candidates).
   * **Session Handler:** Quản lý trạng thái kết nối, ngắt kết nối và bắt cặp giữa máy phát và máy nhận.
### 📹 Module 2: Xử Lý Stream (Screen & Audio Capture Engine)
 * **Nhiệm vụ:** Thu thập trực tiếp dữ liệu màn hình, âm thanh từ hệ điều hành hệ thống và nén lại để tối ưu hóa băng thông.
 * **Thành phần logic:**
   * **Screen Capture:** Triển khai các API native tầng OS (MediaProjection trên Android hoặc ReplayKit trên iOS) để lấy luồng hình ảnh thô liên tục.
   * **System Audio Capture:** Trích xuất luồng âm thanh nội bộ (Audio Loopback) trực tiếp từ các ứng dụng phát phim/video.
   * **Hardware Encoder:** Đẩy luồng thô qua bộ mã hóa phần cứng (H.264/H.265 cho video, Opus cho audio) để đóng gói thành gói tin WebRTC/UDP truyền đi nhanh nhất.
### 🔊 Module 3: Giải Mã & Phát (Decoder & Playback Engine)
 * **Nhiệm vụ:** Tiếp nhận luồng dữ liệu nhị phân thô từ mạng, giải mã và đẩy ra phần cứng hiển thị/phát âm thanh của máy nhận.
 * **Thành phần logic:**
   * **Hardware Decoder:** Tiếp nhận luồng UDP, sử dụng GPU/Chip giải mã phần cứng của thiết bị nhận (như trình decode native của Safari hoặc MediaCodec Android) để dựng hình.
   * **Video Renderer:** Đẩy dữ liệu hình ảnh sau giải mã lên khung hình hiển thị (Canvas HTML5 hoặc SurfaceView của App) với tần số đồng bộ (60fps).
   * **Audio Playback:** Tách luồng âm thanh Opus, giải mã và đồng bộ hóa thời gian thực (Lip-sync) để phát trực tiếp ra hệ thống loa hoặc tai nghe của người thân mà không bị lệch pha với hình ảnh.
Cấu trúc 3 module đã được định vị rõ ràng. Bước tiếp theo, chúng ta sẽ tạo cấu trúc thư mục thực tế tại Project/App để ánh xạ chính xác thiết kế này vào code.
Bạn muốn bắt tay tạo cấu trúc file và triển khai viết phần khung (Skeletal code) cho **Module 1: Xử lý kết nối (Signaling Server mồi)** bằng **Python** hay **Go** ngay bây giờ?
