
Answers:

1. `true`. 

    Thay đổi `Rabbit.prototype` chỉ ảnh hưởng tới `[[Prototype]]` của đối tượng được tạo ra sau này, không ảnh hưởng đến các đối tượng đã tạo từ trước. 

2. `false`. 

    Đối tượng được gán theo tham chiếu. Đối tượng từ `Rabbit.prototype` không được nhân bản khi gán cho `[[Prototype]]` cho nên `Rabbit.prototype` và `[[Prototype]]` của `rabbit` là hai tham chiếu tới một đối tượng.

    Nên khi ta thay đổi đối tượng qua một tham chiếu, sự thay đổi này cũng thấy được từ tham chiếu kia.

3. `true`.

    Hành động xóa bằng toán tử `delete` không tác động tới nguyên mẫu. Ở đây `delete rabbit.eats` chỉ cố xóa thuộc tính `eats` của `rabbit`, nhưng nó không tồn tại. Nên hành động này không gây ảnh hưởng gì.

4. `undefined`.

    Thuộc tính `eats` bị xóa khỏi nguyên mẫu, vậy nên nó không thể tìm thấy nữa.
