# Tài liệu tuần 1 — Máy chủ Dell PowerEdge / HPE DL, iDRAC/iLO, Firmware/BIOS, Hardware RAID
### 

---

# CHƯƠNG 1: Dòng máy chủ Dell PowerEdge & HPE ProLiant DL

## 1.1 Mục tiêu chương
Nắm được cách phân loại và đọc tên các dòng máy chủ Dell/HPE phổ biến, để khi nhìn vào một model bất kỳ có thể suy ra ngay: dạng máy, phân khúc, số CPU, thế hệ.

## 1.2 Lý thuyết

### a) Dell PowerEdge
- **3 nhóm hình dạng (form factor):**
  - **Rack (R)** — tối ưu hiệu năng, mật độ cho trung tâm dữ liệu.
  - **Tower (T)** — cho văn phòng nhỏ/chi nhánh, vận hành êm.
  - **Modular/Blade (M)** — mở rộng linh hoạt theo rack-scale.
- **Cách đọc tên model** (ví dụ R660, R760xa):
  - Chữ cái đầu = dạng máy (R/T/M…).
  - Số đầu = phân khúc/số CPU: 1–3 ≈ 1 CPU, 4–7 ≈ 2 CPU, 8 có thể 2 hoặc 4 CPU, 9 luôn 4 CPU.
  - Số thứ hai = thế hệ (generation) — R740 = Gen14, R660 = Gen16.
  - Hậu tố (xa, xs, xd…) = biến thể chuyên dụng (xa = GPU-dense, xd = nhiều khay ổ đĩa).
- **Dòng chuyên dụng khác:** XE (AI/GPU, HPC), XR (rugged — chịu nhiệt/bụi/rung), C-series (siêu quy mô).
- **Thế hệ hiện tại (2026):** Gen16/17 dùng Xeon 6 / EPYC Turin, CXL interconnect; Gen18 (Venice/Rubin) dự kiến cuối 2026.

### b) HPE ProLiant DL
- **DL = Density Line** (rack); anh em cùng dòng: **ML** (tower), **BL** (blade, nay thành Synergy), **SL** (HPC, nay thành Apollo).
- **Cách đọc tên** (ví dụ DL380 Gen11):
  - Số đầu = phân khúc (1 entry, 3 mid, 5/8 cao cấp).
  - "GenX" = thế hệ phần cứng — hiện phổ biến Gen10/Gen10 Plus/Gen11, đang chuyển sang Gen12 (Xeon 6, CXL).
- **DL380** là mẫu rack 2U hai socket tiêu chuẩn ngành, sản xuất liên tục hơn 20 năm; **Gen10 Plus** đổi socket LGA 4189, linh kiện không tương thích ngược với Gen10 thường.

