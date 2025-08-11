# TTTN-2025

Cách đóng gói imodule trong github
# Labtainers – Đóng gói IModule cho từng bài lab (publish lên Docker Hub trước)

 quy trình **đóng gói IModule** cho một bài lab Labtainers và cách tổ chức **mỗi lab trên một nhánh Git** để chỉ pack đúng lab cần thiết.

---

## 0) Yêu cầu & Chuẩn bị

- Đã cài Labtainers và chạy cập nhật `updatedesigner.sh`.
- Có script `create-imodules.sh` (mặc định ở: `~/labtainer/trunk/scripts/designer/bin/create-imodules.sh`).  
- Có tài khoản Docker Hub **và mật khẩu**.

**Thiết lập registry (bắt buộc trước khi publish):**  
Mở **labedit** → **Edit** → **config (registry)** → nhập **tên người dùng Docker Hub** (vd: `labattt2025`) → **Build & Run** lại để cập nhật cấu hình.

---

## 1)TL;DR

```bash
# Làm sạch lab (tùy chọn, khuyên dùng)
python3 ~/labtainer/trunk/scripts/designer/bin/cleanlab4svn.py

# Khởi tạo repo Git (nếu chưa có) tại thư mục labs
cd ~/labtainer/trunk/labs
git init

# Add & commit chỉ thư mục lab cần đóng gói
git add <lab-name>
git commit -m "Add <lab-name>"

# Publish image của lab lên Docker Hub
cd $LABTAINER_DIR/distrib
./publish.py -d -l <lab-name>

# Tạo IModule
~/labtainer/trunk/scripts/designer/bin/create-imodules.sh

# File đầu ra:
#   ~/labtainer/trunk/imodule.tar
```

Upload `imodule.tar` lên GitHub → **nhấn Raw** để lấy **link tải trực tiếp**, rồi phân phối cho sinh viên:
```bash
imodule <raw-url-tren-github>
```

---

## 2) Quy trình chi tiết cho 1 bài lab

### Bước 0: Cấu hình registry cho Docker Hub
- Trong **labedit** → **Edit** → **config (registry)** → nhập username Docker Hub → **Build & Run** lại.

### Bước 1: Dọn rác (khuyến nghị)
```bash
cd ~/labtainer/trunk/labs/<lab-name>
python3 ~/labtainer/trunk/scripts/designer/bin/cleanlab4svn.py
```

### Bước 2: Tạo/kiểm tra Git repo cục bộ ở `labs/`
```bash
cd ~/labtainer/trunk/labs
git init                    # bỏ qua nếu đã có .git
git add <lab-name>
git commit -m "Add <lab-name>"
git log --oneline           # xác nhận đã commit
```

> ⚠️ `create-imodules.sh` **chỉ pack các file đang được Git track** (`git ls-files`). Nếu không `git add` thì sẽ **không** được đóng gói.

### Bước 3: Publish image của lab lên Docker Hub
```bash
cd $LABTAINER_DIR/distrib
./publish.py -d -l <lab-name>
```
- Quá trình có thể lâu. Sẽ yêu cầu **username**/**password** Docker Hub.

### Bước 4: Tạo IModule
```bash
~/labtainer/trunk/scripts/designer/bin/create-imodules.sh
```
- Đầu ra: `~/labtainer/trunk/imodule.tar` (sẽ **bị ghi đè** mỗi lần chạy).

### Bước 5: Kiểm tra & phân phối
```bash
# Liệt kê nội dung tar (tùy chọn)
tar -tf ~/labtainer/trunk/imodule.tar
```
- Upload `imodule.tar` lên GitHub → mở file → bấm **Raw** → copy URL **Raw**.
- Hướng dẫn sinh viên:
```bash
imodule <raw-url-tren-github>
```

---

## 3) Đóng gói **chỉ 1 lab** bằng **Git branch riêng** (khuyến nghị)

`create-imodules.sh` sẽ pack **toàn bộ** lab đã commit trong **nhánh hiện tại**.  
Để chỉ pack **duy nhất 1 lab**, dùng **mỗi lab = 1 branch**:

```bash
# Tại ~/labtainer/trunk/labs
git branch -a
git checkout --orphan <lab-name>-branch   # nhánh orphan, không lịch sử
git rm -rf .                              # xóa index của nhánh (không xóa file ngoài đĩa)

# Thêm lại đúng lab cần pack
git add <lab-name>
git commit -m "Add <lab-name> only"

# Publish & tạo IModule
cd $LABTAINER_DIR/distrib
./publish.py -d -l <lab-name>

~/labtainer/trunk/scripts/designer/bin/create-imodules.sh
# => ~/labtainer/trunk/imodule.tar (chỉ chứa <lab-name>)
```

> Mỗi lab một branch giúp:
> - Dễ quản lý 10–20 labs độc lập.
> - Không cần xóa lab cũ; update lab nào thì checkout branch tương ứng, commit & chạy lại.
> - Đảm bảo `imodule.tar` chỉ chứa đúng lab của nhánh.

---

## 4) cheat-sheet

```bash
# Dọn lab
python3 ~/labtainer/trunk/scripts/designer/bin/cleanlab4svn.py

# Git tại ~/labtainer/trunk/labs
git init
git add <lab-name>
git commit -m "Add <lab-name>"
git checkout --orphan <lab-name>-branch
git rm -rf .
git add <lab-name>
git commit -m "Add <lab-name> only"

# Publish & tạo IModule
cd $LABTAINER_DIR/distrib
./publish.py -d -l <lab-name>
~/labtainer/trunk/scripts/designer/bin/create-imodules.sh

# Kiểm tra & phân phối
tar -tf ~/labtainer/trunk/imodule.tar
imodule <raw-url-tren-github>
```