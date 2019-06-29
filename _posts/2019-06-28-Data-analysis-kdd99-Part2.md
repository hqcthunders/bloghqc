---
layout: post
title:  "Phân tích dữ liệu KDD99 (P.2)"
description: Phân tích dữ liệu KDD99 để sử dụng cho ML/DL
tags: tutorial MachineLearning analysis
---

# 1. Phân tích dữ liệu

![Image](https://i.imgur.com/0yd79P5.png)

Để có thể đưa dữ liệu vào mô hình, đầu tiên ta cần phải chuẩn hóa dữ liệu, đưa dữ liệu **string** về dạng số, có thể thực hiện vài thao tác để đưa dữ liệu số về loại chuẩn hơn và các giá trị đó sẽ nằm trong khoảng [0, 1]

Ở hình trên, những dữ liệu kiểu **strings** cần chuẩn hóa là cột : `Protocol Type, Service, Flag`

## 1.1 Chuẩn hóa dữ liệu strings

Có rất nhiều phương pháp để chuẩn hóa **string**, cách đơn giản nhất là chuyển chúng về 0 hoặc 1. Giả sử mình có một 2 cột là Bánh_kẹo và Thức_uống, giá trị của 2 cột đó là sở thích của mỗi member:

| Member | Bánh_kẹo | Thức_Uống |
|---|---|---|
| Cường | Gateaux | Coca |
| Hoàng| Crepe | Coffee |

Khi ta chuẩn hóa, ta sẽ thay đổi các cột. Những loại sản phẩm nào member đó thích thì ta để số 1, không thích thì để số 0, như vậy ta thay đổi như sau:

| Member | Bánh_kẹo_Gateaux | Bánh_kẹo_Crepe | Thức_Uống_Coca | Thức_Uống_Coffee |
|---|---|---|---|---|
| Cường | 1 | 0 | 1 | 0 |
| Hoàng| 0 | 1 | 0 | 1 |

Với cách làm như vậy, ta có thể chuẩn hóa các cột `Protocol Type, Service, Flag` thành:

![Image](https://i.imgur.com/X8C7oNM.png)

Mình sẽ sử dụng hàm **get_dummies(dataFrame, prefix)** của Pandas. Việc này tuy sẽ làm tăng kích thước của tập dữ liệu nhưng đồng thời lượng thông tin cho từng nhãn sẽ được tăng lên, làm tập dữ liệu tốt hơn để giúp training sau này.

## 1.2 Phân tích dữ liệu số.
### Z-Score

- Z-score là số độ lệch chuẩn của một giá trị x cho trước với giá trị trung bình μ. 
- Nó thường được sử dụng cho quá trình chuẩn hóa dữ liệu.
- Công thức như sau:

z = \frac{x - \mu }{\sigma }

Vì chi giá trị này, bạn có thể tham khảo tại (Đây)[https://mathbitsnotebook.com/Algebra2/Statistics/STzScores.html]

Chúng ta sẽ chuyển đổi các cột chứa giá trị số  về các giá trị z-score theo công thức trên. Ở đây mình sẽ làm ví dụ trên 2 cột **src_bytes** và **dst_bytes** : 

![Image](https://i.imgur.com/eXQUwbS.png)

# 2. Làm mới dữ liệu

Sau khi phân tích dữ liệu, ta tiến hành thực hiện các việc cần làm để tạo ra một dữ liệu mới và sử dụng nó để training. Dữ liệu sau khi làm mới như sau:

![Image](https://i.imgur.com/FMcBcSF.png)

Sau khi xử lý, mình đã thu được dữ liệu mới có kích thước là 4898431 dòng và 126 cột. Phần class sẽ được tách riêng thành một dữ liệu riêng để đưa vào mô hình training.

# 3. Training cho mô hình neural network

Mình sẽ sử dụng dữ liệu vừa làm mới để training cho mô hình neural network đơn giản. Để tạo được mô hình, mình sẽ sử dụng công cụ tensorflow với API là Keras để tạo mô hình. Chúng ta có thể hoàn toàn tự thiết kế các thuật toán, xây dựng model với phần core của tensorflow low level, nhưng ở đây mình sẽ lựa chọn high level là Keras để việc thiết kế mô hình sẽ đơn giản hơn.

Cấu trúc mô hình mình thử nghiệm là như sau:

![Image](https://i.imgur.com/GbMMW4N.png)

Quá trình training kết quả thu được khá là tốt, khoảng 0.99 cho validation accuracy và 0.01 validation loss. Tuy nhiên, khi đưa vào predict thực tế thì kết quả không tốt cho lắm với test dataset. 

![Image](https://i.imgur.com/8wFo5bV.png)

Có 1 lý do để giải thích cho chuyện này đó là các nhãn lạ, nghĩa là sẽ không có các nhãn đã được training mà là hoàn toàn mới. Ví thế, model sẽ nhận dạng nó với nhãn nào gần nhất và đưa ra giá trị đó. Với bài toàn Unknown này, mình sẽ viết nó vào bài tiếp theo.