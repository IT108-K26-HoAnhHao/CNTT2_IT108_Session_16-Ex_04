# BÀI 4

## I. GIẢI PHÁP KỸ THUẬT: CẤU HÌNH FILES ON-DEMAND

Để giải quyết triệt để tình trạng tràn bộ nhớ SSD 256GB khi đồng bộ khối dữ liệu CCTV 500GB, giải pháp tối ưu nhất là kích hoạt tính năng **Files On-Demand** (Cloud Files/Ứng dụng đồng bộ ngầm) theo các bước thiết lập dưới đây:

### Bước 1: Kích hoạt trạng thái đám mây ngầm (Cloud-Only)
*   **Thao tác:** Mở phần mềm đồng bộ trên máy tính (OneDrive / Google Drive Desktop). Vào mục **Settings (Cài đặt)** -> Chọn tab **Sync and backup (Đồng bộ và sao lưu)** -> Tìm mục **Advanced settings (Cài đặt nâng cao)**.
*   **Thiết lập:** Bật tính năng **Files On-Demand** bằng cách tick chọn *"Save space and download files as you use them"* (Tiết kiệm không gian và tải file khi sử dụng).

### Bước 2: Giải phóng dung lượng Local (Free up space)
*   **Thao tác:** Truy cập vào thư mục chứa dữ liệu video giám sát campus trên máy tính. Click chuột phải vào thư mục gốc `Campus_CCTV_Data`.
*   **Thiết lập:** Chọn lệnh **"Free up space" (Giải phóng không gian)**. Lúc này, hệ thống sẽ xóa toàn bộ các tệp video vật lý đang lưu trú trên ổ cứng SSD và chỉ giữ lại các "tệp tượng trưng" (Placeholder) với dung lượng bằng 0 byte.

### Bước 3: Nhận diện trực quan trạng thái tệp (Status Icons)
Designer/Lập trình viên cần phân biệt 3 trạng thái biểu tượng để kiểm soát dung lượng:
*  **Biểu tượng đám mây (Cloud-only):** File chỉ nằm trên Cloud, chiếm 0 byte trên SSD Local. Khi cần xem thực tế, chỉ cần double-click, file sẽ tự động tải xuống.
* **Biểu tượng tích xanh nền trắng (Locally available):** File tạm thời được tải xuống để mở xem. Khi đóng file, hệ thống sẽ tự động chuyển về dạng đám mây sau một khoảng thời gian để tiết kiệm bộ nhớ.
* **Biểu tượng tích xanh nền đặc (Always keep on this device):** Dành riêng cho các file rất quan trọng cần xem ngay cả khi không có mạng (không áp dụng cho thư mục video nặng này).

---

## II. BÁO CÁO DUNG LƯỢNG (CAPACITY & EFFICIENCY REPORT)

### 1. Bài toán chứng minh tính hiệu quả

**Thông số kỹ thuật:**
*   Tổng dung lượng dữ liệu CCTV trên Central Cloud ($V_{Cloud}$): $500 \text{ GB}$
*   Dung lượng ổ cứng máy tính cá nhân ($V_{Local\_SSD}$): $256 \text{ GB}$
*   Dung lượng khả dụng thực tế của hệ điều hành và phần mềm khác: $\approx 100 \text{ GB}$
*   Dung lượng trống còn lại dành cho dữ liệu dự án ($V_{Free\_SSD}$): $\approx 150 \text{ GB}$

**Chứng minh hiệu quả khi bật Files On-Demand:**
*   Khi chạy chế độ Files On-Demand, mỗi file video 5GB khi chưa mở chỉ tiêu tốn khoảng **~10 KB** dung lượng SSD cho dữ liệu đặc tả (Metadata bao gồm: Tên file, định dạng, mốc thời gian).
*   Giả sử thư mục chứa 100 file video tổng dung lượng 500GB:
$$\text{Dung lượng chiếm dụng thực tế trên SSD} = 100 \text{ file} \times 10 \text{ KB} = 1000 \text{ KB} \approx 1 \text{ MB}$$

**Kết luận:** Nhờ giải pháp Files On-Demand, hệ thống đã giúp lưu trữ tượng trưng lượng dữ liệu 500GB trên không gian ổ cứng chỉ tốn **1 MB**, giúp tiết kiệm **99.99%** tài nguyên bộ nhớ Local SSD và bảo vệ máy tính khỏi tình trạng sập nguồn hay treo app đồng bộ do tràn đĩa.

---

## III. GIẢI QUYẾT BẪY DỮ LIỆU: BẢO VỆ ĐƯỜNG TRUYỀN & KHÔNG ĐỨT ĐOẠN

**Tình huống bẫy:** *Dữ liệu video rất lớn, làm thế nào để đồng bộ lên Cloud mà không làm treo đường truyền Internet của cả nhà trọ hoặc bị đứt đoạn khi gập máy laptop?*

Nhóm sẽ áp dụng chiến lược 3 lớp sau để vượt qua bẫy dữ liệu này:

### 1. Giới hạn băng thông ngầm (Bandwidth Throttling)
*   **Giải pháp:** Vào cài đặt ứng dụng Cloud -> Mục **Network (Mạng)** hoặc **Bandwidth (Băng thông)**.
*   **Thực thi:** Giới hạn tốc độ tải lên (Upload Rate) ở mức cố định (ví dụ: `2 MB/s` hoặc `20 Mbps`). Việc này đảm bảo ứng dụng đồng bộ không chiếm dụng hết toàn bộ băng thông của router, giúp Internet nhà trọ luôn ổn định, không bị nghẽn mạng hay gây treo đường truyền tập thể.

### 2. Định thời gian đồng bộ lệch giờ cao điểm (Schedule Sync)
*   **Giải pháp:** Cấu hình ứng dụng hoặc sử dụng script để tạm dừng đồng bộ (Pause) vào ban ngày.
*   **Thực thi:** Chỉ cho phép kích hoạt tiến trình đồng bộ dữ liệu lớn vào ban đêm (từ 12h đêm đến 6h sáng hôm sau). Đây là thời điểm không ai sử dụng mạng, tối ưu hóa tốc độ tải lên cao nhất mà không ảnh hưởng tới bất kỳ ai.

### 3. Cấu hình trạng thái nguồn khi gập nắp máy (Lid-Close Power Policy)
*   **Giải pháp:** Ngăn chặn việc máy tính rơi vào trạng thái ngủ (Sleep) làm đứt đoạn tiến trình tải file khi người dùng gập màn hình laptop.
*   **Thực thi:** 
    *   *Trên Windows:* Vào `Control Panel` -> `Power Options` -> `Choose what closing the lid does` -> Tại cột **Plugged in (Khi cắm sạc)**, chuyển trạng thái thành **Do nothing (Không làm gì cả)**.
    *   *Quy trình bắt buộc:* Nhân viên cắm nguồn sạc máy tính, nhấn cho hệ thống chạy đồng bộ, sau đó có thể thoải mái gập nắp máy lại bỏ vào balo hoặc đặt trên bàn; máy tính vẫn chạy ngầm và tải video lên Cloud liên tục xuyên suốt mà không sợ bị ngắt quãng nửa chừng.
