

Nguồn tổng hợp: [Đề cương ôn tập Hackathon DevOps](https://huongcaoha.github.io/de_cuong_on_tap_hackathon_devops/).

Tài liệu gồm:

1. Linux VPS: thư mục, tệp tin, tìm kiếm và phân quyền.
2. Git Flow: quản lý nhánh, phát hành, vá lỗi và xử lý xung đột.
3. Nginx: cài đặt, cấu hình và triển khai website HTML.
4. UFW: quản lý tường lửa và quyền truy cập mạng.
5. Công cụ thực hành và 10 câu hỏi ôn tập.

**Quy ước:** Các ví dụ triển khai dùng Ubuntu/Debian, Bash và Nginx. IP, tên miền, tên nhánh và đường dẫn trong ví dụ cần thay theo môi trường thực tế. Các lệnh là tài liệu tham khảo để thực hành từng bước, không phải một script để chạy toàn bộ.

## 1. Linux VPS và thao tác với tệp tin

### 1.1. Cấu trúc thư mục Linux

Linux tổ chức hệ thống tệp dưới một thư mục gốc duy nhất là `/`.

| Đường dẫn | Vai trò |
|---|---|
| `/` | Gốc của hệ thống tệp |
| `/etc` | Cấu hình hệ thống và dịch vụ, như Nginx, SSH, UFW |
| `/var` | Dữ liệu thường xuyên thay đổi |
| `/var/www` | Vị trí thường dùng để lưu website |
| `/var/log` | Nhật ký hệ thống và ứng dụng |
| `/home` | Thư mục cá nhân của người dùng thông thường |
| `/root` | Thư mục cá nhân của tài khoản `root` |
| `/bin`, `/usr` | Chương trình, công cụ và tài nguyên hệ thống |
| `/tmp` | Tệp tạm; cách và thời điểm dọn dẹp phụ thuộc cấu hình |

Phân biệt:

- **Đường dẫn tuyệt đối:** bắt đầu từ `/`, ví dụ `/var/www/html/index.html`.
- **Đường dẫn tương đối:** tính từ thư mục hiện tại, ví dụ `assets/style.css`.
- `.` là thư mục hiện tại.
- `..` là thư mục cha.
- `~` là thư mục cá nhân của người dùng hiện tại.

`/`, `/root` và tài khoản `root` là ba khái niệm khác nhau.

### 1.2. Xác định vị trí và xem danh sách tệp

```bash
# Xem đường dẫn thư mục hiện tại
pwd

# Chuyển đến một thư mục
cd /var/www/html

# Lùi về thư mục cha
cd ..

# Về thư mục cá nhân
cd ~

# Quay lại thư mục vừa đứng trước đó
cd -

# Liệt kê chi tiết, gồm cả tệp ẩn
ls -la

# Hiển thị kích thước dễ đọc
ls -lh

# Sắp theo thời gian sửa đổi, cũ trước và mới sau
ls -ltr

# Kết hợp thông tin chi tiết, tệp ẩn và kích thước dễ đọc
ls -lah /var/www/html
```

Các cờ của `ls`:

| Cờ | Ý nghĩa |
|---|---|
| `-l` | Hiển thị thông tin chi tiết |
| `-a` | Hiển thị cả tên bắt đầu bằng dấu `.`, như `.env`, `.git` |
| `-h` | Hiển thị kích thước dễ đọc khi kết hợp với chế độ phù hợp |
| `-t` | Sắp theo thời gian sửa đổi, mới trước |
| `-r` | Đảo ngược thứ tự |

### 1.3. Tạo, đọc và chỉnh sửa tệp

| Lệnh | Ví dụ | Công dụng |
|---|---|---|
| `touch` | `touch app.js index.html` | Tạo tệp rỗng nếu chưa tồn tại; cập nhật thời gian nếu đã tồn tại |
| `mkdir -p` | `mkdir -p project/assets/css` | Tạo thư mục và các thư mục cha còn thiếu |
| `cat` | `cat index.html` | In toàn bộ nội dung |
| `less` | `less /var/log/syslog` | Đọc tệp dài theo từng màn hình |
| `head -n` | `head -n 20 error.log` | Đọc 20 dòng đầu |
| `tail -n` | `tail -n 30 error.log` | Đọc 30 dòng cuối |
| `tail -f` | `tail -f /var/log/nginx/access.log` | Theo dõi các dòng mới được ghi |
| `nano` | `sudo nano /etc/nginx/nginx.conf` | Chỉnh sửa bằng Nano |
| `vim` | `vim index.html` | Chỉnh sửa bằng Vim |

**Phím cần nhớ:**

| Công cụ | Thao tác |
|---|---|
| `less` | Phím mũi tên hoặc Space để di chuyển; `/từ_khóa` để tìm; `q` để thoát |
| `tail -f` | `Ctrl + C` để dừng theo dõi |
| Nano | `Ctrl + O`, Enter để lưu; `Ctrl + X` để thoát |
| Vim | `i` để nhập; `Esc` về chế độ Normal; `:wq` để lưu và thoát; `:q!` để thoát không lưu |

### 1.4. Sao chép, di chuyển, đổi tên và xóa

```bash
# Sao lưu một tệp
cp index.html index.html.bak

# Sao chép thư mục cùng nội dung bên trong
cp -r /my-app /var/www/my-app

# Đổi tên tệp
mv old-config.conf new-config.conf

# Di chuyển tệp
mv main.js /var/www/html/js/

# Xóa một tệp
rm temp.txt

# Xóa thư mục và nội dung bên trong
rm -r /var/www/old-website

# Xóa đệ quy, không hỏi xác nhận thông thường
rm -rf /var/www/old-website
```

- `-r`: thao tác đệ quy với thư mục con.
- `-f`: bỏ qua một số tình huống lỗi và không hỏi xác nhận thông thường.
- `rm` không đưa dữ liệu vào thùng rác. Kiểm tra đường dẫn và nội dung trước khi xóa.

### 1.5. Tìm kiếm và lọc dữ liệu

**Tìm tệp bằng `find`:**

```bash
# Tìm các tệp cấu hình trong /etc
find /etc -type f -name "*.conf"

# Tìm tệp lớn hơn 100 MiB trong thư mục log
find /var/log -type f -size +100M
```

| Thành phần | Ý nghĩa |
|---|---|
| `-type f` | Chỉ tìm tệp thông thường |
| `-type d` | Chỉ tìm thư mục |
| `-name "*.conf"` | Tìm theo mẫu tên |
| `-size +100M` | Lọc theo kích thước lớn hơn ngưỡng |

**Tìm nội dung bằng `grep`:**

```bash
# Tìm server_name trong cấu hình Nginx
grep -rni "server_name" /etc/nginx/
```

- `-r`: tìm trong cả thư mục con.
- `-n`: hiển thị số dòng.
- `-i`: không phân biệt chữ hoa, chữ thường.

**Kết hợp bằng pipe `|`:**

Pipe chuyển đầu ra tiêu chuẩn của lệnh bên trái thành đầu vào của lệnh bên phải.

```bash
# Lọc các dòng tiến trình có chữ nginx
ps aux | grep "nginx"

# Đếm dòng log chứa chuỗi " 404 "
cat /var/log/nginx/access.log | grep " 404 " | wc -l
```

Lưu ý: ví dụ đếm lỗi 404 là cách lọc văn bản đơn giản; kết quả phụ thuộc định dạng log. `ps aux | grep nginx` cũng có thể hiển thị chính tiến trình `grep`.

### 1.6. Phân quyền và chủ sở hữu

Linux chia quyền thành ba nhóm:

| Ký hiệu | Nhóm |
|---|---|
| `u` | Owner: chủ sở hữu |
| `g` | Group: nhóm sở hữu |
| `o` | Others: những người dùng còn lại |

Ví dụ:

```text
-rwxr-xr--
│└─┬─┘└┬┘└┬┘
│  │   │  └── Others: r--
│  │   └───── Group: r-x
│  └───────── Owner: rwx
└──────────── Loại tệp
```

Ký tự đầu thường gặp:

- `-`: tệp thông thường.
- `d`: thư mục.
- `l`: liên kết mềm.

**Quy đổi quyền:**

| Quyền | Giá trị | Với tệp | Với thư mục |
|---|---:|---|---|
| `r` | 4 | Đọc nội dung | Liệt kê tên bên trong |
| `w` | 2 | Sửa nội dung | Tạo, xóa, đổi tên mục bên trong khi có đủ quyền cần thiết |
| `x` | 1 | Thực thi | Đi xuyên qua hoặc truy cập thư mục |
| `-` | 0 | Không cấp quyền tương ứng | Không cấp quyền tương ứng |

Mỗi chữ số là tổng quyền của một nhóm:

- `7 = 4 + 2 + 1 = rwx`.
- `6 = 4 + 2 = rw-`.
- `5 = 4 + 1 = r-x`.
- `4 = r--`.

| Mã | Ký hiệu | Cách dùng thường gặp |
|---|---|---|
| `755` | `rwxr-xr-x` | Thư mục web, chương trình hoặc script cần thực thi |
| `644` | `rw-r--r--` | HTML, CSS, JavaScript, ảnh |
| `600` | `rw-------` | Tệp riêng tư chỉ chủ sở hữu được đọc và ghi |
| `400` | `r--------` | Tệp riêng tư chỉ chủ sở hữu được đọc |
| `777` | `rwxrwxrwx` | Mọi nhóm có đủ quyền; không dùng như cách sửa lỗi quyền chung |

```bash
# Đổi quyền thư mục
chmod 755 /var/www/html

# Đổi quyền tệp HTML
chmod 644 /var/www/html/index.html

# Thêm quyền thực thi cho script
chmod +x deploy.sh

# Đổi chủ sở hữu và nhóm cho cả cây thư mục
sudo chown -R www-data:www-data /var/www/html
```

Phân biệt:

- `chmod`: đổi quyền truy cập.
- `chown`: đổi chủ sở hữu và có thể đổi cả nhóm.
- `-R`: áp dụng đệ quy.

**Điểm cần hiểu đúng:** trình duyệt của khách không trực tiếp dùng quyền `others` để đọc ổ đĩa VPS. Tiến trình Nginx đọc tệp bằng tài khoản hệ thống của nó. Nginx cần quyền đọc tệp và đi qua các thư mục cha; không bắt buộc phải sở hữu toàn bộ website.

### 1.7. Nén, giải nén, dung lượng và liên kết mềm

```bash
# Tạo bản nén gzip
tar -czvf backup-website.tar.gz /var/www/html

# Chuẩn bị thư mục đích
mkdir -p /var/www/restored

# Giải nén vào thư mục đích
tar -xzvf backup-website.tar.gz -C /var/www/restored/

# Xem dung lượng các filesystem
df -h

# Xem tổng dung lượng của từng mục trong thư mục log
du -sh /var/log/*

# Tạo liên kết mềm để kích hoạt site Nginx
sudo ln -s /etc/nginx/sites-available/mysite.conf /etc/nginx/sites-enabled/
```

Các cờ của `tar`:

| Cờ | Ý nghĩa |
|---|---|
| `-c` | Tạo archive |
| `-x` | Giải nén archive |
| `-z` | Dùng gzip |
| `-v` | Hiển thị tên đang xử lý |
| `-f` | Chỉ định tên archive |
| `-C` | Chuyển sang thư mục đích trước khi xử lý |

`df` cho biết dung lượng filesystem; `du` đo dung lượng tệp và thư mục. Khi nén đường dẫn tuyệt đối như ví dụ, archive có thể giữ các thành phần `var/www/html`; lúc giải nén cần chú ý cấu trúc này.

## 2. Git Flow và xử lý xung đột

### 2.1. Git Flow là gì?

Git Flow là mô hình tổ chức nhánh, tách công việc phát triển tính năng, chuẩn bị phát hành và sửa lỗi khẩn cấp.

Mô hình này phù hợp với dự án có các đợt phát hành rõ ràng. Đây là một lựa chọn quy trình làm việc, không phải yêu cầu bắt buộc cho mọi dự án Git.

### 2.2. Năm loại nhánh

| Nhánh | Tách từ | Hợp nhất về | Vai trò |
|---|---|---|---|
| `main` hoặc `master` | Nhánh chính ban đầu | Nhận release và hotfix | Theo dõi mã nguồn phát hành |
| `develop` | `main` | Nhận feature, thay đổi từ release và hotfix | Tích hợp cho phiên bản tiếp theo |
| `feature/*` | `develop` | `develop` | Phát triển tính năng |
| `release/*` | `develop` | `main` và `develop` | Hoàn thiện phiên bản trước phát hành |
| `hotfix/*` | `main` | `main` và `develop` | Sửa lỗi khẩn cấp của bản phát hành |

`main` và `develop` thường tồn tại lâu dài. Các nhánh còn lại thường được xóa sau khi tích hợp xong.

Tag như `v1.0.0` dùng để đánh dấu phiên bản phát hành; không phải mọi commit bất kỳ trên `main` đều tự động có tag.

### 2.3. Khởi tạo nhánh develop

Các ví dụ sau giả định repository đã có nhánh `main`, ít nhất một commit và remote tên `origin`.

```bash
git checkout -b develop main
git push -u origin develop
```

- `checkout -b`: tạo nhánh và chuyển sang nhánh đó.
- `-u`: thiết lập nhánh remote được theo dõi.

Nếu đã cài tiện ích Git Flow, có thể khởi tạo quy ước bằng:

```bash
git flow init
```

`git flow` là tiện ích bổ sung, không có sẵn trong mọi bản cài Git.

### 2.4. Quy trình feature

```bash
# Cập nhật develop
git checkout develop
git pull origin develop

# Tạo nhánh tính năng
git checkout -b feature/auth develop

# Sau khi sửa và kiểm tra mã nguồn
git add .
git commit -m "feat: hoàn thành chức năng đăng nhập"

# Tích hợp vào develop
git checkout develop
git pull origin develop
git merge --no-ff feature/auth

# Đẩy kết quả
git push origin develop

# Xóa nhánh local đã tích hợp
git branch -d feature/auth
```

`--no-ff` tạo merge commit khi thực hiện hợp nhất thay vì chỉ di chuyển con trỏ nhánh theo kiểu fast-forward. Điều này giúp nhận ra ranh giới của một tính năng trong lịch sử; không ngăn được conflict. [Tài liệu Git merge](https://git-scm.com/docs/git-merge).

### 2.5. Quy trình release

```bash
# Cập nhật develop và tạo nhánh release
git checkout develop
git pull origin develop
git checkout -b release/v1.0.0 develop

# Sau khi chỉnh phiên bản, tài liệu hoặc sửa lỗi
git add .
git commit -m "chore: chuẩn bị phát hành v1.0.0"

# Hợp nhất vào main
git checkout main
git pull origin main
git merge --no-ff release/v1.0.0

# Tạo tag
git tag -a v1.0.0 -m "Release v1.0.0"

# Đưa các chỉnh sửa lúc release trở lại develop
git checkout develop
git pull origin develop
git merge --no-ff release/v1.0.0

# Đẩy hai nhánh và tag phiên bản
git push origin main
git push origin develop
git push origin v1.0.0

# Xóa nhánh release local
git branch -d release/v1.0.0
```

Trong giai đoạn release, tập trung sửa lỗi, hoàn thiện tài liệu và số phiên bản; hạn chế bổ sung tính năng mới.

Website dùng `git push origin main --tags`. Lệnh đó đẩy `main` cùng tất cả tag local; ví dụ trên đẩy riêng tag cần phát hành.

### 2.6. Quy trình hotfix

```bash
# Cập nhật main rồi tách nhánh sửa lỗi
git checkout main
git pull origin main
git checkout -b hotfix/v1.0.1 main

# Sau khi sửa lỗi và kiểm tra
git add .
git commit -m "fix: sửa lỗi trên bản phát hành"

# Hợp nhất bản vá vào main
git checkout main
git merge --no-ff hotfix/v1.0.1
git tag -a v1.0.1 -m "Hotfix v1.0.1"

# Đồng bộ bản vá sang develop
git checkout develop
git pull origin develop
git merge --no-ff hotfix/v1.0.1

# Đẩy kết quả
git push origin main
git push origin develop
git push origin v1.0.1

# Xóa nhánh hotfix local
git branch -d hotfix/v1.0.1
```

Điểm quan trọng là đưa bản vá về luồng phát triển để các phiên bản sau tiếp tục chứa phần sửa lỗi.

### 2.7. Các lệnh Git bổ trợ

```bash
# Xem trạng thái repository
git status

# Xem lịch sử và quan hệ giữa các nhánh
git log --oneline --graph --all --decorate

# Cất tạm thay đổi đang làm
git stash

# Áp dụng lại thay đổi đã cất
git stash pop

# Liệt kê nhánh local và remote-tracking
git branch -a

# Xóa nhánh trên remote
git push origin --delete feature/auth
```

Lưu ý:

- `git stash` mặc định không cất tệp chưa được theo dõi; có thể dùng `git stash -u` khi cần.
- `git stash pop` có thể phát sinh xung đột.
- `git commit -am "..."` trong tài liệu gốc chỉ tự đưa vào commit các thay đổi của tệp đã được theo dõi; tệp mới vẫn cần `git add`.
- Cần xem lại nội dung trước khi dùng `git add .` để tránh đưa nhầm tệp vào commit.

### 2.8. Xử lý merge conflict

Conflict xảy ra khi Git không thể tự kết hợp thay đổi, chẳng hạn hai nhánh sửa cùng một vùng nội dung hoặc một nhánh sửa tệp mà nhánh kia xóa.

Ví dụ:

```bash
git checkout develop
git merge --no-ff feature/header
```

Tệp xung đột có thể chứa:

```text
<<<<<<< HEAD
Nội dung của nhánh hiện tại
=======
Nội dung của nhánh đang được hợp nhất
>>>>>>> feature/header
```

Quy trình xử lý:

1. Chạy `git status` để tìm tệp xung đột.
2. Mở tệp, chọn nội dung cần giữ hoặc kết hợp hai phía.
3. Xóa toàn bộ conflict marker.
4. Kiểm tra lại nội dung và hoạt động của mã nguồn.
5. Đưa tệp đã xử lý vào staging rồi commit.

```bash
git status
nano index.html

# Sau khi sửa xong
git add index.html
git commit -m "merge: xử lý xung đột index.html"
git push origin develop
```

Nếu cần hủy lần merge đang dang dở:

```bash
git merge --abort
```

**Làm rõ so với website:** `--abort` cố gắng khôi phục trạng thái trước merge, nhưng không bảo đảm khôi phục đầy đủ mọi thay đổi chưa commit trong mọi tình huống. Nên commit hoặc stash công việc trước khi merge. [Tài liệu Git về hủy merge](https://git-scm.com/docs/git-merge).

## 3. Nginx và triển khai website HTML

### 3.1. Nginx là gì?

Nginx có ba vai trò chính:

| Vai trò | Công dụng |
|---|---|
| Web server | Phục vụ HTML, CSS, JavaScript, ảnh và các tệp tĩnh |
| Reverse proxy | Nhận request rồi chuyển tiếp đến backend |
| Load balancer | Phân phối request giữa nhiều backend |

Nginx dùng kiến trúc xử lý hướng sự kiện, giúp các worker quản lý nhiều kết nối đồng thời.

Luồng phục vụ website tĩnh:

```text
Trình duyệt
    → Kết nối tới Nginx
    → Chọn server block
    → Chọn location và ánh xạ đường dẫn
    → Đọc tệp trong web root
    → Trả phản hồi HTTP
```

### 3.2. Cài đặt và quản lý dịch vụ

```bash
# Cập nhật danh sách gói
sudo apt update

# Cài Nginx
sudo apt install -y nginx

# Khởi động dịch vụ
sudo systemctl start nginx

# Cho phép khởi động cùng hệ thống
sudo systemctl enable nginx

# Kiểm tra trạng thái
sudo systemctl status nginx

# Kiểm tra cấu hình
sudo nginx -t

# Nạp lại cấu hình
sudo systemctl reload nginx

# Khởi động lại dịch vụ
sudo systemctl restart nginx

# Dừng dịch vụ
sudo systemctl stop nginx
```

Phân biệt:

- **Reload:** nạp cấu hình mới, chuyển dần sang worker mới và cho worker cũ hoàn tất công việc.
- **Restart:** dừng rồi khởi động lại dịch vụ; có thể gây gián đoạn.

Luôn chạy `sudo nginx -t` và xử lý lỗi trước khi reload.

### 3.3. Thư mục và tệp quan trọng

Các đường dẫn sau thường gặp khi cài bằng gói trên Ubuntu/Debian:

| Đường dẫn | Công dụng |
|---|---|
| `/etc/nginx/nginx.conf` | Cấu hình chính |
| `/etc/nginx/sites-available/` | Lưu cấu hình các website |
| `/etc/nginx/sites-enabled/` | Thường chứa symlink đến cấu hình được bật |
| `/var/www/html/` | Web root mặc định thường gặp |
| `/var/log/nginx/access.log` | Nhật ký truy cập |
| `/var/log/nginx/error.log` | Nhật ký lỗi |

Nginx chỉ nạp các tệp được cấu hình `include`; cấu trúc `sites-available` và `sites-enabled` không phải quy định bắt buộc cho mọi bản cài.

### 3.4. Cấu hình server block cho website tĩnh

Ví dụ `/etc/nginx/sites-available/my-static-web.conf`:

```nginx
server {
    listen 80;
    listen [::]:80;

    server_name example.com www.example.com;

    root /var/www/my-static-web;
    index index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~* \.(css|js|jpg|jpeg|png|gif|ico|svg|woff2)$ {
        expires 30d;
        add_header Cache-Control "public, no-transform";
    }

    error_page 404 /404.html;

    location = /404.html {
        internal;
    }
}
```

Thay `example.com` bằng tên miền của bạn. Khi sử dụng trang lỗi tùy chỉnh, cần tạo `404.html` trong web root.

| Chỉ thị | Ý nghĩa |
|---|---|
| `listen 80` | Nhận kết nối HTTP trên cổng 80 |
| `listen [::]:80` | Lắng nghe trên IPv6 |
| `server_name` | Tên miền để chọn website |
| `root` | Thư mục chứa nội dung |
| `index` | Các tên tệp mặc định khi truy cập thư mục |
| `location /` | Khối xử lý đường dẫn |
| `try_files` | Thử tệp, thư mục rồi trả lỗi hoặc chuyển hướng nội bộ |
| `expires 30d` | Thiết lập thời hạn cache |
| `add_header` | Thêm header phản hồi |
| `error_page` | Khai báo trang xử lý lỗi |
| `internal` | Chỉ cho phép truy cập location qua xử lý nội bộ |

Với HTML thông thường:

```nginx
try_files $uri $uri/ =404;
```

Với ứng dụng SPA dùng định tuyến phía trình duyệt, có thể cần:

```nginx
try_files $uri $uri/ /index.html;
```

**Làm rõ về `server_name _`:** dấu `_` không phải wildcard nhận mọi tên miền. Request không khớp tên được xử lý bởi server mặc định của địa chỉ/cổng đó. Muốn chỉ định rõ, dùng `default_server` trong `listen`. [Cách Nginx chọn server](https://nginx.org/en/docs/http/request_processing.html).

### 3.5. Quy trình triển khai HTML có sẵn trên VPS

**Bước 1 — Kết nối và chuẩn bị Nginx**

```bash
ssh user@IP_VPS
```

Trên VPS:

```bash
sudo apt update
sudo apt install -y nginx
sudo systemctl start nginx
sudo systemctl enable nginx
sudo systemctl status nginx
```

**Bước 2 — Tạo web root**

```bash
sudo mkdir -p /var/www/my-static-web
```

**Bước 3 — Sao chép nội dung website**

Nếu nội dung công khai đã nằm trong `~/my-website/`:

```bash
sudo cp -r ~/my-website/* /var/www/my-static-web/
ls -la /var/www/my-static-web
```

Dấu `*` không bao gồm các tệp ẩn. Chỉ triển khai các tài nguyên cần công khai; không đưa `.env`, khóa riêng hoặc dữ liệu bí mật vào web root.

Nếu chưa có trang HTML, có thể tạo trang thử:

```bash
sudo nano /var/www/my-static-web/index.html
```

Nội dung mẫu:

```html
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <title>DevOps thực hành</title>
</head>
<body>
    <h1>Triển khai Nginx thành công</h1>
    <p>Website HTML đang được phục vụ từ VPS.</p>
</body>
</html>
```

**Bước 4 — Đặt quyền thư mục và tệp**

```bash
sudo find /var/www/my-static-web -type d -exec chmod 755 {} \;
sudo find /var/www/my-static-web -type f -exec chmod 644 {} \;
```

Website minh họa cách đổi chủ sở hữu:

```bash
sudo chown -R www-data:www-data /var/www/my-static-web
```

Đây là một lựa chọn sở hữu, không phải điều kiện bắt buộc để Nginx đọc tệp. Có thể để tài khoản triển khai sở hữu và cấp quyền đọc phù hợp cho Nginx.

Bản tổng hợp tách quyền thư mục `755` và tệp `644`, thay vì đặt toàn bộ cây thành `755`.

**Bước 5 — Tạo cấu hình website**

```bash
sudo nano /etc/nginx/sites-available/my-static-web.conf
```

Với VPS thực hành chỉ dùng một website mặc định:

```nginx
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    server_name _;

    root /var/www/my-static-web;
    index index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

Mỗi địa chỉ/cổng chỉ được có một server mặc định tương ứng. Nếu cấu hình mặc định đi kèm gói vẫn đang bật và không cần dùng, kiểm tra rồi gỡ liên kết của nó:

```bash
ls -l /etc/nginx/sites-enabled/
sudo rm /etc/nginx/sites-enabled/default
```

Lệnh `rm` ở đây chỉ phù hợp khi mục `default` đúng là cấu hình mặc định cần vô hiệu hóa.

**Bước 6 — Kích hoạt website**

```bash
sudo ln -s /etc/nginx/sites-available/my-static-web.conf /etc/nginx/sites-enabled/
```

Nếu liên kết đã tồn tại, kiểm tra đích của liên kết thay vì tạo lặp lại.

**Bước 7 — Kiểm tra và nạp cấu hình**

```bash
sudo nginx -t
sudo systemctl reload nginx
```

Chỉ chạy lệnh reload sau khi kiểm tra thành công.

**Bước 8 — Mở cổng và kiểm tra truy cập**

Cấu hình UFW theo phần 4, đồng thời kiểm tra firewall của nhà cung cấp VPS nếu có.

Mở trình duyệt tại `http://IP_VPS`, hoặc kiểm tra ngay trên VPS:

```bash
curl http://localhost
```

Đối với tên miền, cần cấu hình DNS trỏ về VPS. Mở cổng 443 chưa tự tạo HTTPS; còn cần chứng chỉ và cấu hình TLS.

### 3.6. Khắc phục lỗi thường gặp

| Hiện tượng | Nguyên nhân thường gặp | Hướng kiểm tra |
|---|---|---|
| `403 Forbidden` | Thiếu quyền đọc/truy cập thư mục, thiếu tệp index hoặc bị cấu hình từ chối | Quyền tệp, quyền thư mục cha, `index`, error log |
| `404 Not Found` | Sai URL, sai `root`, thiếu tệp hoặc sai định tuyến | Đường dẫn thực tế và `try_files` |
| `502 Bad Gateway` | Nginx làm proxy nhưng không giao tiếp được với backend | Tiến trình backend, cổng, địa chỉ upstream và log |
| Vẫn thấy trang mặc định | Request đi vào server block khác | `server_name`, `default_server`, site đã bật |
| Không kết nối được | Dịch vụ dừng, cổng chưa mở, sai IP hoặc DNS | Trạng thái Nginx, UFW, firewall của VPS |
| Cấu hình chưa có hiệu lực | Chưa bật site, chưa reload hoặc kiểm tra cấu hình thất bại | Symlink, kết quả `nginx -t`, reload |

```bash
# Xem log lỗi gần nhất
sudo tail -n 20 /var/log/nginx/error.log

# Theo dõi lượt truy cập
sudo tail -f /var/log/nginx/access.log

# Kiểm tra backend trong ví dụ dùng cổng 3000
curl http://localhost:3000
```

Lỗi 502 chủ yếu liên quan đến giao tiếp với backend, không phải lỗi thông thường của việc đọc một tệp HTML tĩnh.

## 4. Tường lửa UFW

### 4.1. UFW là gì?

UFW là công cụ dòng lệnh giúp quản lý tường lửa Linux bằng các quy tắc dễ đọc.

Một chính sách thường dùng:

- Chặn kết nối đi vào nếu chưa được cho phép.
- Cho phép kết nối đi ra.
- Chỉ mở những dịch vụ cần thiết.

UFW giúp hạn chế truy cập mạng, nhưng không thay thế một hệ thống chống DDoS đầy đủ.

### 4.2. Kiểm tra và điều khiển

```bash
# Xem trạng thái
sudo ufw status

# Xem trạng thái và chính sách chi tiết
sudo ufw status verbose

# Nạp lại
sudo ufw reload

# Tắt tường lửa
sudo ufw disable

# Bật tường lửa
sudo ufw enable
```

`inactive` nghĩa là chưa hoạt động; `active` nghĩa là đang hoạt động.

**Phải cho phép cổng SSH thực tế trước khi bật UFW**, như trình tự bên dưới.

### 4.3. Mở SSH trước khi bật UFW

Nếu SSH đang dùng cổng mặc định 22:

```bash
sudo ufw allow 22/tcp
sudo ufw enable
sudo ufw status verbose
```

Một cách viết khác:

```bash
sudo ufw allow ssh
```

Nếu SSH đã đổi cổng, phải mở đúng cổng mới. Ví dụ SSH dùng 2222:

```bash
sudo ufw allow 2222/tcp
```

Không mở đúng cổng có thể khiến bạn mất khả năng kết nối hoặc kết nối lại bằng SSH. Nên giữ phiên hiện tại và thử một phiên SSH mới sau khi thay đổi.

### 4.4. Mở cổng website

**Theo số cổng:**

```bash
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
```

| Cổng | Dịch vụ |
|---|---|
| `22/tcp` | SSH mặc định |
| `80/tcp` | HTTP |
| `443/tcp` | HTTPS |

**Theo application profile:**

```bash
sudo ufw app list

# Chọn profile phù hợp
sudo ufw allow 'Nginx HTTP'
sudo ufw allow 'Nginx HTTPS'
sudo ufw allow 'Nginx Full'
```

Ba lệnh `allow` trên là các lựa chọn:

- `Nginx HTTP`: mở cổng 80.
- `Nginx HTTPS`: mở cổng 443.
- `Nginx Full`: mở cả hai.

Chỉ mở các cổng dịch vụ cần dùng; không cần thêm cả ba profile.

### 4.5. Cho phép hoặc chặn theo IP

Các IP dưới đây chỉ là ví dụ.

```bash
# Thêm quy tắc chặn một IP
sudo ufw deny from 192.168.1.150

# Thêm quy tắc chặn một dải mạng
sudo ufw deny from 192.168.1.0/24

# Cho phép một IP kết nối SSH
sudo ufw allow from 14.161.20.55 to any port 22 proto tcp

# Cho phép một máy nội bộ kết nối MySQL
sudo ufw allow from 10.0.0.5 to any port 3306 proto tcp
```

**Điểm cần hiểu đúng:** thêm quy tắc cho phép một IP không tự xóa quy tắc mở SSH cho tất cả. Muốn chỉ một IP được SSH, phải kiểm tra và loại bỏ quy tắc rộng còn tồn tại sau khi xác nhận kết nối quản trị hoạt động. Thứ tự quy tắc cũng ảnh hưởng đến kết quả. [Tài liệu UFW](https://manpages.ubuntu.com/manpages/noble/man8/ufw.8.html).

### 4.6. Xem, xóa và đặt lại quy tắc

```bash
# Xem số thứ tự
sudo ufw status numbered

# Ví dụ: xóa quy tắc số 3 đang hiển thị
sudo ufw delete 3

# Kiểm tra lại sau khi xóa
sudo ufw status numbered

# Đặt lại UFW
sudo ufw reset
```

- Số thứ tự có thể thay đổi sau mỗi lần xóa.
- Kiểm tra cả quy tắc IPv4 và IPv6 nếu hệ thống sử dụng cả hai.
- `reset` vô hiệu hóa UFW và đưa cấu hình về trạng thái ban đầu; các quy tắc đã thêm sẽ mất. Chỉ dùng khi chủ động muốn cấu hình lại. [Hướng dẫn UFW](https://manpages.ubuntu.com/manpages/noble/man8/ufw.8.html).

## 5. Công cụ thực hành và câu hỏi ôn tập

### 5.1. Bộ tính quyền Linux

Website có công cụ chọn quyền `r`, `w`, `x` cho owner, group và others rồi sinh mã quyền cùng lệnh `chmod`.

Ví dụ:

```text
Owner:  r + w + x = 7
Group:  r + x     = 5
Others: r + x     = 5

Kết quả: 755 → rwxr-xr-x
```

```bash
chmod 755 /var/www/my-static-web
```

Với tệp HTML chỉ cần quyền đọc và sửa thông thường:

```bash
chmod 644 /var/www/my-static-web/index.html
```

### 5.2. Terminal mô phỏng

Website có terminal trả kết quả mẫu cho các lệnh như:

```bash
help
pwd
ls -lah
nginx -t
systemctl status nginx
systemctl reload nginx
ufw status
git status
git flow init
git merge --abort
chmod 755 index.html
chown www-data:www-data index.html
df -h
whoami
clear
```

Đây là mô phỏng trong trình duyệt. Kết quả không phản ánh trạng thái VPS thật và không thay thế việc kiểm tra lệnh trên môi trường thực hành.

`help` trong công cụ này hiển thị danh sách lệnh mô phỏng; trên Bash thật, `help` có ý nghĩa khác.

### 5.3. Mười câu ôn tập kèm đáp án

Dưới đây là nội dung 10 câu hỏi của website được rút gọn thành dạng hỏi–đáp.

| STT | Câu hỏi | Đáp án và giải thích |
|---:|---|---|
| 1 | Xem dung lượng từng mục trong thư mục hiện tại bằng đơn vị dễ đọc? | `du -sh *`. `du` đo dung lượng tệp/thư mục; `df -h` xem filesystem. |
| 2 | Quyền `755` tương ứng ký hiệu nào? | `rwxr-xr-x`; với tệp thường, chuỗi có thêm ký tự loại tệp thành `-rwxr-xr-x`. |
| 3 | Theo dõi access log Nginx theo thời gian thực? | `tail -f /var/log/nginx/access.log`. |
| 4 | Nhánh nào tách từ `main` để sửa lỗi khẩn cấp? | `hotfix/*`. |
| 5 | Vì sao dùng `git merge --no-ff`? | Giữ dấu vết hợp nhất bằng merge commit, kể cả khi có thể fast-forward. |
| 6 | Kiểm tra cấu hình Nginx trước khi reload bằng lệnh nào? | `sudo nginx -t`. |
| 7 | `restart` khác `reload` thế nào? | `restart` dừng rồi khởi động lại; `reload` nạp cấu hình và chuyển worker theo cơ chế graceful. |
| 8 | Chỉ thị nào xác định web root? | `root`. |
| 9 | Kích hoạt site bằng symlink như thế nào? | `sudo ln -s /etc/nginx/sites-available/app.conf /etc/nginx/sites-enabled/`. |
| 10 | Đổi owner và group của thư mục web sang `www-data`? | `sudo chown -R www-data:www-data /var/www/my-site`. |

### 5.4. Các điểm cần nhớ trước khi thực hành

- Dùng `pwd` và `ls` để kiểm tra vị trí trước khi sửa hoặc xóa.
- Phân biệt `find` tìm tệp và `grep` tìm nội dung.
- Phân biệt `chmod` đổi quyền và `chown` đổi chủ sở hữu.
- Thư mục web thường dùng `755`; tệp tĩnh thường dùng `644`.
- Feature tách từ `develop`; hotfix tách từ `main`.
- Release và hotfix cần được tích hợp trở lại luồng phát triển.
- Xử lý conflict bằng cách sửa nội dung, xóa marker, `git add`, rồi commit.
- Kiểm tra `nginx -t` trước khi reload.
- Kiểm tra đúng server block nếu Nginx vẫn hiện trang mặc định.
- Mở đúng cổng SSH trước khi bật UFW.
- Mở cổng 443 không đồng nghĩa đã cấu hình HTTPS.
- Đọc log để xác định nguyên nhân lỗi trước khi đổi cấu hình hoặc quyền.
