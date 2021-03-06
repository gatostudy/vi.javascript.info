
# Truy cập nguyên mẫu không dùng "__proto__"

Trong bài đầu của chương, chúng ta đã nói về các phương thức hiện đại để lấy và cài đặt nguyên mẫu.

Thuộc tính truy cập `__proto__` được coi là lỗi thời và không được dùng nữa (chỉ trên môi trường trình duyệt).

Các phương thức hiện đại là:

- [Object.create(proto[, descriptors])](mdn:js/Object/create) -- tạo đối tượng có nguyên mẫu là `proto` (cài đặt cho `[[Prototype]]`) và có thể dùng `descriptors` làm các "property descriptor".
- [Object.getPrototypeOf(obj)](mdn:js/Object/getPrototypeOf) -- trả về `[[Prototype]]` của `obj`.
- [Object.setPrototypeOf(obj, proto)](mdn:js/Object/setPrototypeOf) -- cài đặt `proto` làm `[[Prototype]]` của `obj`.

Chúng nên được dùng thay cho `__proto__`.

Ví dụ:

```js run
let animal = {
  eats: true
};

// tạo đối tượng nhận animal làm nguyên mẫu
*!*
let rabbit = Object.create(animal);
*/!*

alert(rabbit.eats); // true

*!*
alert(Object.getPrototypeOf(rabbit) === animal); // lấy nguyên mẫu của rabbit
*/!*

*!*
Object.setPrototypeOf(rabbit, {}); // đổi nguyên mẫu của rabbit thành {}
*/!*
```

`Object.create` có đối số không bắt buộc là các "property descriptor". Chúng ta có thể thêm các thuộc tính vào đối tượng được tạo:

```js run
let animal = {
  eats: true
};

let rabbit = Object.create(animal, {
  jumps: {
    value: true
  }
});

alert(rabbit.jumps); // true
```

Các "property descriptor" đã được nói đến trong bài <info:property-descriptors>.

Chúng ta có thể sử dụng `Object.create` để nhân bản đối tượng thay vì sao chép các thuộc tính trong `for..in`:

```js
// nhân bản đối tượng obj
let clone = Object.create(Object.getPrototypeOf(obj), Object.getOwnPropertyDescriptors(obj));
```

Nó sao chép tất cả các thuộc tính: liệt kê lẫn không liệt kê, thuộc trính truy cập cũng như thuộc tính dữ liệu, sao chép cả nguyên mẫu từ `[[Prototype]]`.

## Tóm tắt lịch sử

Nếu chúng ta đếm tất cả các cách để quản lý `[[Prototype]]`, sẽ có rất nhiều! Rất nhiều cách để cùng làm một thứ!

Tại sao lại như vậy?

Nó do lịch sử để lại.

- Thuộc tính `"prototype"` của constructor có từ rất lâu.
- Sau đó vào năm 2012: `Object.create` xuất hiện trong tiêu chuẩn JavaScript. Nó cho phép tạo các đối tượng với nguyên mẫu cho trước, nhưng không cho phép lấy/cài đặt nguyên mẫu. Vậy nên các trình duyệt tự thực thi phương thức truy cập `__proto__` để lấy và cài đặt nguyên mẫu trong thời gian này.
- Tới năm 2015: `Object.setPrototypeOf` và `Object.getPrototypeOf` được thêm vào tiêu chuẩn JavaScript để thực hiện các chức năng của `__proto__`. Vì `__proto__` có ở nhiều nơi, nó không được dùng và chuyển sang phụ lục B của tiêu chuẩn, là tùy chọn cho các môi trường không phải trình duyệt.

Kết quả là giờ chúng ta có tất cả những cách này để quản lý `[[Prototype]]`.

Tại sao `__proto__` bị thay thế bởi `getPrototypeOf/setPrototypeOf`? Đó là một câu hỏi thú vị, yêu cầu bạn phải hiểu tại sao `__proto__` không tốt. Hãy đọc để có câu trả lời.

<<<<<<< HEAD
```warn header="Không đặt lại `[[Prototype]]` trừ khi không cần chạy nhanh"
Về mặt kỹ thuật, chúng ta có thể lấy/cài đặt `[[Prototype]]` bất cứ lúc nào. Nhưng thường ta chỉ cài đặt nó một lần khi tạo đối tượng và sau đó không thay đổi nữa: `rabbit` thừa kế từ `animal`, sau đó không thay đổi nữa.
=======
```warn header="Don't change `[[Prototype]]` on existing objects if speed matters"
Technically, we can get/set `[[Prototype]]` at any time. But usually we only set it once at the object creation time, and then do not modify: `rabbit` inherits from `animal`, and that is not going to change.
>>>>>>> fb38a13978f6e8397005243bc13bc1a20a988e6a

