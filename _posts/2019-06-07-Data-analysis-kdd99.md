---
layout: post
title:  "Phân tích dữ liệu KDD99 (P.1)"
description: Phân tích dữ liệu KDD99 để sử dụng cho ML/DL
tags: tutorial MachineLearning analysis
---
## 1. Giới thiệu
KDD Cup 1999 là tập dữ liệu được sử dụng cho cuộc thi ***The Third International Knowledge Discovery and Data Mining Tools***. Nó chứa một bộ dữ liệu tiêu chuẩn để được kiểm toán, bao gồm các loại tấn công, xâm nhập khác nhau được mô phỏng trong môi trường mạng quân sự.

## 2. Thành phần dữ liệu.

Có tổng cộng 9 file trên trang chủ, tùy thuộc vào khả năng phần cứng mà bạn có thể lựa chọn chúng. Dưới đây, mình sẽ note lại ý nghĩa của từng file và file nào bạn nên tải:

[kddcup.names](http://kdd.ics.uci.edu/databases/kddcup99/kddcup.names) : Trong file này bao gồm 2 phần mà ta phải để ý đến:
    
        - Tên các class: các kiểu kết nối bình thường hoặc các kiểu tấn công, xâm nhập.
        
        - Tên của các feature.
    
[kddcup.data.gz](http://kdd.ics.uci.edu/databases/kddcup99/kddcup.data.gz) : 
        
        - Đây là tập dữ liệu chính mà ta cần xử lý. Nó sẽ được sử dụng cho việc huấn luyện các mô hình ML/DL. Gồm 41 cột và hơn 4 triệu dòng. Với các bạn máy yếu thì mình khuyên không nên xử lý trên file này.

[kddcup.data_10_percent.gz](http://kdd.ics.uci.edu/databases/kddcup99/kddcup.data_10_percent.gz) : 
        
        Nó chỉ là 10% của file trên. Và đây là file mà các bạn nên sử dụng vì nó khá nhẹ, dễ dàng xử lý một cách nhanh chóng (về mặt thời gian).
 
[kddcup.testdata.unlabeled.gz](http://kdd.ics.uci.edu/databases/kddcup99/kddcup.testdata.unlabeled.gz) : 

        Đã có train thì sẽ phải có test, thì đây là tập dữ liệu để kiểm tra xem model của bạn dự đoán như thế nào. Vì nó không hề có nhãn, nên ta không biết được là nó có chính xác không mà chỉ biết được nó sẽ được dự đoán ra những kiểu nào.

[kddcup.testdata.unlabeled_10_percent.gz](http://kdd.ics.uci.edu/databases/kddcup99/kddcup.testdata.unlabeled_10_percent.gz) : 

        Tương tự file trên.

[corrected.gz](http://kdd.ics.uci.edu/databases/kddcup99/corrected.gz) : 

        Tập này là 10% của tập test, nhưng nó có thêm cột class, nghĩa là đã được đánh nhãn để các bạn có thê kiểm tra xem model của mình dự đoán chính xác bao nhiêu %, từ đó đưa ra kết luận, chỉnh sửa và thay đổi đối với model.

- Mô tả thông tin của từng feature. Những thông tin sẽ có ích trong việc lựa chọn cách thức xử lý dữ liệu của chúng ta.

| Tên feature | Mô tả | Phân loại |
|---|---|---|---|---|
| Duration | Độ dài của một kết nối | continuous |
| Protocol_type |Các loại giao thức như: tcp, udp, ... | Discrete |
| Service | Các dịch vụ mạng: http, telnet, … | Discrete |
| src_bytes | Số byte data từ nguồn tới đích | continuous |
| dst_bytes | Số byte data từ đích tới nguồn continuous |
| flag | Trạng thái kết nối (bình thường hoặc lỗi) | discrete |
| land | 1 nếu kết nối được thiết lập từ một host/port tương tự <br \> 0 cho các trường hợp còn lại | discrete |
| wrong_fragment | Số fragment bị cảnh báo | continuous |
| urgent | Số gói tin urgent | continuous |
| hot | Số lượng chỉ số “hot” | continuous |
| num_failed_logins | Số kết nối thất bại | continuous |
| logged_in | 1 nếu đăng nhập thành công <br /> 0 các trường hợp còn lại | discrete |
| num_compromised | Số lượng tình huống bị xâm phạm | continuous |
| root_shell | 1 nếu thu được roo shell <br \> 0 các trường hợp còn lại | discrete |
| su_attempted | 1 nếu “su root” được thực hiện <br /> 0 cho các trường hợp còn lại | discrete |
| num_root | Số lượng truy cập root | continuous |
| num_file_creations | Số lượng tập tin được tạo ra | continuous |
| num_shells | Số lượng shell | continuous |
| num_access_files | Số các thao tác kiểm soát truy cập tệp tin | continuous |
| num_outbound_cmds | Số lượng các lệnh outbound trong một phiên ftp | continuous |
| is_hot_login | 1 nếu thông tin đăng nhập thuộc danh sách “hot”. <br />0 cho các trường hợp còn lại | discrete |
| is_guest_login | 1 nếu thông tin đăng nhập là khách <br /> 0 cho các trường hợp khác | discrete |
| count | Số lượng kết nối tới cùng một host cách kết nối hiện tại 2s | continuous |
| serror_rate | % các kết nối có lỗi SYN | continuous |
| rerror_rate | % các kết nối có lỗi “REJ” | continuous |
| same_srv_rate | % các kết nối đến cùng dịch vụ. | continuous |
| diff_srv_rate | % các kết nối tới các dịch vụ khác. | continuous |
| srv_count | Số lượng các kết nối tới cùng 1 dịch vụ so với kết nối hiện tại 2s | continuous |
| srv_serror_rate | % các kết nối dịch vụ lỗi “SYN” | continuous |
| srv_rerror_rate | % các kết nối dịch vụ lỗi “REJ” | continuous |
| srv_diff_host_rate | % các kết nối tới các host khác. | continuous |

## 3. Tạo môi trường để xử lý dữ liệu.
- Ngôn ngữ sử dụng Python 3.x
- Thư viện sử dụng: Numpy, Pandas, Jupyter Notebook.

 Lý do mà mình lựa chọn Python 3.x bởi vì nó là một ngôn ngữ dễ sử dụng, có nhiều module hỗ trợ trong nhiều lĩnh vực, đặc biệt là khoa học phân tích dữ liệu. 

 ***Numpy*** là một thư viện xử lý số học, trong bài này, mình sẽ xử dụng nó để tính toán Z-score (ý nghĩa mình sẽ giới thiệu trong mục tiếp theo). 
 
 Tiếp theo là ***Pandas***, đây là một thư viện hỗ trợ rất mạnh cho việc xử lý, thể hiện dữ liệu ở dạng bảng (như exel), giúp cho việc phân tính trở nên tường mình, dễ nhìn hơn.

 ***Jupyter Notebook***. Đây là một công cụ rất mạnh cho việc tạo môi trường Python phục vụ việc nghiên cứu. Nó được chạy trên nền Web, bạn có thể thực hiện các dòng lệnh 1 và biểu diễn chúng mà không cần compile cả file. Nó tương tự như việc bạn chạy lệnh python trên Terminal, tuy nhiên với Jupyter, nó sẽ lưu lại các câu lệnh, kết quả mà bạn làm, giúp cho việc tiếp tục công việc ở đợt sau thuận tiện hơn.

 **Note**: Khi làm việc với Python, bạn nêu tạo một môi trường ảo được gọi là **Virtualenv** được hỗ trợ bởi python, sau khi truy cập vào môi trường ảo rồi thì mới cài đặt các module liên quan. Mỗi project thì nên sử dụng một Virtualenv để tránh việc xung đột giữa các phiên bản, đồng thời thử nghiệm được phiên bản mới của các module mà không ảnh hưởng đến phiên bản chính. Nên tập thói quen tạo ra một Virtualenv khi thực hiện các Project không liên quan tới nhau.

## 4. Thể hiện dữ liệu với pandas.
Sau khi đã cài đặt môi trường cho Python xong, ta sẽ khởi động jupyter notebook bằng terminal và truy cập nó trên web.

{% highlight ruby %}jupyter-lab {% endhighlight %}

Bạn có thể thay đổi port truy cập cho nó bằng cách thêm **--port** và thêm số port muốn truy cập sau đó.

Import thư viện pandas và xem thử nó được mô tả như nào nhé:

![Image](https://i.imgur.com/DjJGe9o.png)

À quên chưa nhắc, nếu các bạn có lỡ mở file data lên bằng notepad (trên windows) hay text  editor (trên linux) thì khả năng treo máy khá cao, trừ máy mạnh. Muốn biết nội dung trong file như thế nào thì nó sẽ như thế này:

![Image](https://i.imgur.com/S4aGTfj.png)

Trong thư viện pandas, chúng ta sẽ mở file data bằng method **read_csv()** của **Pandas** và display nó ra. Ở đây mình sẽ display 10 dòng đầu.

![Image](https://i.imgur.com/HN8UP4N.png)

Vì trong tập dữ liệu này chưa có tên các cột (tên feature ứng với mỗi cột) nên ta phải thêm parameter ```header=None```. Nếu không thêm vào, Pandas sẽ tự động lấy dòng đầu tiên làm tên column.

Việc cần làm tiếp theo là chúng ta sẽ thêm các tên feature vào để dễ đọc hơn, ta tiến hành mở file ```kddcup.names.txt```.

![Image](https://i.imgur.com/Nin3NOT.png)

Đây là một điểm lợi thế khi dùng jupyter. Bạn chỉ cần ghi tên variable ra thì nó sẽ hiện giá trị của variable khi được thực thi. Ở đây ta thấy nó một list bao gồm: dòng đầu tiên sẽ là tên các loại kết nối (có thể là tấn công hoặc bình thường). Các dòng còn lại sẽ là tên các feature và nó đã được sắp xếp theo đúng thứ tự. Việc cần làm bây giờ là tách nó ra, đưa nó về dạng dễ nhìn và thêm vào trong data.

![Image](https://i.imgur.com/KQeYGCn.png)

Nhìn vào đoạn code trên, có thể một vài bạn mới sẽ không hiểu. Trong python có một thuật ngữ là [List comprehension](https://docs.python.org/3/tutorial/datastructures.html#list-comprehensions). Nó được sử dụng để tạo ra một list mới từ một iterables (ở đây là một list). Vì sử dụng method **readlines()** nên giá trị trả về khi đọc file sẽ là một list như mình đã show ở biến names, và ở mình chỉ lấy giá trị từ vị trí 1 trở đi nên dùng list slicing **[1:]**. Tiếp theo, mình chỉ muốn lấy tên của các feature chứ không lấy các râu ria như dấu **:** hay các từ như ***continuous*** nên ta sẽ split chuỗi tại ký tự ```:``` và lấy giá trị tên feature.

Nếu để ý, bạn sẽ thấy chỉ có 41 tên feature nhưng lại có 42 cột. Đúng vậy, cột cuối cùng là tên các loại kết nối, mình sẽ đặt cho nó là **labels** và dùng parameters **names** trong **read_csv** để chèn thêm vào tên cột khi đọc file vào.

![Image](https://i.imgur.com/GG4hmhT.png)

Như vậy là đã xong một phần nhỏ của việc xử lý data ban đầu. Mình xin kết thúc phần 1 tại đây. Trong phân 2, mình sẽ đi sâu vào việc phân tích, biến đổi data để sử dụng cho model Neural Network.

Thanks & Best Regrads!