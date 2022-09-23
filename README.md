# -Write-Up-Root-Me
### Target: ELF x86 - Random Crackme

  Crack me này cũng không hẳn là khó, có nhiều cách làm. Vì là một thành viên của hội người hèn nên ma xui quy khiến làm cho tìm ra cách giải đơn giản trước và sau đó mới
tìm ra hướng làm đầy đủ và chắc là đúng ý tác giả hơn. Bài này sẽ trình bày cả hai cách và đương nhiên, đường dễ đi trước, cách dễ trước.

### Start: 
![Imgur](https://i.imgur.com/FKlxykE.png)
  Mở file lên bằng IDA là thấy luôn ptrace, biết là debug không được rồi nên tạm gác lại, qua string view coi có gì dùng được không, và có cái dùng được thật.
 
![Imgur](https://i.imgur.com/Cv28yMf.png)

  Good password là đây chứ đâu. Sau khi nhập mật khẩu đúng, chương trình sẽ in ra đoạn str trên và flag. Đọc code một chút thấy được rằng đoạn flag được tạo 
  ra hoàn toàn không phụ thuộc vào mật khẩu nhập vào, debug bỏ qua ptrace bằng key patch thay đổi JNS(0x79) thành JMP(0xEB) và ta nhận được flag
  
![Imgur](https://i.imgur.com/HCaDi3P.png)
  
  **Flag: _VQLG1160_VTEPI_AVTG_3093_**
  
### Cách 2 thì là sau khi làm xong ngồi mò mẫm lại và tìm ra được, tại thấy khả năng đọc code khá kém nên là đọc lại từ từ để ngẫm.
  Trước tiên thì vẫn phải patch qua ptrace đã, sau đó tiến hành debug ta nhận được:
 ```
 Reading symbols from ./Crackme.dbg...done.
gdb$ r
Starting program: ./Crackme.dbg

    ** Bienvenue dans ce challenge de cracking **

[+] Password :hello

[!]Access Denied !
[-] Try again

[Inferior 1 (process 27) exited with code 0377]
--------------------------------------------------------------------------[regs]
 ```
 
  Xem xét lại code ta thấy có một memset và sau đó là lệnh gọi time(0), kết quả của nó được chuyển tới srand() sau đó gọi hàm getpid()
  kết quả được sử dụng để random ra giá trị được sử dụng ở đoạn chương trình sau:
  
  ![Imgur](https://i.imgur.com/rZicNFQ.png)
  
  Debug chương trình một vài lần ta nhận được kết quả sau từ hàm sprintf được gọi bên dưới:
  
  ![Imgur](https://i.imgur.com/ybuBdbS.png)
  
  Kết quả như sau: 
  ```
7427423_VQLGE_TQPTYD_KJTIV_
8974598_VQLGE_TQPTYD_KJTIV_
7529837_VQLGE_TQPTYD_KJTIV_
  ```
  Như vậy giá trị random sẽ được chuyển đổi thành giá trị nguyên và thêm vào đầu xâu "_VQLGE_TQPTYD_KJTIV_" kết quả sẽ được so sánh với
  password về độ dài của chúng, nếu độ dài khác nhau sẽ in ra thông báo lỗi. Và sau đó là so sánh hai chuỗi, cuối cùng là anti debug 
  bằng ptrace. Chúng ta sẽ cần dự đoán giá trị của chuỗi ngẫu nhiên này hoặc làm cho nó có thể dự đoán được. Chúng ta loại bỏ lệnh gọi đến rand
  và thay thế giá trị của nó bằng 0. Tiếp theo là sửa tương tự với getpid cuối cùng xâu chương trình dùng để so sánh sẽ luôn là:
  
  **0_VQLGE_TQPTYD_KJTIV_**
  
  Và đó cũng chính là mật khẩu mà ta cần đưa vào ^^ !!!
  
  #### Regards