Và JavaScript dựa vào điều này để tối ưu hóa tốc độ truy cập thuộc tính của các đối tượng. Thay đổi nguyên mẫu với `Object.setPrototypeOf` hoặc `obj.__proto__=` phá vỡ sự tối ưu này. Vì vậy hãnh tránh hành động này trừ khi bạn biết nó không ảnh hưởng tới mình.
```

<<<<<<< HEAD
## Đối tượng không có nguyên mẫu
=======
## "Very plain" objects [#very-plain]
>>>>>>> 4a8d8987dfc3256045e6b4a3bd8810ad3b25d1b3

Như đã biết, đối tượng có thể dùng như mảng liên kết để lưu các cặp key/value.

...Nhưng nếu chúng ta muốn lưu các key *do người dùng cung cấp* trong đối tượng, bạn thấy mọi key đều hoạt động ngoại trừ `"__proto__"`.

Cùng kiểm tra bằng ví dụ:

```js run
let obj = {};

let key = prompt("Nhập key?", "__proto__");
obj[key] = "giá trị nào đó";

alert(obj[key]); // [object Object], không phải "giá trị nào đó"!
```

Ở đây nếu nhập key là  `"__proto__"`, lệnh gán bị bỏ qua!

Không có gì ngạc nhiên. Thuộc tính `__proto__` là thuộc tính đặc biệt: nó phải là đối tượng hoặc `null`, một chuỗi không thể là nguyên mẫu được.

Trong những tình huống ta cần lưu key `"__proto__"` thì nó sẽ không được lưu và đây là nhược điểm của `__proto__`.

Nhược điểm trên không gây ra hậu quả quá khủng khiếp. Nhưng trong các trường hợp khác, chúng ta có thể vô tình gán một đối tượng vào `"__proto__"` và gây ra sự thay đổi nguyên mẫu mà ta nói ở trên. Điều này dẫn đến hậu quả rất nghiệm trọng vì toàn bộ mã sẽ chạy sai.

Điều tồi tệ nhất đối với nhà phát triển đó là các lỗi như vậy rất khó nhận thấy và chúng trở thành các lỗ hổng trong chương trình, đặc biệt khi JavaScript được dùng ở phía máy chủ.

<<<<<<< HEAD
Kết quả bất thường cũng có thể xuất hiện với `toString` -- nó có thể là hàm hoặc thuộc tính của các đối tượng có sẵn.
=======
Unexpected things also may happen when assigning to `toString` -- that's a function by default, and other built-in methods.
>>>>>>> fb38a13978f6e8397005243bc13bc1a20a988e6a

Làm sao để tránh vấn đề này?

Trước tiên, ta có thể chuyển sang dùng `Map`, và mọi thứ sẽ ổn.

Nhưng `Object` cũng có thể làm tốt chuyện này, bởi người tạo ra ngôn ngữ đã lường trước được vấn đề này từ lâu.

Thực ra `__proto__` là một thuộc tính truy cập được thừa kế từ `Object.prototype`:

![](object-prototype-2.svg)

Cho nên, nếu `obj.__proto__` được đọc hoặc ghi, các getter/setter tương ứng được lấy từ nguyên mẫu, nó lấy/cài đặt `[[Prototype]]` của đối tượng hiện tại.

Đúng như ta đã nói ở bài đầu chương này: `__proto__` chỉ là cách truy cập `[[Prototype]]`, không phải là `[[Prototype]]`.

Giờ, nếu muốn sử dụng một đối tượng làm mảng liên kết, chúng ta có thể thực hiện một mẹo nhỏ:

```js run
*!*
let obj = Object.create(null);
*/!*

let key = prompt("Nhập key?", "__proto__");
obj[key] = "giá trị nào đó";

alert(obj[key]); // "giá trị nào đó"
```

`Object.create(null)` tạo một đối tượng không có nguyên mẫu (`[[Prototype]]` là `null`):

![](object-prototype-null.svg)

Cho nên nó không thuộc tính truy cập `__proto__` lấy từ nguyên mẫu. Lúc này `__proto__` được xem là thuộc tính dữ liệu thông thường và ví dụ trên đã làm việc.

Chúng ta còn gọi đối tượng được tạo ra theo cách này là đối tượng "thuần", bởi nó còn đơn giản hơn cả một đối tượng trống `{...}`.

Nhược điểm của đối tượng "thuần" là thiếu đi rất nhiều phương thức có sẵn, ví dụ `toString`.

```js run
*!*
let obj = Object.create(null);
*/!*

alert(obj); // Lỗi (không có toString)
```

...Nhưng nó lại dùng tốt với mảng liên kết.

<<<<<<< HEAD
Mặt khác, các thuộc tính dạng `Object.something(...)`, ví dụ `Object.keys(obj)` không nằm trong nguyên mẫu, nên nó vẫn có thể sử dụng được với đối tượng "thuần nhất":
=======
Note that most object-related methods are `Object.something(...)`, like `Object.keys(obj)` -- they are not in the prototype, so they will keep working on such objects:
>>>>>>> fb38a13978f6e8397005243bc13bc1a20a988e6a


```js run
let chineseDictionary = Object.create(null);
chineseDictionary.hello = "你好";
chineseDictionary.bye = "再见";

