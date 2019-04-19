#   Mở đầu

Nếu bạn đã từng sử dụng Context API hay thư viện Formik, có lẽ sẽ không xa lạ gì với những đoạn code như sau:

```jsx
<List 
    items={data} 
    renderItem={item => <ListItem item={item} />} 
/> 
```

 hay  là

 ```jsx
<Formik>
	{({ values, errors }) => (
    	....
    )}
</Formik>
 ```

Có thể trông hơi khác nhau một chút nhưng chúng đều là 1 pattern khá phổ biến của React - **render props**

Mục đích của nó là để tái sử dụng code, như là khi làm việc với form, ta sẽ cần những phương thức như là `handleChange`, `values`, `errors`, ... Khi ta có nhiều form, tất nhiên ta sẽ không muốn cứ mỗi form lại viết lại những hàm buồn chán này.

Và sao lại gọi là **render props**? Đơn giản là vì ta truyền vào component 1 props có khả năng render nên nó có tên như vậy.

# Tại sao lại cần render props?

**Render Props** kế thừa tư tưởng của Dependency Injection / Dependency Inversion.

Hãy quan sát 1 ví dụ đơn giản trong JS.

```js
// Non-DI
const f = () => "The value is: " + Math.random();
// Usage
f();

// DI
const f = g => "The value is: " + g();
// Usage
f(Math.random);
```

Như đã thấy, ta đã tách phần logic của hàm ra ngoài, và inject nó vào mỗi khi gọi hàm, từ đó hàm `f` của ta có thể sử dụng trong nhiều trường hợp hơn.

Còn đối với React thì sao?

Khi không dùng render props:

```jsx
const UserList = (props) => (
	<div>
    	<header>User list</header>
        {props.users.map(user => <UserItem user={user}/>)}
    </div>
)

const HashtagList = (props) => (
	<div>
    	<header>Hashtag list</header>
        {props.hashtags.map(hashtag => <HashtagItem hashtag={hashtag} />)}
    </div>
)

const App = () => (
	<>
    	<UserList users={[{ name: 'user1' }, { name: 'user2' }]} />
    	<HashtagList hashtags={['worldcup', 'depzai']} />
    </>
)
```

Cùng thử chuyển qua render props

```jsx
# List.jsx
const List = (props) => (
    <div>
        <header>List data</header>
        <div>
            {props.data.map(props.renderItem)}
        </div>
    </div>
)
```

Khi đó ta có thể tái sử dụng List với nhiều trường hợp khác nhau:

```jsx
const App = () => (
	<>
    	<List data={[{ name: 'user1' }, { name: 'user2' }]} renderItem={item => <UserItem user={item} />} />
        <List data={['worldcup', 'depzai']} renderItem={item => <HashtagItem hashtag={item} />} />
    </>	
)
```

Đó chỉ là ví dụ đơn giản, nhưng trong thực tế, `<List />` của ta có thể có thêm lifeCycle để phục vụ mục đích fetch dữ liệu, cancel request khi unmount để tránh memory leak, hay là xử lý scrolling để làm infinite scrolling. Và ta sẽ không cần phải viết đi viết lại những hàm giống nhau đó mỗi khi cần hiển thị một list dữ liệu.

# **Children** as a Function

Có thể ở một vài nơi, bạn cũng đã nhìn thấy từ này rồi, thực ra nó cũng chỉ là 1 style khác của **Render Props** mà thôi, vì children cũng chính là một props.

Theo đó bạn sẽ viết children thành 1 function để render, thay vì viết những element như bình thường. Có thể dễ dàng thấy pattern này nếu bạn sử dụng ContextAPI.

```jsx
<MyContext.Consumer>
  {value => /* render something based on the context value */}
</MyContext.Consumer>
```

# Nhược điểm

#### Giới hạn trong hàm render

Với **Render props**, bạn sẽ bị hạn chế sử dụng những function/varoable mà Component cung cấp ở trong hàm render. Nếu bạn muốn sử dụng chúng ở ngoài, trong lifecycle, thì sẽ gặp một chút khó khăn hơn.

Ví dụ như khi ta có một CurrentUserContext để chứa thông tin người dùng hiện tại, ta sẽ thường sử dụng nó như sau:

