**State** là thành phần không thể thiếu trong ứng dụng React. 

Thông qua [Thinking in React](<https://reactjs.org/docs/thinking-in-react.html>), chúng ta đã biết khi nhiều component sử dụng chung 1 **state**, ta cần đặt nó tại component cha chung gần nhất của những component này.

Tuy nhiên đối với **state** mà chúng ta cần dùng ở khắp nơi trong ứng dụng, nếu sử dụng cách trên, ta sẽ phải truyền state qua lần lượt từng component cha của component cần sử dụng **state**. Những **state** dạng này còn được gọi là **global state**. 

Một ví dụ đơn giản là những biến `isAuthenticated` (kiểm tra user đã đăng nhập hay chưa), hay `authToken` (để gửi kèm HTTP request với những request yêu cầu login, permission), ...

Để khắc phục tình trạng này, context đã ra đời.

Context đã có từ khá lâu, tuy nhiên ở những phiên bản gần đây mới stable, và cũng được support khá tốt ([contextType](<https://reactjs.org/docs/context.html#classcontexttype>), [useContext](<https://reactjs.org/docs/hooks-reference.html#usecontext>))

Không như Redux cồng kềnh, khi ta muốn viết 1 action đơn giản, ta phải tạo 1 đống thứ, nào là reducer, action, saga, ... Context rất dễ sử dụng, và ta cũng không tốn công cài và học cách sử dụng một thư viện 3rd-party.

# Cách sử dụng

#### Cơ bản

```jsx
import React from 'react'

const defaultState = {
	isAuthenticated: false,
  authToken: ''
}

const AuthContext = React.createContext(defaultState)

export default function App = () => (
	<AuthContext.Provider value={{ isAuthenticated: true, authToken: 'abcd' }}>
  	<AuthContext.Consumer>
    	{(authContext) => (
        <div>{authContext.isAuthenticated ? 'Welcome back' : 'Please sign in'}</div>
      )}
    </AuthContext.Consumer>
  </AuthContext.Provider>
)
```

Các bước thực hiện như sau:

* **Bước 1**: 

  Ta sử dụng `createContext` để tạo **context**, ta có thể truyền vào giá trị mặc định của **context**.

* **Bước 2**: 

  Khai báo `Context.Provider`. Hãy đảm bảo `Consumer` luôn là child của `Provider`

  Trong khi khai báo `Provider`, ta có thể ghi đè giá trị mặc định mà ta đã truyền vào context ở bước 1 bằng việc sử dụng props `value`

* **Bước 3:**

  Sử dụng `Context.Consumer` để có thể truy cập vào những giá trị có trong context.

  Cách render của `Consumer` được gọi là **render props** nếu bạn cảm thấy lạ lẫm với syntax này.

Phía trên là một ví dụ đơn giản, ta chỉ đọc giá trị trong context, chắc hẳn mọi người sẽ thắc mắc:

> Làm thế nào để thay đổi giá trị trong context?

Vậy ta hãy thử nghĩ xem, thứ gì trong React dùng để lưu giá trị thay đổi theo thời gian?

Đó chính là **state**. Ta sẽ không sử dụng trực tiếp `Provider` như trên nữa, mà sẽ wrap nó bởi một stateful component khác. Ta sẽ dùng chính **state** này làm `value` của `Provider`, và hãy nhớ truyền thêm một vài setter nữa.

```jsx
import React from 'react'

const noop = () => {}

const defaultState = {
	isAuthenticated: false,
  authToken: '',
  signIn: noop,
  signOut: noop
}

const AuthContext = React.createContext(defaultState)

class AuthProvider extends React.PureComponent {
  signIn = authToken => this.setState({ isAuthenticated: true, authToken })

	signOut = () => this.setState(defaultState)
  
  state = {
    ...defaultState,
    signIn: this.signIn,
    signOut: this.signOut
  }

  render() {
    return <AuthContext.Provider value={this.state} {...this.props} />
  }
}

export default function App = () => (
	<AuthProvider>
  	<AuthContext.Consumer>
    	{(authContext) => (
        <div>
          <h2>{authContext.isAuthenticated ? 'Welcome back' : 'Please sign in'}</h2>
          
          {authContext.isAuthenticated ? (
            <button onClick={authContext.signOut} type="button">Sign out</button>
          ) : (
            <button onClick={() => authContext.signIn('sample token')} type="button">Sign in</button>
          )}
        </div>
      )}
    </AuthContext.Consumer>
  </AuthProvider>
)
```

#### Sử dụng context ngoài render

Nếu sử dụng **render props** ta vẫn có thể dùng context ngoài hàm render, nhưng cú pháp sẽ không được đẹp mắt.

Khi đó, hãy tách component cần sử dụng context ra, và truyền context vào props của nó tại component cha. 

```jsx
import React from 'react'

// ...

class Sample extends React.PureComponent {
  componentDidMount() {
    this.props.signIn()
  }
  
  render() {
    // ...
  }
}
  
export default function App = () => (
	<AuthProvider>
  	<AuthContext.Consumer>
    	{(authContext) => (
        <Sample signIn={authContext.signIn} />
      )}
    </AuthContext.Consumer>
  </AuthProvider>
)
```

Khi cần sử dụng AuthConsumer ở nhiều nơi, việc viết đi viết lại nó khá là vất vả phải không nào, để giải quyết, ta có thể sử dụng **Higher Order Component** (aka HOC).

```jsx
// ...

const withAuthContext = Component => props => (
	<AuthConsumer>
  	{(authContext) => (
    	<Component auth={authContext} {...props} />
    )}
  </AuthConsumer>
)

// ...

const SampleWithContext = withAuthContext(Sample)

export default function App = () => (
	<AuthProvider>
    <SampleWithContext />
  </AuthProvider>
)
```

###### contextType

React 16.6 cho ra mắt `static contextType` giúp ta tránh phải viết HOC, hay viết đi viết lại hàm render props.

Lúc này, ta sẽ truy cập value của **context** thông qua `this.context`

```jsx
// ...

class Sample extends React.PureComponent {
  static contextType = AuthContext
  
  componentDidMount() {
    this.context.signIn()
  }
  
  render() {
    // ...
  }
}
```

Tuy nhiên, đáng tiếc là nó chỉ support nếu component của ta sử dụng 1 context, nếu ta cần dùng nhiều context, thì chỉ còn cách HOC, hoặc viết 2 cái render props mà thôi.

###### useContext

React 16.8 đã cho ra mắt Hook. Ngoài `useState` và `useEffect` thần thánh ra, còn có 1 API nữa là `useContext`

Ta sẽ sử dụng nó như sau:

```jsx
import { useEffect, useContext } from 'react'

function Sample() {
  const auth = useContext(AuthContext)
  const context2 = useContext(Context2)
  const context3 = useContext(Context3)
  
  useEffect(() => {
    auth.signIn()
  }, [])
  
  return (
  	// ...
  )
}
```

Có thể thấy là `useContext` hỗ trợ cả một lẫn nhiều context, khá tiện lợi.

# Sử dụng nhiều Provider của cùng một Context

Lấy ví dụ với Twitter, ta có thể nhận thấy 1 bài viết (tweet) có rất nhiều bình luận (cũng gọi là tweet), trong mỗi bình luận lại có một bình luận khác, cứ thế vô tận level.

Ở màn hình detail, tweet sẽ được hiển thị cùng với những bình luận con (trực tiếp) của nó, khi click vào mỗi tweet, một modal sẽ được hiển thị, nó cũng có cấu trúc, logic giống hệt màn hình detail vừa xong của ta.

Không lẽ ta sẽ phải viết 2 component với state, và logic y hệt nhau? 

Chắc chắn là không ai thừa bơ làm vậy rồi. Để có thể reuse được logic, ta có thể dùng **HOC**, **Render Props** hay **Hook**.

Ngoài ra **Context** cũng làm được điều tương tự.

Ta có thể dùng nhiều hơn 1 `Provider` của một **context**, tất cả `Provider` sẽ tách biệt với nhau, `Consumer` sẽ nhận giá trị theo `Provider` gần nó nhất.

Ứng dụng ngay vào bài toán trên, ta chỉ cần viết một Context, rồi sử dụng nó

```jsx
const TweetsContext = React.createContext(...)

const TweetsProvider = () => { ... }
 
const Screen = () => {
  const [currentSubTweetId, setCurrentSubTweetId] = useState(null)
  
  const currentTweetId = this.props.match.params.id
  
  return (
  	<TweetsProvider id={currentTweetId}>
      ....
      
    	<Modal>
      	<TweetProvider key={currentSubTweetId} id={currentSubTweetId}>
        	....
        </TweetProvider>
      </Modal>
    </TweetsProvider>
  )
}

// hoặc tối ưu hơn

const Screen = () => {
  const [currentSubTweetId, setCurrentSubTweetId] = useState(null)
  
  const currentTweetId = this.props.match.params.id
  
  return (
    <>
      <TweetsProvider id={currentTweetId}>
        ....
      </TweetsProvider>
    
      <Modal>
        <TweetProvider key={currentSubTweetId} id={currentSubTweetId}>
          ....
        </TweetProvider>
      </Modal>
    </>
  )
}
```

# Re-render

Khi ta thay đổi giá trị props `value` truyền vào một `Provider`

* Children của `Provider` sẽ không re-render
* Những children của `Consumer` sẽ re-render
* Những component sử dụng `contextType` hay `useContext`  sẽ re-render

Vì vậy, với những trường hợp không cần sử dụng context ở ngoài `render`, để tối ưu, hãy dùng cách cơ bản nhất:

```jsx
const Sample = () => (
  <Context.Consumer>
    {context => ....}
  </Context.Consumer>
)
```

# So sánh với Redux

#### Ưu điểm

* Không cần import thư viện 3rd party 
* Cú pháp React quen thuộc, dễ sử dụng
* Không cồng kềnh như Redux
* Nếu sử dụng 2 provider của cùng 1 context, chúng sẽ isolate với nhau. Trong khi Redux, để đạt được nhiều này phải tạo ra 2 state giống nhau.

#### Nhược điểm

* Muốn persist data phải làm thủ công, không có lib như **redux-persist**