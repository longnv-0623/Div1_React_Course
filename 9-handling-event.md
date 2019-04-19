# Handling Events

**Handling Events (Xử lý sự kiện)** được hiểu đơn giản là các thao tác của người dùng tác động lên các phần tử DOM.

### Sự khác biệt giữa React và HTML

- Tên sự kiện trong React sử dụng kiểu viết camelCase chứ không phải lowercase như HTML

  - *VD*: `onClick`, `onBlur`, `onFocus`, ... 

- Đối với syntax JSX, chúng ta có thể truyền một function như một event handler, thay vì sử dụng string như HTML

  ```jsx
  <button onClick={activateLasers}>
  	Activate Lasers
  </button>
  ```

  thay vì

  ```jsx
  <button onclick="activateLasers()">
    Activate Lasers
  </button>
  ```

- Trong React, để ngăn chặn các hành động của phần tử, ta cần sử dụng funtion `preventDefault` được định nghĩa mặc định của các đối tượng `event`, thay vì trả về `false` như HTML:

  ```jsx
  function ActionLink() {
    function handleClick(e) {
      e.preventDefault();
      console.log('The link was clicked.');
    }
  
    return (
      <a href="#" onClick={handleClick}>
        Click me
      </a>
    );
  }
  ```

  thay vì:

  ```jsx
  <a href="#" onclick="console.log('The link was clicked.'); return false">
    Click me
  </a>
  ```

***Note:*** Ở đây chúng ta có thể để ý tới đối tượng `e` xuất hiện trong function được gán với event `onClick`. Mặc dù không được truyền một cách trực tiếp khi gọi hàm nhưng đối tượng `e` vẫn không bị báo `undefined`. Điều đó là do tất cả các event handler của React luôn được truyền vào một đối tượng của class `SyntheticEvent`. Đối tượng này là sự kiện tổng hợp, tương thích với các loại trình duyệt khác nhau.

- Khi sử dụng React, chúng ta không cần gọi `addEventListener` để thêm sự kiện cần lắng nghe sau khi phần tử DOM được tạo thành. Thay vào đó, chỉ cần khai báo một sự kiện lắng nghe khi phần tử được khai báo lần đầu. Khi định nghĩa một component theo kiểu ES6, một event handler sẽ là một phương thức trong class đó.

  ```jsx
  class Toggle extends React.Component {
    constructor(props) {
      super(props);
      this.state = {isToggleOn: true};
  
      // This binding is necessary to make `this` work in the callback
      this.handleClick = this.handleClick.bind(this);
    }
  
    handleClick() {
      this.setState(state => ({
        isToggleOn: !state.isToggleOn
      }));
    }
  
    render() {
      return (
        <button onClick={this.handleClick}>
          {this.state.isToggleOn ? 'ON' : 'OFF'}
        </button>
      );
    }
  }
  
  ReactDOM.render(
    <Toggle />,
    document.getElementById('root')
  );
  ```

  ***Note***: Cần phải thận trọng với việc sử dụng *this* trong việc JSX callbacks. Trong Javascript, các method của class không bị ràng buộc bởi mặc định. Nếu quên không gọi *this.handleClick* mà gọi *onClick* khi đó *this* sẽ bị lỗi *undefined* khi mà function được gọi. Nếu cảm thấy bất tiện, ta có thể sử dụng `arrow function`, một trong những tính năng của ES6:

  ```jsx
  class LoggingButton extends React.Component {
    handleClick() {
      console.log('this is:', this);
    }
  
    render() {
      // This syntax ensures `this` is bound within handleClick
      return (
        <button onClick={(e) => this.handleClick(e)}>
          Click me
        </button>
      );
    }
  }
  ```



### Truyền tham số cho Event Handlers

Để truyền tham số vào event handlers, có thể dùng theo 1 trong số cách sau:

```jsx
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```

Trong cả hai trường hợp, tham số `e` đại diện cho React Event sẽ được truyền như là tham số thứ 2 sau `id`.