Nguồn: [Dell — Servers Rack, Tower & Edge](https://www.dell.com/en-us/shop/scc/sc/servers) · [Wikipedia — List of PowerEdge servers](https://en.wikipedia.org/wiki/List_of_PowerEdge_servers) · [Dell — PowerEdge Rack & Tower Hardware/Software Portfolio](https://www.dell.com/support/kbdoc/en-us/000203855/poweredge-rack-and-tower-servers-hardware-and-software-portfolio-documentation-videos) · [Renewtech — HPE Server Naming Convention](https://www.renewtech.com/blog/hpe-server-naming-convention.html) · [Wikipedia — ProLiant](https://en.wikipedia.org/wiki/ProLiant) · [HPE — DL325 Gen11 Maintenance Guide](https://support.hpe.com/hpesc/public/docDisplay?docId=sd00002041en_us)

## 1.3 Lab thực hành
1. Tra Service Tag (Dell) hoặc serial (HPE) của ít nhất 1 máy chủ thực tế đang có sẵn (hoặc ảnh/thông số online) → xác định: hãng, dòng, thế hệ, số CPU tối đa.
2. Tải và đọc **QuickSpecs** chính thức của 1 model Dell (vd R650/R660) và 1 model HPE (vd DL380 Gen10/Gen11) → ghi lại: CPU hỗ trợ, RAM tối đa, số khe PCIe, số khay ổ đĩa.
3. Lập bảng so sánh nhanh Rack vs Tower vs Blade (kích thước, mật độ, use-case) áp dụng cho bối cảnh hosting/VPS.
4. Tra vòng đời hỗ trợ (End of Life/End of Service) của model đang dùng trong hạ tầng thực tế (nếu có quyền truy cập).

## 1.4 Checklist tìm hiểu thêm
- [ ] So sánh chi tiết Rack vs Tower vs Blade — ưu/nhược điểm thực tế.
- [ ] Đọc QuickSpecs của tối thiểu 2 model mỗi hãng.
- [ ] Tra EOL/EOS của các model đang vận hành.

---

# CHƯƠNG 2: Giao diện quản trị iDRAC (Dell) và iLO (HPE)

## 2.1 Mục tiêu chương
Hiểu vai trò của bộ điều khiển quản lý ngoài băng (out-of-band), biết các khu vực chính trong giao diện web, và tự cấu hình được truy cập ban đầu.

## 2.2 Lý thuyết

### a) iDRAC (Integrated Dell Remote Access Controller)
- Bộ điều khiển quản lý **ngoài băng**, tích hợp sẵn trên mainboard PowerEdge — quản lý được kể cả khi server tắt nguồn hoặc chưa cài OS.
- Phiên bản: iDRAC7/8 (đời cũ), **iDRAC9** (phổ biến — Redfish API, LDAP/AD, Smart Card, TLS 1.2, system lockdown), iDRAC10 (mới nhất, Gen17).
- Các tab chính trên web UI: **Overview/Dashboard**, **System → Inventory** (kiểm kê phần cứng/firmware), **Storage** (controller/đĩa), **Configuration** (virtual console, boot), **Maintenance** (cập nhật firmware, log), **iDRAC Settings** (mạng, user, bảo mật), **SupportAssist**.
- Cấu hình lần đầu: qua **Lifecycle Controller** (F10 lúc boot) hoặc BIOS F2 → iDRAC Settings; nên đặt IP/DNS, đổi mật khẩu mặc định, bật HTTPS/TLS.

### b) iLO (Integrated Lights-Out)
- Chức năng tương đương iDRAC: xem sức khỏe hệ thống, remote console, cập nhật firmware, log sự kiện.
- Phiên bản phổ biến: iLO 5 (Gen10/Gen10 Plus/Gen11), iLO 6/7 (Gen11/Gen12).
- Đặc trưng riêng: **HPE Active Health System (AHS)** ghi log chẩn đoán sự cố, kết hợp **HPE InfoSight** dự đoán lỗi; trang **Security** có dashboard chấm điểm cấu hình an toàn (Security State: Production/High Security/FIPS/CNSA).
- Cấu hình lần đầu: **F9** lúc POST → System Utilities → iLO Configuration, hoặc **F10** vào Intelligent Provisioning để cấu hình mạng/license/user.

Nguồn: [Dell — iDRAC GUI Features (video)](https://www.dell.com/support/contents/en-us/videos/videoplayer/idrac-gui-features/6336285310112) · [Dell — iDRAC9 User's Guide](https://www.dell.com/support/manuals/en-us/idrac9-lifecycle-controller-v4.x-series/idrac9_4.00.00.00_ug_new/overview-of-idrac) · [Dell — Configure iDRAC9 & Lifecycle Controller network settings](https://www.dell.com/support/kbdoc/en-us/000177212/dell-poweredge-how-to-configure-the-idrac9-and-the-lifecycle-controller-network-ip) · [HPE — iLO Document Overview](https://cdn.support.hpe.com/docs/display/public/ilo-server-lifecycle/index.html) · [HPE — iLO 5 User Guide](https://itpfdoc.hitachi.co.jp/manuals/ha8000v/hard/Gen10/iLO/880740-004_en.pdf) · [HPE — iLO 7 User Guide](https://itpfdoc.hitachi.co.jp/manuals/ha8000v/hard/Gen12/iLO/30-842B82CC-003.pdf)

## 2.3 Lab thực hành
1. Trên máy lab Dell: vào Lifecycle Controller (F10) hoặc BIOS (F2) → cấu hình IP tĩnh cho iDRAC, đổi mật khẩu mặc định, bật HTTPS.
2. Trên máy lab HPE (nếu có): vào System Utilities (F9) hoặc Intelligent Provisioning (F10) → cấu hình IP tĩnh cho iLO, đổi mật khẩu mặc định.
3. Đăng nhập web UI của cả hai (nếu có đủ máy) → dạo qua từng tab, ghi chú lại chức năng tương ứng giữa iDRAC và iLO (lập bảng đối chiếu song song).
4. Thử mở **Virtual Console** (KVM ảo) từ xa trên 1 trong 2 nền tảng.
5. Cấu hình cảnh báo email hoặc SNMP cơ bản.

## 2.4 Checklist tìm hiểu thêm
- [ ] So sánh license tier: iDRAC Basic/Express/Enterprise/Datacenter vs iLO Standard/Advanced — tính năng nào bị khóa (Virtual Media, Remote Console…).
- [ ] Tìm hiểu Redfish API (chuẩn DMTF) mà cả hai đều hỗ trợ.
- [ ] Bảng đối chiếu thuật ngữ/tab chức năng iDRAC ↔ iLO (dùng làm slide so sánh).

---

# CHƯƠNG 3: Nâng cấp Firmware & BIOS

## 3.1 Mục tiêu chương
Nắm các con đường cập nhật firmware/BIOS trên cả hai hãng, biết chọn cách phù hợp theo tình huống (có OS hay không, một máy hay nhiều máy).

## 3.2 Lý thuyết

### a) Trên Dell PowerEdge
1. **Qua iDRAC Web UI:** Maintenance → System Update → tải Dell Update Package (DUP) theo Service Tag → Upload & Install. Cập nhật iDRAC không làm gián đoạn OS/VM (chỉ tự khởi động lại iDRAC); cập nhật BIOS cần khởi động lại server.
2. **Qua Lifecycle Controller (F10, không cần OS):** Firmware Update → chọn nguồn (USB FAT32 / network share / Dell online catalog qua HTTPS) → Apply.
3. **Qua OS đang chạy:** Dell Repository Manager (DRM) để đóng gói, hoặc Dell System Update (DSU) dòng lệnh (`dsu --apply-upgrades --non-interactive`).
4. **Thứ tự khuyến nghị:** iDRAC → BIOS → RAID controller/driver OS; kiểm tra release notes vì có model cần "stepped upgrade".
5. Log lịch sử: iDRAC → Maintenance → Lifecycle Log.

### b) Trên HPE ProLiant DL
1. **Qua iLO Web UI:** cần quyền "Configure iLO Settings" — hỗ trợ iLO firmware, System ROM (BIOS), Power Management Controller, CPLD, NVMe backplane… SPS/Innovation Engine phải tắt hẳn server mới update được.
2. **Qua Smart Update Manager (SUM)** trong bộ **Service Pack for ProLiant (SPP)**: mount ISO → chạy `launch_sum.bat`/`.sh` → thêm node bằng IP/tài khoản iLO → "Localhost Guided Update" hoặc "Remote Node Update".
3. **In-band từ OS:** cần driver iLO trên host; chế độ Production không cần xác thực, High Security/FIPS/CNSA thì bắt buộc.
4. Firmware đóng gói dạng **Smart Component** (.fwpkg/.zip/.rpm/.exe), có chữ ký .compsig.
5. Kiểm tra sau khi cập nhật: F9 → System Information → Firmware Versions; hoặc iLO → Information → Overview.

Nguồn: [Dell — How to Update Firmware Remotely Using iDRAC Web Interface](https://www.dell.com/support/kbdoc/en-us/000134013/dell-poweredge-update-the-firmware-of-single-system-components-remotely-using-the-idrac) · [Dell — Update Firmware From LifeCycle Controller](https://www.dell.com/support/kbdoc/en-us/000128133/poweredge-server-lifecycle-controller-update) · [Dell — Recommended BIOS/iDRAC9 update methods](https://www.dell.com/support/kbdoc/en-us/000215873/update-poweredge-bios-and-idrac-for) · [HPE — Online firmware update, iLO 5 Guide](https://support.hpe.com/hpesc/public/docDisplay?docId=a00105236en_us) · [HPE Developer — Firmware updates Part 1](https://developer.hpe.com/blog/hpe-firmware-updates-part-1-file-types-and-smart-components/) · [IBM Support — Update iLO 5/server firmware](https://www.ibm.com/support/pages/how-update-ilo-5-or-server-firmware)

## 3.3 Lab thực hành
1. Trên máy lab Dell: cập nhật firmware iDRAC qua Web UI (Maintenance → System Update) bằng 1 file DUP tải sẵn.
2. Trên cùng máy: thử cách thứ hai — vào Lifecycle Controller (F10) và cập nhật qua USB hoặc network share, so sánh trải nghiệm với cách 1.
3. (Nếu có máy HPE) Mount SPP ISO, chạy SUM, thực hiện "Localhost Guided Update" và xuất log kết quả.
4. Ghi lại phiên bản firmware trước/sau khi cập nhật (BIOS version, iDRAC/iLO version) làm bằng chứng thực hành.
5. Thảo luận: điều gì xảy ra nếu mất điện giữa lúc đang flash BIOS? Có cơ chế rollback không?

## 3.4 Checklist tìm hiểu thêm
- [ ] Rủi ro khi nâng cấp firmware (brick BIOS, mất điện giữa chừng) và cách backup/rollback.
- [ ] Xây quy trình nội bộ: tần suất nâng cấp định kỳ, môi trường test trước khi lên production.
- [ ] Sự khác nhau giữa DUP (Dell) và Smart Component (HPE) về mặt đóng gói.

---

# CHƯƠNG 4: Hardware RAID — Tổng quan & Cấu hình RAID 0, RAID 1

## 4.1 Mục tiêu chương
Hiểu nguyên lý RAID 0/1, biết các thông số cấu hình quan trọng, và thực hiện được thao tác tạo virtual disk/logical drive trên cả PERC (Dell) và Smart Array (HPE).

## 4.2 Lý thuyết

### a) Khái niệm chung
- **Hardware RAID** dùng RAID controller vật lý (Dell PERC, HPE Smart Array/MR) có chip xử lý + cache riêng, gộp nhiều ổ đĩa vật lý thành **virtual disk/logical drive** — không tốn CPU của OS như software RAID.
- **RAID 0 (Striping):** dữ liệu chia nhỏ ghi xen kẽ trên tất cả ổ → tăng tốc độ, dung lượng = tổng các ổ, nhưng **không có dự phòng** (1 ổ hỏng = mất hết dữ liệu).
- **RAID 1 (Mirroring):** ghi giống hệt lên 2 ổ → dung lượng = 1 ổ, chịu được hỏng 1 ổ trong cặp; phù hợp cho ổ chứa OS.
- Thông số quan trọng khi tạo: **RAID level, strip size, read policy (Read Ahead/No), write policy (Write Back/Write Through)** — tùy controller hỗ trợ khác nhau (vd PERC H330 cố định 64KB/No Read Ahead/Write Through; H730/H830 tùy chỉnh nhiều hơn).

### b) Trên Dell (PERC)
- Vào **BIOS Configuration Utility**: khởi động lại → F2 (System Setup) → Device Settings → chọn PERC → Configuration Management → **Create Virtual Disks** (máy cũ hơn dùng Ctrl+R lúc POST).
- Các bước: chọn RAID level (0/1) → chọn ổ vật lý (phím Space) → chọn strip size/read-write policy nếu hỗ trợ → xác nhận tạo.
- Lưu ý: PERC S150 (RAID phần mềm tích hợp chipset) cần bật RAID Mode trong SATA Settings trước.

### c) Trên HPE (Smart Array/SSA)
- Vào **HPE Smart Storage Administrator (SSA)** qua Intelligent Provisioning: khởi động lại → **F10** → "Perform Maintenance"/"HPE Smart Storage Administrator" (chỉ ~15 giây để chọn).
- Trong SSA: chọn controller → **Configure/Create Array** → chọn ổ vật lý → chọn RAID level (RAID 1 nếu 2 ổ, RAID 0 nếu 1 ổ) → **Create Logical Drive**.
- Với ORCA/UEFI System Utilities (controller cũ), RAID mặc định theo số ổ: 1 ổ = RAID 0, 2 ổ = RAID 1(+0), 3–6 ổ = RAID 5.
- RAID 1 **không thể mở rộng** bằng cách thêm ổ vào mirror hiện có — phải tạo logical drive mới hoặc migrate lên RAID 10.

Nguồn: [Dell — Create RAID Using PERC H700 BIOS Utility](https://www.thegeekstuff.com/2012/03/dell-server-perc-h700-create-raid/) · [Dell — PERC 9 User's Guide — Creating Virtual Disks](https://www.dell.com/support/manuals/en-rs/poweredge-rc-h330/perc9ugpublication/creating-virtual-disks) · [Dell — RAID levels với Strip Size & Read/Write Policy (video)](https://www.dell.com/support/contents/en-us/videos/videoplayer/how-to-create-raid-levels-using-different-strip-sizes-and-readwrite-policies-for-dell-perc/6079806984001) · [HPE — Smart Array SR Gen10 Configuration Guide](https://www.hitachi.co.jp/products/it/ha8000v/download/manuals/hard/Gen10/SA/30-37CF4443-006_en.pdf) · [HPE — Configuring Arrays on HP Smart Array Controllers](https://h10032.www1.hp.com/ctg/Manual/c03476613.pdf) · [Keemotion — Rebuild HP RAID configuration (SSA walkthrough)](https://support.keemotion.com/hc/en-us/articles/360011008099-Disk-Array-Issue-Rebuild-HP-server-s-RAID-configuration)

## 4.3 Lab thực hành
1. Trên máy lab Dell: vào PERC BIOS Configuration Utility → tạo 1 Virtual Disk RAID 0 (tối thiểu 1–2 ổ) → xóa đi → tạo lại 1 Virtual Disk RAID 1 (2 ổ).
2. (Nếu có máy HPE) Vào SSA qua Intelligent Provisioning → tạo 1 Logical Drive RAID 1 tương tự.
3. So sánh dung lượng khả dụng hiển thị trước/sau khi tạo RAID 0 vs RAID 1 với cùng số ổ, cùng dung lượng mỗi ổ — ghi số liệu thực tế để minh họa trên slide.
4. Thử rút 1 ổ (nếu môi trường cho phép an toàn) trong cấu hình RAID 1 để quan sát trạng thái degraded, sau đó cắm lại và theo dõi quá trình rebuild.
5. Ghi lại các bước bằng ảnh chụp màn hình (mỗi bước 1 ảnh) — dùng trực tiếp làm slide minh họa quy trình.

## 4.4 Checklist tìm hiểu thêm
- [ ] RAID 5, RAID 6, RAID 10 — khác biệt hiệu năng/dung lượng/khả năng chịu lỗi so với RAID 0/1.
- [ ] Quy trình rebuild khi 1 ổ trong RAID 1/5 hỏng và được thay ổ mới (hot-swap).
- [ ] BBWC/FBWC (battery/flash-backed write cache) — vì sao cần thiết khi bật Write Back.
- [ ] Hardware RAID vs Software RAID (mdadm/ZFS) — khi nào nên dùng loại nào trong môi trường hosting/VPS.

---

## Gợi ý cấu trúc slide (tham khảo)
- 1 slide bìa + 1 slide mục lục (4 chương).
- Mỗi chương: 1 slide mục tiêu → 2–4 slide lý thuyết (mỗi slide 1 ý chính, hình minh họa nếu có) → 1–2 slide lab (ảnh chụp màn hình các bước) → 1 slide checklist/câu hỏi mở.
- 1 slide tổng kết + nguồn tham khảo ở cuối mỗi chương (hoặc gom lại 1 slide "Tài liệu tham khảo" cuối bài).

*Nguồn tổng hợp từ trang hỗ trợ chính thức của Dell (dell.com/support) và HPE (support.hpe.com), cùng một số tài liệu kỹ thuật/blog uy tín liên quan — liên kết đã đính kèm dưới mỗi chương.*
