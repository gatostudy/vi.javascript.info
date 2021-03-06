Chúng ta chỉ có thể làm vậy nếu chắn chắn rằng thuộc tính `"constructor"` chứa giá trị đúng.

Ví dụ, nếu thuộc tính `"prototype"` mặc định không bị thay đổi, thì đoạn mã chắc chắn làm việc được:

```js run
function User(name) {
  this.name = name;
}

let user = new User('Việt');
let user2 = new user.constructor('Nam');

alert( user2.name ); // Nam (làm việc!)
```

Nó làm việc đúng bởi vì `User.prototype.constructor == User`.

...Nhưng nếu ai đó ghi đè mất `User.prototype` và quên tạo lại `"constructor"`, đoạn mã sẽ không làm việc đúng nữa:

Ví dụ:

```js run
function User(name) {
  this.name = name;
}
*!*
User.prototype = {}; // (*)
*/!*

let user = new User('Việt');
let user2 = new user.constructor('Nam');

alert( user2.name ); // undefined
```

Tại sao `user2.name` là `undefined`?

Đây là cách `new user.constructor('Nam')` làm việc:

1. Trước tiên nó tìm `constructor` trong `user`. Không có.
2. Sau đó nó theo chuỗi nguyên mẫu. Nguyên mẫu của `user` là `User.prototype`, và cũng không có.
3. Giá trị của `User.prototype` là đối tượng trống `{}`, nên mặc định nó thừa kế từ `Object.prototype`. Và có `Object.prototype.constructor == Object`. Nên `Object` được sử dụng.

Cuối cùng chúng ta có `let user2 = new Object('Nam')`. `Object` là một hàm tạo có sẵn, nó bỏ qua mọi đối số truyền vào và tạo ra một đối tượng trống -- vậy `user2` là đối tượng trống không có thuộc tính nào.
