# [Vận dụng chuyên sâu] Thiết kế giải pháp Prompting ứng dụng thực tế cho Kỹ thuật Prompting Nâng cao (Logic & Chuỗi)

**Phần 1** Thiết kế giải pháp (I/O)

Input

Mảng JSON gồm các trường:

- orderId: Mã đơn hàng, không được trống hoặc trùng.

- product: Tên sản phẩm, không được trống.

- quantity: Số lượng, phải là số nguyên lớn hơn 0.

- unitPrice: Đơn giá, phải là số lớn hơn 0, đơn vị đồng.

Output

Một đối tượng JSON gồm:

- orders: Danh sách giữ đủ các đơn theo thứ tự đầu vào. Mỗi đơn có dữ liệu gốc và thêm recordIndex, status, errors, amount.

- totalRecords: Tổng số đơn.

- validRecords, invalidRecords: Số đơn hợp lệ và không hợp lệ.

- totalAmount: Tổng thành tiền của các đơn hợp lệ.

2. Prompt thực thi

Prompt 1: Kiểm tra dữ liệu

Bạn là nhân viên xử lý đơn hàng của cửa hàng. Kiểm tra dữ liệu trong [INPUT_JSON] theo các quy tắc:
```
1. Nếu đầu vào không phải mảng JSON hợp lệ, chỉ trả về {"error":"INVALID_INPUT"} và dừng.

2. Nếu mảng rỗng, trả về {"orders":[]}.

3. Với mỗi đơn, thêm recordIndex bắt đầu từ 1. Giữ các trường orderId, product, quantity, unitPrice; trường thiếu hoặc chuỗi chỉ chứa khoảng trắng chuyển thành null. Chỉ bỏ khoảng trắng thừa ở hai đầu mã đơn và tên sản phẩm; không tự đoán hoặc sửa số liệu.

4. Nếu mã đơn hoặc tên sản phẩm trống, ghi lỗi tương ứng. Nếu mã đơn trùng, đánh dấu tất cả đơn có mã đó là lỗi.

5. Nếu số lượng không phải số nguyên dương hoặc đơn giá không phải số dương, ghi lỗi tương ứng. Không tự chuyển chuỗi thành số.
6. Nếu phần tử không phải đối tượng, vẫn giữ vị trí bản ghi, đặt bốn trường dữ liệu là null và ghi lỗi "Bản ghi không phải đối tượng".

7. Thêm status là VALID nếu không có lỗi, ngược lại là INVALID; thêm errors là mảng mô tả lỗi ngắn gọn. Không bỏ hoặc gộp đơn.
```
Chỉ xuất JSON dạng {"orders":[...]}, không thêm giải thích hoặc Markdown Code Fence.

Prompt 2: Tính thành tiền

Bạn là nhân viên tính tiền đơn hàng. Đầu vào là [JSON_BUOC_1]. Nếu có trường error, giữ nguyên kết quả và dừng.
Với mỗi đơn, kiểm tra trạng thái rồi tính theo thứ tự:
```
- Nếu status là VALID, tính amount = quantity × unitPrice.

- Nếu status là INVALID, đặt amount bằng null, không tính tiền.

- Giữ nguyên các trường còn lại, thứ tự và số lượng bản ghi. Không thêm thuế, giảm giá hoặc phí vận chuyển.
```

Kiểm tra lại phép nhân. Chỉ xuất JSON dạng {"orders":[...]}, không trình bày quá trình giải thích.