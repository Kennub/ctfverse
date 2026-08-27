# SQLi là gì (What is SQLi)
  - SQLi (SQL Injection) là một lỗ hổng bảo mật cho phép kẻ tán công can thiệp vào câu lệnh query đến CSDL của ứng dụng. Điều này cho phép kẻ tấn công đọc được những dữ liệu nhạy cảm mà đáng ra họ không có quyền.
  - Trong một số trường hợp, hacker có thể tận dụng lỗi này để leo thang đặc quyền, sang tấn công những hạ tầng/dịch vụ khác, dẫn tới tấn công từ chối dịch vụ (DoS)

# Ảnh hưởng của SQLi
  - Lộ những thông tin nhạy cảm (Password, Credit card, thông tin cá nhân)
  - Nhiều vụ rò rỉ thông tin lớn diễn ra hằng năm là do SQLi. Không chỉ thế, attacker còn có thể để lại một backdoor để xâm nhập kéo dài mà không ai hay biết.

# Cách để nhận biết lỗ hổng SQLi
  - Bạn có thể nhận biết SQLi thủ công bằng cách gửi 1 hệ thống payload ở mọi nơi mà có thể nhập dữ liệu:
    - Nhập payload `'`:
      - Đây là lệnh đóng chuỗi trong SQL, giả dụ 1 câu SQL gốc của hệ thống:
        
        `SELECT * FROM USERS WHERE USERNAME='A' AND PASSWORD='B'`

        

      
