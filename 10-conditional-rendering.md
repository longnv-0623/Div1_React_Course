# Conditional Rendering

**Conditional Rendering** được hiểu đơn giản là việc ***hiển thị theo điều kiện***. Trong React, Conditional rendering hoạt động giống như cách mà biểu thức điều kiện trong Javascript, sử dụng các toán tử trong Javascript như `if` hay các toán tử điều kiện để biểu diễn trạng thái hiện tại, đồng thời cho phép React cập nhật UI mà liên kết tới chúng. Dưới đây là các cách để 

### 1. Sử dụng biến trung gian

Ta có thể sử dụng biến để lưu các phần tử. VD:

```jsx
function LoginButton(props) {
  return (
    <button onClick={props.onClick}>
      Login
    </button>
  );
}

function LogoutButton(props) {
  return (
    <button onClick={props.onClick}>
      Logout
    </button>
  );
}
```

Ta sẽ sử dụng 2 component trên tùy theo điều kiện đã đăng nhập hay chưa. Nếu đã đăng nhập, UI sẽ hiển thị `<LogoutButton />`, còn ngược lại sẽ hiển thị `<LoginButton />`. Ở đây ta sẽ sử dụng `if` - `else` cho việc hiển thị của biến được gán:

```jsx
class LoginControl extends React.Component {
  constructor(props) {
    super(props);
    this.handleLoginClick = this.handleLoginClick.bind(this);
    this.handleLogoutClick = this.handleLogoutClick.bind(this);
    this.state = {isLoggedIn: false};
  }

  handleLoginClick() {
    this.setState({isLoggedIn: true});
  }

  handleLogoutClick() {
    this.setState({isLoggedIn: false});
  }

  render() {
    const isLoggedIn = this.state.isLoggedIn;
    let button;

    if (isLoggedIn) {
      button = <LogoutButton onClick={this.handleLogoutClick} />;
    } else {
      button = <LoginButton onClick={this.handleLoginClick} />;
    }

    return (
      <div>
        <Greeting isLoggedIn={isLoggedIn} />
        {button}
      </div>
    );
  }
}

ReactDOM.render(
  <LoginControl />,
  document.getElementById('root')
);
```



## 2. Toán tử logic &&

Chúng ta có thể nhúng bất kì biểu thức JSX bằng cách bọc chúng trong `{ }`. Toán tử `&&` sẽ có ích trong các trường hợp điều kiện chỉ bao gồm 1 phần tử:

```jsx
function Mailbox(props) {
  const unreadMessages = props.unreadMessages;
  return (
    <div>
      <h1>Hello!</h1>
      {unreadMessages.length > 0 &&
        <h2>
          You have {unreadMessages.length} unread messages.
        </h2>
      }
    </div>
  );
}

const messages = ['React', 'Re: React', 'Re:Re: React'];
ReactDOM.render(
  <Mailbox unreadMessages={messages} />,
  document.getElementById('root')
);
```

Trong Javascript, `true && expression` sẽ trả về `expression`, còn `false && expression` thì sẽ trả về `false`. Có điều này là bởi toán tử && sẽ thực hiện phép toán lần lượt từ trái sang phải, nó sẽ dừng lại nếu có bất kì phần tử nào là `false` (nếu đó chưa phải là phần tử cuối cùng của phép logic) hoặc kết quả của biểu thức cuối cùng của toán tử logic (nếu đó là phần tử cuối cùng của phép logic). Chính vì thế, nếu điều kiện là true, phần tử xuất hiện sau `&&` sẽ là kết quả phép logic và hiển thị lên UI. Còn ngược lại, nó sẽ được bỏ qua.



## 3. Toán tử điều kiện 3 ngôi

Một phương thức khác được sử dụng đó là toán tử 3 ngôi trong Javascript. `condition ? true : false`:

```jsx
render() {
  const isLoggedIn = this.state.isLoggedIn;
  return (
    <div>
      {isLoggedIn ? (
        <LogoutButton onClick={this.handleLogoutClick} />
      ) : (
        <LoginButton onClick={this.handleLoginClick} />
      )}
    </div>
  );
}
```

Nếu điều kiện là `true`, UI sẽ hiển thị ra phần tử sau dấu `?`, còn `false` thì hiển thị ra phần tử sau dấu `:`. Ngoài ra, chúng ta còn có thể sử dụng các toán tử 3 ngôi lồng nhau. VD: `firstCondition ? (secondCondition ? true : false) : false`. Tuy nhiên, cách này được khuyến cáo không nên sử dụng.



## 4. Sử dụng null

Trong một số trường hợp, bạn muốn một số component sẽ tự ẩn đi mặc dù nó được render từ một component khác. Để là điều đó ta có thể trả về `null` thay vì render ra output của nó:

```jsx
function WarningBanner(props) {
  if (!props.warn) {
    return null;
  }

  return (
    <div className="warning">
      Warning!
    </div>
  );
}

class Page extends React.Component {
  constructor(props) {
    super(props);
    this.state = {showWarning: true};
    this.handleToggleClick = this.handleToggleClick.bind(this);
  }

  handleToggleClick() {
    this.setState(state => ({
      showWarning: !state.showWarning
    }));
  }

  render() {
    return (
      <div>
        <WarningBanner warn={this.state.showWarning} />
        <button onClick={this.handleToggleClick}>
          {this.state.showWarning ? 'Hide' : 'Show'}
        </button>
      </div>
    );
  }
}

ReactDOM.render(
  <Page />,
  document.getElementById('root')
);
```



## Tài liệu tham khảo

**Conditional Rendering** - https://reactjs.org/docs/conditional-rendering.html

