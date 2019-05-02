# Mở đầu

Khác với jQuery, ReactJS phải xử lý rất nhiều logic, mỗi component đều nhận vào một lượng input và trả về output nào đó, nhưng nếu nó không chạy đúng theo những gì ta nghĩ thì sao?

Tất nhiên là sẽ phải ngồi debug rồi. Dưới đây là một số công cụ hữu ích khi làm việc với ReactJS.

* DevTools
* React DevTools

Ngoài ra còn có Redux DevTools nếu bạn dùng redux

# Tools

#### DevTools

Trong bài sẽ sử dụng Chrome DevTools.

Khi code ta chạy không như dự tính, trước hết ta sẽ phải log đống biến ra và định hình xem bug xuất hiện ở vị trí nào. 

Nhưng ta sẽ không muốn dùng đặt 1 đống câu lệnh `console.log` trong code rồi ra console xem kết quả.

Khi đó hãy dùng `debugger`

VD: Ta có 1 hàm `setInterval` để tăng giá trị `count` sau mỗi 1 giây, nhưng không hiểu sao chỉ thấy `count` nhảy duy nhất 1 lần từ 0 - 1, sau đó chết ngóm luôn. Hãy đặt `debugger`

```jsx
import React, { useEffect, useState } from 'react'

import Utils from './util'

function App() {
	const [count, setCount] = useState(0)
  
  useEffect(() => {
    setInterval(() => {
      debugger
      setCount(count + 1)
    }, 1000)
  }, [])
  
  return <p>Counter: {count}</p>
}
```

Khi đó ta có thể check giá trị những biến ta muốn check (miễn là trong phạm vi cho phép) trước khi `setCount` được gọi, ta nhận thấy `count` luôn nhận giá trị `0`, sau đó thì ngồi nghĩ cách fix thôi :v

Có một vài lưu ý khi dùng `debugger`.

* Những module được import sẽ không truy cập được trong khi debug, ta có thể dùng 1 trick sau:

  ```jsx
  setInterval(() => {
    const u = Utils
    debugger
    setCount(count + 1)
  }, 1000)
  ```

  Sau đó ta có thể dùng `u` như module `Utils` bình thường.

* Biến `error` khi dùng `try - catch` cũng không truy cập được trong quá trình debug. Để giải quyết ta cũng dùng cách như trên

  ```js
  try {
    doSomethingDangerous()
  } catch (error) {
    const e = error
    debugger
  }
  ```

* Nếu Console mà không hiển thị trong tab Source thì hãy bấm phím `Esc` để bật nó lên.

#### React DevTools

Công dụng: 

###### Inspect React element

![](https://images.viblo.asia/6ed237b7-2188-4d42-a678-099c5a83143b.png)

Cũng giống như inspect DOM vậy, ta có thể vào đây để xem React Component của ta được bố trí như thế nào.

Trên là demo khi inspect 1 trang trên môi trường production, nên tên component toàn `t` hết :v

###### Inspect props, state của một component

Chọn một component cần inspect, hãy chắc chắn đó là một component chứ không phải là 1 dom element.

Ta có thể dễ dàng quan sát thấy ở bên phải có 1 tab hiển thị props và state hiện tại của element đúng không nào. Nhưng không dừng lại ở đó, ta còn có thể sử dụng `$r` để truy cập tới ref của nó.

* `$r.props`
* `$r.state`
* `$r.current`
* ...

VD như component đang được highlight ở trên

```js
>	$r.state
<	{currentLocale: "en", changeLocale: ƒ}
```

