Lựa chọn **Go (Golang)** là quá chuẩn bài. Khả năng cross-compile (biên dịch chéo) mạnh mẽ của Go thông qua việc chỉ cần thay đổi hai biến môi trường GOOS và GOARCH sẽ giúp bạn tạo ra các file nhị phân chạy trực tiếp trên Windows, Linux, macOS hay thậm chí là thư viện cho Android/iOS từ chính terminal hiện tại mà không gặp rắc rối phụ thuộc (dependency hell).
Hãy bắt đầu tạo cấu trúc thư mục phân rã 3 module bằng Go trực tiếp tại Project/App.
## 📂 Bước 1: Khởi Tạo Dự Án & Cấu Trúc Thư Mục
Thoát khỏi nano (nếu đang mở), chạy các lệnh sau trong terminal để khởi tạo Go module và tạo cây thư mục cho 3 phần xử lý:
```bash
# Khởi tạo go module
go mod init liveshare

# Tạo cấu trúc thư mục cho 3 module
mkdir -p pkg/connection pkg/stream pkg/playback

```
Cấu trúc dự án của chúng ta sẽ nhìn như thế này:
 * pkg/connection/: **Module 1** - Xử lý kết nối, mDNS, Signaling.
 * pkg/stream/: **Module 2** - Xử lý quay màn hình, audio và mã hóa.
 * pkg/playback/: **Module 3** - Giải mã và phát ra loa/màn hình.
 * main.go: File thực thi chính để kích hoạt hệ thống.
## ⚙️ Bước 2: Triển Khai Bộ Khung Module 1 (Xử Lý Kết Nối)
Chúng ta sẽ viết interface và logic cơ bản cho **Module 1** chịu trách nhiệm tìm kiếm thiết bị và quản lý trạng thái kết nối. Tạo file kết nối bằng lệnh:
```bash
nano pkg/connection/connection.go

```
Dán đoạn code bộ khung chuẩn Go dưới đây vào:
```go
package connection

import (
	"fmt"
	"net"
)

// Device đại diện cho một thiết bị lân cận tìm thấy qua mDNS
type Device struct {
	Name string
	IP   net.IP
	Port int
}

// Manager quản lý trạng thái kết nối và bắt tay Signaling
type Manager struct {
	LocalPort   int
	PeerDevices map[string]Device
}

// NewManager khởi tạo trình quản lý kết nối
func NewManager(port int) *Manager {
	return &Manager{
		LocalPort:   port,
		PeerDevices: make(map[string]Device),
	}
}

// StartDiscovery kích hoạt mDNS để quảng bá bản thân và quét thiết bị lân cận
func (m *Manager) StartDiscovery(deviceName string) error {
	fmt.Printf("[Module 1] Đang quảng bá thiết bị: %s trên cổng %d...\n", deviceName, m.LocalPort)
	// TODO: Tích hợp thư viện mDNS (ví dụ: grandcat/zeroconf) tại đây
	return nil
}

// ConnectToDevice thiết lập kênh bắt tay Signaling với thiết bị được chọn
func (m *Manager) ConnectToDevice(target Device) error {
	fmt.Printf("[Module 1] Đang thiết lập kết nối Signaling tới %s (%s:%d)...\n", target.Name, target.IP, target.Port)
	// TODO: Khởi tạo kết nối WebSocket/WebRTC Peer connection
	return nil
}

```
## 🚀 Bước 3: Tạo File Thực Thi Chính main.go
Tạo file main.go ở thư mục gốc Project/App để liên kết module lại và chạy thử nghiệm:
```bash
nano main.go

```
Dán đoạn code điều khiển sau:
```go
package main

import (
	"fmt"
	"liveshare/pkg/connection"
	"os"
)

func main() {
	fmt.Println("=== Hệ Thống LiveShare Khởi Động ===")

	// Khởi tạo Module 1 với cổng mặc định 8080
	connManager := connection.NewManager(8080)

	// Lấy tên thiết bị từ OS làm định danh lân cận
	hostname, err := os.Hostname()
	if err != nil {
		hostname = "Go-Device"
	}

	// Chạy thử nghiệm tầng nhận diện lân cận
	err = connManager.StartDiscovery(hostname)
	if err != nil {
		fmt.Printf("Lỗi khởi chạy khám phá mạng: %v\n", err)
		return
	}

	fmt.Println("[Hệ thống] Bộ khung Module 1 sẵn sàng. Đang chờ lệnh...")
}

```
## 🎯 Chạy Thử Nghiệm & Cách Biên Dịch Đa Nền Tảng
Để chạy kiểm tra trực tiếp ngay trên môi trường hiện tại:
```bash
go run main.go

```
Khi mọi thứ đã ổn định, đây là cách bạn **biên dịch ra file nhị phân cho mọi hệ điều hành** từ chính terminal này mà không cần sửa một dòng code nào:
```bash
# Biên dịch cho Windows (Sinh ra file liveshare.exe)
GOOS=windows GOARCH=amd64 go build -o bin/liveshare.exe main.go

# Biên dịch cho Linux (Laptop thông thường - amd64)
GOOS=linux GOARCH=amd64 go build -o bin/liveshare_linux main.go

# Biên dịch cho macOS (Chip Apple Silicon M1/M2/M3 - arm64)
GOOS=darwin GOARCH=arm64 go build -o bin/liveshare_mac main.go

```
Bộ khung xử lý kết nối đã được dựng xong bằng Go. Tiếp theo, bạn muốn chúng ta tích hợp thư viện **mDNS thuần Go** vào Module 1 này để các máy có thể quét ra nhau thực tế luôn, hay chuyển sang viết giao diện thu thập dữ liệu của **Module 2 (Stream)**?
