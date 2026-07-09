# Mô tả

<img width="645" height="699" alt="image" src="https://github.com/user-attachments/assets/a8aec125-bbeb-4d18-9359-6144272fd2f2" />

# Giải
  * Kiểm tra và phân tích
    - Theo gợi ý của đề bài, chúng ta cần phải leo thang đặc quyền (Privelege Escalating) lên quyền admin để có thể RCE (Remote Code Execute), từ đó lấy được flag. Đề bài cũng nói thêm là rockyou.txt không thể sử dụng, vậy nên chúng ta sẽ loại ngay hướng bruteforce mật khẩu.
    - Đề bài cho chúng ta một trang web tuyển dụng của FPT University. 
      
      <img width="1920" height="914" alt="image" src="https://github.com/user-attachments/assets/a0e98b3f-d6a8-4c5e-bc0c-d899fb17f754" />

    - Ta thấy ở giao diện có mục `Ứng tuyển`, khi ta bấm thử vào, hệ thống đưa chúng ta tới trang đăng kí. Chúng ta sẽ tiến hành tạo một tài khoản

      <img width="1920" height="919" alt="image" src="https://github.com/user-attachments/assets/aad90a6a-708b-4e55-9cc0-ef940ae2a911" />
      
    - Sau khi tạo tài khoản, hệ thống sẽ đưa chúng ta tới trang đăng nhập. Đăng nhập tài khoản chúng ta vừa tạo, hệ thống sẽ đưa chúng ta tới trang upload cv

      <img width="1920" height="915" alt="image" src="https://github.com/user-attachments/assets/66729309-9faa-41c8-bc18-957627b039c5" />

    - Chúng ta thử bấm bừa để tạo 1 cái cv. Sau khi tạo xong, CV của chúng ta đã được gửi lên hệ thống, ở trạng thái pending.
    - Bên cạnh đăng nhập, hệ thống cũng có chức năng `Quên mật khẩu`. Hệ thống sẽ yêu cầu chúng ta cung cấp tên đăng nhập, và tạo ra một OTP có giá trị trong 30 phút
      
      <img width="608" height="495" alt="image" src="https://github.com/user-attachments/assets/a60a61e6-7eac-4f19-9c8f-9e18e593ab46" />

      <img width="599" height="509" alt="image" src="https://github.com/user-attachments/assets/67775a4b-2cae-4f9c-b0dd-0fd70408435e" />

    - Tới đây cũng hết những gì chúng ta có thể làm rồi, tương tác với những phần khác cũng chẳng có thêm giá trị gì. Chúng ta sẽ kiểm tra file source code của hệ thống đi kèm trong đề bài
    - Bắt đầu với file `Dockerfile`, đây là file Docker của hệ thống, chúng ta phát hiện 1 vài thông tin quan trọng sau mà lập trình viên đã "bỏ quên" :
      - File flag có tên là part2.txt, nằm ở thư mục `/` và chỉ có thể xem với quyền `root`
      - Lệnh INTO OUTFILE CỦA mysql có thể sử dụng ở khắp nơi, không bị hạn chế
        
        <img width="682" height="190" alt="image" src="https://github.com/user-attachments/assets/d845e515-4aa2-4f87-8fc9-c6370a667314" />

    - Kiểm tra tới thư mục `src`, đây là mỏ vàng chứa tất cả những file của hệ thống. Qua kiểm tra, hệ thống filter input khá chuẩn, khá kĩ, đa số là đều đã kín, nhưng vẫn có những file quan trọng mà chúng ta cần lưu tâm: `admin.php`, `forgot.php`, `preview.php`
      - `admin.php`
        - Ở đây logic không có gì đặc biệt, chúng ta chỉ phát hiện được rằng part 1 của flag nằm ở đây

          <img width="885" height="250" alt="image" src="https://github.com/user-attachments/assets/0aa19fcf-289b-4ef3-8ee8-0b53a7618caf" />

      - `forgot.php`
        - Đây là file xử lý khi chúng ta quên mật khẩu, hệ thống sẽ kiểm tra xem tài khoản của bạn có tồn tại hay không bằng cách kiểm tra tên đăng nhập. Nếu có tồn tại, hệ thống sẽ tạo một OTP code và nhét thẳng vào Database
          
          <img width="1024" height="410" alt="image" src="https://github.com/user-attachments/assets/395b5bc1-a038-4086-9906-540a6f03a354" />

      - `preview.php`
        - Đây là giao diện xem cv của admin. Ở đây có một lỗ hổng khá lớn, hệ thống dùng lệnh query sql cho phép nối chuỗi trực tiếp, chúng ta có thể nghĩ ngay tới SQLi
       
          <img width="650" height="185" alt="image" src="https://github.com/user-attachments/assets/bd5369a2-b3f1-4f96-8f85-514058d02cd3" />

        (Lập trình viên còn tử tế đến mực gợi ý cho chúng ta lỗ hổng để khai thác =)))))) )
    - Sau khi đã kiểm tra kĩ, hướng đi của chúng ta sẽ như sau: tìm cách truy cập vào admin panel, sau đó sẽ thực hiện RCE ở đó, để có thể lấy được đầy đủ flag

  * Giải
    - Đầu tiên chúng ta sẽ tìm cách để truy cập được vào admin panel. Có vẻ như không có cách nào ngoài việc chúng ta phải tìm ra tài khoản admin rồi truy cập, bởi chẳng có cách nào tạo ra tài khoản admin cả. Kiểm tra trang web kĩ càng hơn chút, ta sẽ thấy ở phần header có một số thông tin của team HR, có lẽ chúng ta sẽ phải thử từng tên một xem liệu đâu sẽ là tài khoản admin

      <img width="550" height="323" alt="image" src="https://github.com/user-attachments/assets/aea9b3ee-793d-4b15-b32a-b0f66358441c" />

# Kết bài
  - Filter SQL thật kĩ càng, tránh nối chuỗi trực tiếp, tạo đường cho hacker sử dụng kĩ thuật SQLi
CẢM ƠN BẠN ĐÃ ĐỌC!


