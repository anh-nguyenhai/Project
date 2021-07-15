# Project
# Sử dụng mã hóa RSA để tạo một cơ chế bảo vệ hình ảnh :
## Mã hóa RSA 
Hệ mã hóa bất đối xứng (asymmetric cryptography) hay còn gọi là hệ mã hóa public key là một hệ mã hóa sử dụng một cặp key để mã hóa và giải mã: public key (khóa công khai) dùng để mã hóa và private key (khóa bí mật) để giải mã.

Trong hệ mã hóa này, bất cứ ai cũng có thể sử dụng public key đã mã hóa bản tin và gửi cho người nhận. Nhưng một điều hiển nhiên là người sở hữu private key sẽ giữ nó cho riêng mình, và do đó, chỉ anh ta mới có giải mã được mà thôi.

Thông thường thì cặp khóa được sinh này sẽ cố gắng đảm bảo rằng từ public key rất khó (gần như là không thể) truy ra được private key. Vì vậy, bất cứ kẻ tấn công nào nếu có được public key (điều này khá dễ dàng) cũng không thể có được private key để giải mã.

## Sinh khóa 
Mấu chốt cơ bản của việc sinh khóa trong RSA là tìm được bộ 3 số tự nhiên e, d và n sao cho:

mned

và một điểm không thể bỏ qua là cần bảo mật cho d sao cho dù biết e, n hay thậm chí cả m cũng không thể tìm ra d được.

Cụ thể, khóa của RSA được sinh như sau:

Chọn 2 số nguyên tố p và q
Tính n = pq. Sau này, n sẽ được dùng làm modulus trong cả public key và private key.
Tính một số giả nguyên tố bằng phi hàm Carmichael như sau: λ(n) = BCNN(λ(p), λ(q)) = BCNN(p − 1, q − 1). Giá trị này sẽ được giữ bí mật.
Chọn một số tự nhiên e trong khoảng (1, λ(n)) sao cho ƯCLN(e, λ(n)) = 1, tức là e và λ(n) nguyên tố cùng nhau.
Tính toán số d sao cho d ≡ 1/e (mod λ(n)) hay viết dễ hiểu hơn thì de ≡ 1 (mod λ(n)). Số d được gọi là số nghịch đảo modulo của e (theo modulo mod λ(n)).
Public key sẽ là bộ số (n, e), và private key sẽ là bộ số (n, d). Chúng ta cần giữ private key thật cẩn thận cũng như các số nguyên tố p và q vì từ đó có thể tính toán các khóa rất dễ dàng.

Trong thực hành, chúng ta thường chọn e tương đối nhỏ để việc mã hóa và giải mã nhanh chóng hơn. Giá trị thường được sử dụng là e = 65537. Ngoài ra, chúng ta có thể tính số giả nguyên tố bằng phi hàm Euler φ(n) = (p − 1)(q − 1) và dùng nó như λ(n). Vì φ(n) là bội của λ(n) nên các số d thỏa mãn de ≡ 1 (mod φ(n)) cũng sẽ thỏa mãn d ≡ 1/e (mod λ(n)). Tuy nhiên, một số tác dụng phụ của việc này là d thường sẽ trở nên lớn hơn mức cần thiết.

## Mã hóa và giải mã
Trong phần này, chúng ta sẽ tìm hiểu cách mã hóa với public key (n, e) và giải mã với private key (n, d).

Nếu chúng ta có bản rõ M, chúng ta cần chuyển nó thành một số tự nhiên m trong khoảng (0, n) sao cho m, n nguyên tố cùng nhau. Việc này rất dễ dàng thực hiện bằng cách thêm một các kỹ thuật padding. Tiếp theo, chúng ta sẽ má hóa m, thành c như sau:

cipher

Sau đó giá trị c sẽ được chuyển cho người nhận.

Ở phía người nhận, họ sẽ giải mã từ c để lấy được m như sau:

decrypt

Từ m có thể lấy lại được bản tin bằng cách đảo ngược padding

Chúng ta lấy một ví dụ đơn giản như sau:

p = 5, q = 7
=> n = pq = 35
=> φ(n) = 24
Chúng ta chọn e = 5 vì ƯCLN(5, 24) = 1, cuối cùng chọn d = 29 vì ed - 1 = 29x5 - 1 chia hết cho 24.

Giả sử m = 32 (dấu cách), chúng ta sẽ mã hóa m và thu được

c = 32 ^ 5 % 35 = 2
Giải mã c để thu được m

m = 2 ^ 29 % 35 = 32
Đây chính là m ban đầu. Bạn có thể thứ m với các giá trị khác nhau để thấy thuật toán hoàn toàn chính xác. Tất nhiên rồi, nó đã được chứng minh mà. Việc chứng minh RSA hoạt động chính xác là một vấn đề toán học khá phức tạp mà bài viết này sẽ không đi vào chi tiết của nó.

Mức độ bảo mật của RSA phụ thuộc rất lớn vào khả năng phân tích thừa số nguyên tố của các số lớn. Bởi vì chúng ta cung cấp public một cách rộng rãi, nếu việc phân tích thừa số nguyên tố đơn giản, thì việc bị lộ private là không thể tránh khỏi.

Vì vậy, khi sinh khóa, chúng ta cần chọn các số nguyên tố p và q một cách ngẫu nhiên. Bản thân hai số nguyên tố này cũng rất lớn, và để việc phân tích thừa số nguyên tố khó khăn hơn, hai số nguyên tố này sẽ không có cùng độ dài. Trong tương lai gần, có lẽ vẫn chưa có một phương pháp hiệu quả nào cho phép thực hiện điều này với các máy tính cá nhân.
