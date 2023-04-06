# Authentication Testing (Broken Authentication) - Top 10 OWASP 2017
## 1. Bypassing Authentication Schema
Trong lổ hổng *Broken Authentication*, thì Bypassing Authentication Scheme là một nhánh nhỏ của lổ hổng này. Có thể biết rằng các phần mềm hoặc ứng dụng thường yêu cầu xác thực để có thể truy cập vào, ví dụ: email, username, password, …Và Bypassing Authentication (Bypass Authen) là phần rất dễ bị tấn công. Nó cho phép hacker bỏ qua các bước xác thực và giành quyền truy cập vào ứng dụng và dữ liệu. Từ đó có thể thực hiện các hành động nguy hiểm khác như cài back-door, đánh cắp thông tin, can thiệp tiến trình của ứng dụng…
---

## 2. Detect and Exploit
###
Cách dễ phát hiện một cuộc tấn công Bypassing Authentication nhất kiểm tra log và các input đầu vào…

**Snort log**

```rust
[**] [1:21503:10] SERVER-OTHER Bypassing Authentication Schema [**]
[Classification: Attempted Administrator Privilege Gain] [Priority: 1]
06/01-16:03:33.254691 192.168.1.10:54665 -> 192.168.1.20:80
TCP TTL:64 TOS:0x0 ID:54144 IpLen:20 DgmLen:124 DF
***AP*** Seq: 0xB35AA2F2  Ack: 0x7C09EB42  Win: 0x7010  TcpLen: 20
[Xref => http://cve.mitre.org/cgi-bin/cvename.cgi?name=2008-5566][Xref => http://www.securityfocus.com/bid/32248][Xref => http://www.osvdb.org/49626]
```

**Kibana log**

```json
{
  "_index": "logstash-2021.08.24",
  "_type": "_doc",
  "_id": "Nv1ZQXsBz-C0I8A9MGo9",
  "_score": null,
  "_source": {
    "@timestamp": "2021-08-24T12:08:37.000Z",
    "agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/93.0.4577.63 Safari/537.36",
    "client_ip": "192.168.1.10",
    "host": "example.com",
    "message": "Failed login attempt for admin",
    "status": 401,
    "tags": [
      "authentication",
      "failed-login",
      "bypassing-authentication"
    ],
    "url": "/admin/login.php",
    "user_agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/93.0.4577.63 Safari/537.36",
    "user_name": "admin"
  },
  "fields": {
    "@timestamp": [
      "2021-08-24T12:08:37.000Z"
    ]
  },
  "sort": [
    1629808117000
  ]
}
```

Để **exploit** thì ta có thể dùng một số kĩ thuật:

- Khai thác các lỗ hổng: Attacker có thể khai thác các lỗ hổng trong hệ thống xác thực, chẳng hạn như **SQL injection** hoặc **Cross-Site Scripting** (XSS).
    
    Link Test: **[LINK](https://dvwa.fptufia.me/)**
    
    **SQLi**
    
    ![Untitled](.image/Untitled.png)
    
    **XSS**
    
    ![Untitled](.image/Untitled%201.png)
    
    ![Untitled](.image/Untitled%202.png)
    
- File Path Traversal: là một kỹ thuật cho phép attacker truy cập vào các file dữ liệu nhạy cảm trên máy chủ thông qua việc nhập các đường dẫn tập tin khác nhau.
    
    Test lab ở đây: [**LINK](https://portswigger.net/web-security/file-path-traversal).**
    
    ![Untitled](.image/Untitled%203.png)
    
    ![Untitled](.image/Untitled%204.png)
    
- Chỉnh sửa Cookie: là kỹ thuật tấn công mà attacker sửa đổi các giá trị của cookie của user để đăng nhập vào tài khoản của họ hoặc lấy cắp session đã được xác thực.
    
    Link Challange: **[LINK](https://www.root-me.org/en/Challenges/Web-Server/HTTP-Cookies)**
    
    ![Untitled](.image/Untitled%205.png)
    
    ![Untitled](.image/Untitled%206.png)
    

## 3. How to protect?

- Triển khai xác thực hai yếu tố: Xác thực hai yếu tố (2FA) thêm một lớp bảo mật bổ sung bằng cách yêu cầu người dùng cung cấp hình thức nhận dạng thứ hai, chẳng hạn như tin nhắn văn bản, email hoặc OTP, ngoài tên người dùng và mật khẩu của họ.
- Triển khai Hệ thống Phát hiện Xâm nhập: Hệ thống Phát hiện Xâm nhập - Intrusion Detection Systems (IDS) có thể giám sát lưu lượng mạng và xác định bất kỳ hoạt động đáng ngờ nào.
- Giới hạn số lần đăng nhập: Điều này có thể ngăn chặn được các cuộc tấn công Brute-Force.
- Đảm bảo dữ liệu đầu vào là Trust Data