```jsx
const ProfileScreen = () => (
	<CurrentUserContext.Consumer>
        {props => (
        	<>
            	<UserInfo user={props.user} />
            	<UserFriends friends={props.user.friends} />
            </>
        )}
    </CurrentUserContext.Consumer>
)
```

Thế nhưng khi ta muốn sử dụng nó trong lifecycle thì tính sao?

```jsx
class ProfileScreen extends React.Component {
    componentDidMount() {
        // I want `user` can be accessed here
    }
    
    render() {
        return (
            <CurrentUserContext.Consumer>
                {props => ....}
            </CurrentUserContext.Consumer>
        )
    }
    
}
```

Tất nhiên là ta vẫn có cách, đó là wrap `ProfileScreen` bởi `CurrentUserContext.Consumer`

```jsx
class ProfileScreen extends React.Component {
    componentDidMount() {
        // I want `user` can be accessed here
    }
    
    render() {
        ....
    }
    
}

export default props => (
	<CurrentUserContext.Consumer>
        {({ user }) => <ProfileScreen {...props} user={user} />}
    </CurrentUserContext.Consumer>
)
```

hoặc nếu có nhiều nơi cần `CurrentUserContext`, ta có thể sử dụng HOC


```jsx
const withCurrentUserContext = Component => props => (
	<CurrentUserContext.Consumer>
        {({ user }) => <Component {...props} user={user} />}
    </CurrentUserContext.Consumer>
)
```

Ngoài ra, HOC và **Render Props** cũng khá giống nhau về mục đích, đó là tái sử dụng lại những logic thường gặp. Tuy nhiên với HOC, thì ta có thể truy cập thẳng những function/variable bất cứ đâu trong component. Ta cũng có thể kết hợp cả 2 kĩ thuật này lại với nhau như trên. Tùy tình huống hãy linh hoạt áp dụng chúng.

#### Wrapper hell

Sử dụng HOC hay **Render props** rất dễ khiến ta rơi vào wrapper hell, y hệt như callback hell thời chưa có Promise vậy.

```js
// callback hell
fs.readdir(source, function (err, files) {
  if (err) {
    console.log('Error finding files: ' + err)
  } else {
    files.forEach(function (filename, fileIndex) {
      console.log(filename)
      gm(source + filename).size(function (err, values) {
        if (err) {
          console.log('Error identifying file size: ' + err)
        } else {
          console.log(filename + ' : ' + values)
          aspect = (values.width / values.height)
          widths.forEach(function (width, widthIndex) {
            height = Math.round(width / aspect)
            console.log('resizing ' + filename + 'to ' + height + 'x' + height)
            this.resize(width, height).write(dest + 'w' + width + '_' + filename, function(err) {
              if (err) console.log('Error writing file: ' + err)
            })
          }.bind(this))
        }
      })
    })
  }
})

```

React version:

![](https://images.viblo.asia/c5d89a33-63d0-4be2-8b47-844f389ab6b8.jpg)

# Kết luận

**Render props** là một kĩ thuật khá phổ biến trong React. Nắm bắt được nó, khi join các dự án React, ta sẽ không bị bỡ ngỡ trước những dòng code lạ lùng. 

Tuy nhiên khi dùng những pattern này, cũng cần chú ý, nếu không sẽ rơi vào hố bom wrapper hell như trên luôn :v

Gần đây React cũng giới thiệu **Hooks**, nó cũng nhằm tái sử dụng lại những logic lặp đi lặp lại trong component, hơn nữa còn được giới thiệu là xóa bay ~~vết bẩn~~ wrapper hell :v Tuy nhiên mình thấy nó khá là ma giáo nên cũng ít dùng.

# Tham khảo

<https://reactjs.org/docs/context.html>

<https://reactjs.org/docs/render-props.html>

<https://medium.com/byteconf/render-props-in-react-6081b6fa3593>

<https://jaredpalmer.com/formik/docs/overview>

<http://callbackhell.com/>

<https://blog.logrocket.com/react-custom-hooks-and-the-death-of-render-props-a0ce5cba387f>

<https://twitter.com/acdlite/status/955955121979969537/photo/1>

<https://reactjs.org/docs/hooks-intro.html>