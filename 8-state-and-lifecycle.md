# State and Lifecycle

#1. Mở đầu

Như ở bài trước thì chúng ta đã tìm hiểu về props rồi, trong bài này chúng ta sẽ tìm hiểu vể state và lifecycle cũng như chúng tác động đến nhau như nào.

#2. Nội dung

##2.1. State

#####2.1.1 Vậy state là gì ?

- State là một **object** javascript.
- Cả state và props đều là **công cụ để lưu trữ data** ở trong component nhưng state được quản lý ở ngay trong component, ngược lại với props là được truyền vào (giống như parameter của function).

#####2.1.2 Tại sao phải dùng state ?

- Như bài trước chúng ta đã biết đó là những component được tạo ra bằng cách sử dụng function và nhận vào props rồi dùng props đó để render ra jsx component (stateless component).
- Nhưng trong thực tế thì component không chỉ làm những công việc đơn giản như thế, mà chúng ta còn cần phải xử lý dữ liệu và hiển thị nội trong component, ví dụ như một component form hoặc các component có thể tái sử dụng được. Trong các ví dụ tiếp theo chúng ta sẽ tìm hiểu.

#####2.1.3 Sử dụng state như thế nào

- Để sử dụng state ta bắt buộc phải sử dụng statefull component, ta sẽ khai báo statefull component như sau:

```Javascript
import React from 'react';

export default class TodoForm extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            textContent: '',
        };
        this.handleChange = this.handleChange.bind(this);
    }

    handleChange(e) {
        this.setState({ textContent: e.target.value });
    }

    render() {
        const { textContent } = this.state;
        return (
            <div>
                <input
                    type='text'
                    value={textContent}
                    onChange={this.handleChange}
                />
            </div>
        );
    }
}
```

- Vậy là ta đã có một component **TodoForm** và component này có thể import vào bất cứ đâu để sử dụng rồi.

##2.2. Lifecycle

#####2.2.1 Lifecycle là gì ?

- Đó là vòng đời của một component, từ khi nó được khởi tạo cho đến khi được mount vào DOM rồi cho đến khi nó được unmount và destroy khỏi DOM. Tất cả các component của React đều tuân theo vòng đời này.
- React đã cung cấp sẵn cho chúng ta các hook để chúng ta có thể gọi được vào các lifecycle methods này, theo đó chúng ta có thể control được component của chúng ta từ lúc sinh ra cho đến khi bị xoá đi.
- Lifecycle được chia làm 3 phần: **Mounting**, **Updating** and **Unmounting**

#####2.2.2 Thứ tự của các lifecycle

- Mounting methods:

  - **constructor()**: hook này sẽ chạy khi component được khởi tạo. Chúng ta sẽ khởi tạo state hoặc là bind các function cần thiết trong này.
  - **componentWillMount()**: hook này sẽ chạy ngay sau constructor chạy xong (hook này đã bị deprecate khi react 16 release, nhưng trong phạm vi bài này mình vẫn sẽ giới thiệu tất cả cho mọi người biết vì biết đâu có dự án cũ vẫn sử dụng).
  - **render()**: hook này sẽ được gọi khá nhiều trong vòng đời của component, đc gọi lần đầu tiên sau khi componentWillMount được gọi, khi setState được gọi, khi componentWillReceiveProps....
  - **componentDidMount()**: hook này được gọi sau khi component đã render html ra DOM lần đầu tiên, và cũng chỉ được gọi đúng một lần trong vòng đời của component.

- Updating methods:

  - **componentWillReceiveProps()**: hook này sẽ chạy khi component nhận được props mới (hàm này đã deprecate trong phiên bản react 16). Vì rất nhiều lúc state sẽ phụ thuộc vào các props nhận được này nên ta có thể setState ở trong hook này.
  - **shouldComponentUpdate()**: hook này sẽ chạy ngay sau khi componentWillReceiveProps chạy xong. Nó sẽ trả về true hay false, tương ứng nó sẽ quyết định component có render lại hay không theo giá trị true hay false đó.
  - **componentWillUpdate()**: hook này sẽ được gọi khá nhiều trong vòng đời của component, đc gọi lần đầu tiên sau khi componentWillMount được gọi, khi setState được gọi, khi componentWillReceiveProps....
  - **render()**:
  - **componentDidUpdate()**: hook này được gọi sau khi component đã render html ra DOM lần đầu tiên, và cũng chỉ được gọi đúng một lần trong vòng đời của component.

- Unmounting methods:
  - **componentWillUnmount()**: hook này được gọi ngay trước khi component bị xoá khỏi DOM. Hook này là nơi bạn có thể thực hiện các thao tác xoá timer, cancel request, cancel event listener....
  - **componentDidCatch()**: Hook này sẽ được gọi khi component xảy ra bất cứ lỗi nào, ta có thể sử dụng setState trong này để catch các lỗi xảy ra. (hook này mới được thêm vào trong react 16).

# 3. Tổng kết

- Trên đây là các kiến thức cần biết về state và các lifecycle của react.