alert(Object.keys(chineseDictionary)); // hello,bye
```

## Tóm tắt

Các phương thức hiện đại để truy cập trực tiếp đến nguyên mẫu là:

- [Object.create(proto[, descriptors])](mdn:js/Object/create) -- tạo đối tượng có nguyên mẫu là `proto` (có thể là `null`) và có thể có `descriptors` là các "property descriptor".
- [Object.getPrototypeOf(obj)](mdn:js/Object.getPrototypeOf) -- trả về `[[Prototype]]` của `obj` (giống như getter `__proto__`).
- [Object.setPrototypeOf(obj, proto)](mdn:js/Object.setPrototypeOf) -- cài đặt `proto` làm `[[Prototype]]` của `obj` (giống setter `__proto__`).

<<<<<<< HEAD
Getter/setter `__proto__` không an toàn khi muốn dùng đối tượng làm mảng liên kết. Bởi vì người dùng lưu `"__proto__"` làm key, sẽ gây ra lỗi không mong muốn và không thể đoán trước được.
=======
The built-in `__proto__` getter/setter is unsafe if we'd want to put user-generated keys in to an object. Just because a user may enter `"__proto__"` as the key, and there'll be an error, with hopefully light, but generally unpredictable consequences.
>>>>>>> 4a8d8987dfc3256045e6b4a3bd8810ad3b25d1b3

Để khắc phục có thể sử dụng `Object.create(null)` để tạo đối tượng "thuần" `__proto__`, hoặc chuyển sang dùng `Map`.

`Object.create` cũng cấp cấp một cách để nhân bản đối tượng.

```js
let clone = Object.create(Object.getPrototypeOf(obj), Object.getOwnPropertyDescriptors(obj));
```

We also made it clear that `__proto__` is a getter/setter for `[[Prototype]]` and resides in `Object.prototype`, just as other methods.

We can create an object without a prototype by `Object.create(null)`. Such objects are used as "pure dictionaries", they have no issues with `"__proto__"` as the key.

Other methods:

<<<<<<< HEAD
- [Object.keys(obj)](mdn:js/Object/keys) / [Object.values(obj)](mdn:js/Object/values) / [Object.entries(obj)](mdn:js/Object/entries) -- trả về mảng chứa tên/giá trị/cặp key-value của các thuộc tính liệt kê.
- [Object.getOwnPropertySymbols(obj)](mdn:js/Object/getOwnPropertySymbols) -- trả về mảng chứa key của các thuộc tính symbol riêng.
- [Object.getOwnPropertyNames(obj)](mdn:js/Object/getOwnPropertyNames) -- trả về mảng chứa key của các thuộc tính riêng.
- [Reflect.ownKeys(obj)](mdn:js/Reflect/ownKeys) -- trả về mảng chứa key của tất cả thuộc tính riêng (gồm cả symbol).
- [obj.hasOwnProperty(key)](mdn:js/Object/hasOwnProperty): trả về `true` nếu `obj` có thuộc tính riêng (không được thừa kế) có tên là `key`.
=======
- [Object.keys(obj)](mdn:js/Object/keys) / [Object.values(obj)](mdn:js/Object/values) / [Object.entries(obj)](mdn:js/Object/entries) -- returns an array of enumerable own string property names/values/key-value pairs.
- [Object.getOwnPropertySymbols(obj)](mdn:js/Object/getOwnPropertySymbols) -- returns an array of all own symbolic keys.
- [Object.getOwnPropertyNames(obj)](mdn:js/Object/getOwnPropertyNames) -- returns an array of all own string keys.
- [Reflect.ownKeys(obj)](mdn:js/Reflect/ownKeys) -- returns an array of all own keys.
- [obj.hasOwnProperty(key)](mdn:js/Object/hasOwnProperty): it returns `true` if `obj` has its own (not inherited) key named `key`.
>>>>>>> be342e50e3a3140014b508437afd940cd0439ab7

<<<<<<< HEAD
Chúng ta cũng làm sáng tỏ rằng `__proto__` là getter/setter của `[[Prototype]]` và nằm trong `Object.prototype`, như các phương thức khác.

Chúng ta có thể tạo đối tượng không có nguyên mẫu bằng `Object.create(null)`. Các đối tượng này thường dùng như "từ điển thuần túy", chúng không gặp phải vấn đề với key `"__proto__"`.

Tất cả các phương thức trả về các thuộc tính của đối tượng (như `Object.keys`...) -- chỉ trả về thuộc tính riêng. Nếu muốn cả các thuộc tính được thừa kế, chúng ta có thể sử dụng `for..in`.
=======
All methods that return object properties (like `Object.keys` and others) -- return "own" properties. If we want inherited ones, then we can use `for..in`.
>>>>>>> 4a8d8987dfc3256045e6b4a3bd8810ad3b25d1b3
