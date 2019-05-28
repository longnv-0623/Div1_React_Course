Cũng giống như Render Props, HOC - Higher Order Component thường được dùng để tái sử dụng logic của component.

# Higher Order Function (HOF)

Tổ tiên của HOC là HOF, đây là 1 khái niệm thường gặp trong FP (Functional Programming - lập trình hàm)

> HOF là 1 hàm, nhận tham số đầu vào là 1 hàm, hoặc trả về kết quả là 1 hàm, hoặc là có cả 2 điểm trên

#### Hàm nhận tham số là hàm

Ta sẽ lấy ví dụ về 1 hàm filter

```js
function filterByName(albums, name) {
  const filteredAlbums = []
  for (let i = 0; i < albums.length; i++) {
    if (albums[i].name === name) {
      filterdAlbums.push(albums[i])
    }
  }
  return filteredAlbums
}

function filterByArtist(albums, artist) {
  const filteredAlbums = []
  for (let i = 0; i < albums.length; i++) {
    if (albums[i].artist === artist) {
      filterdAlbums.push(albums[i])
    }
  }
  return filteredAlbums
}

albums = []
filterByName(albums, 'Lạc trôi')
filterByArtist(albums, 'Sếp Tùng')
```

Nếu sử dụng HOF nó sẽ trông như sau:

```js
function filterAlbums(albums, filterer) {
  const filteredAlbums = []
  for (let i = 0; i < albums.length; i++) {
    if (filterer(albums[i])) {
      filterdAlbums.push(albums[i])
    }
  }
  return filteredAlbums
}

albums = []
filterAlbums(albums, album => album.name === 'Lạc trôi')
filterAlbums(albums, album => album.artist === 'Sếp Tùng')
```

Trông thì có vẻ kinh khủng nhưng thực chất, `album => album.name === 'Lạc trôi'` chỉ là 1 function, nó được trigger bên trong vòng lặp, mỗi lần lặp sẽ trigger 1 lần, với tham số là phần tử thứ `i` trong mảng.

```js
filterer(albums[i])
```

hàm này nếu true sẽ chính là album thỏa mãn điều kiện, và ngược lại.

#### Hàm trả về hàm

Lấy ví dụ với 1 hàm nhận vào 1 số x, trả về kết quả là x * 2 + 1.

```js
function doubleThenAddOne(x) {
  return x * 2 + 1
}

doubleThenAddOne(2)	// 5
```

Hoặc với style HOF:

```js
function multiplyThenAdd(x) {
  return function multiply(multiplier) {
    return function add(increment) {
      return x * multiplier + increment
    }
  }
}

multiplyThenAdd(2)(2)(1) // 5
```

Trông có vẻ kinh khủng, nhưng thực ra chỉ là ta thực thi lần lượt 3 hàm khác nhau mà thôi

```js
const firstTrigger = multiplyThenAdd(2) // = function multiply(multiplier) { ... }
const secondTrigger = firstTrigger(2) 	// = function add(increment) { ... }
const thirdTrigger = secondTrigger(1)   // = 2 * 2 + 1 = 5
```

Cả 2 dạng hàm nhận tham số hàm, và hàm trả về 1 hàm, đều gặp rất nhiều trong React.

# Higher Order Component

Với HOF, thì ta nhận vào hàm, trả về hàm. HOC cũng tương tự, nhưng ta nhận vào Component, và trả về Component mới.

```jsx
const withInfiniteScroll = Component => {
  class WithInfiniteScroll extends React.Component {
    handler = () => {}
    
    // ... implement
    
    render() {
      return (
        <>
          <Component {...this.props} onSomething={this.handler} />
        	....
        </>
      )
    }
    
  }
  return WithInfiniteScroll
}

const UserList = props => { ... }
const InfinitedUserList = withInfiniteScroll(UserList)

const PostList = props => { ... }
const InfinitedPostList = withInfiniteScroll(PostList)

const App = () => (
	<>
  	<InfinitedUserList />
    <InfinitedPostList />
  </>
)
```

Từ cú pháp trên, ta dễ thấy `withInfiniteScroll` trả về 1 component mới `WithInfiniteScroll`, vì vậy, ta có thể thoải mái thêm những thứ mà 1 component có như **state**, **handler**, và truyền chúng vào props của input Component.

Nó giúp ta tái sử dụng được logic của component. Ví dụ như

* Infinite scrolling list
* Form validate, handle change
* Loading
* Authorization
* vv...

```jsx
<Component {...this.props} onSomething={this.handler} />
```

Đây là 1 yếu tố quan trọng của HOC, vì nếu ta sử dụng Component sau khi đã được wrap bởi HOC như sau:

```jsx
const withInfiniteScroll = Component => {
  class WithInfiniteScroll extends React.Component {
    ...
  }
}

const UserList = ({ users }) => { ... }

const InfinitedUserList = withInfiniteScroll(UserList)

const App = () => <InfinitedUserList users={['user1', 'user2']}/>
```

Mảng `['user1', 'user2']` thực chất đang được truyền vào component `WithInfiniteScroll` thay vì `UserList`, vì vậy, nếu sử dụng `users` ở trong `UserList` ta sẽ nhận được kết quả là `undefined`.

Vì thế, ta cần delegate những props cần thiết xuống cho wrapped component (mà ở ví dụ trên là `UserList`).

#### Compose HOC

Do đặc tính của HOC là nhận vào Component và return 1 Component (input giống output), nên ta có thể wrap 1 component bởi nhiều HOC.

Ví dụ như:

```jsx
const UserList = ({ users }) => { ... }
const withInfiniteScroll = (Component) => { ... } // return component
const withLoading = (Component) => { ... } // return component

const InfinitedScrollingUserList = withInfiniteScroll(UserList) // InfinitedScrollingUserList is a component
const LoadingInfinitedScrollingUserList = withLoading(InfinitedScrollingUserList) // LoadingInfinitedScrollingUserList is also a component
```

viết 1 kiểu xoắn não thì sẽ là:

```jsx
const LoadingInfinitedScrollingUserList = withLoading(withInfiniteScroll(UserList)) 
```

Sau khi wrap bởi rất nhiều HOC, Component của ta sẽ hóa chaos và có tất cả những tính năng của các HOC đã dùng. Ta có thể truy cập handler, state của những HOC thông qua props.

Tuy nhiên chính vì thế nó phát sinh ra những nhược điểm của HOC:

* Wrapper hell

  Thử wrap 1 component bởi 5 HOC rồi sử dụng React DevTools và bạn sẽ thấy bất ngờ:

![Káº¿t quáº£ hÃ¬nh áº£nh cho wrapper hell react](https://cdn-images-1.medium.com/max/1200/1*SU5_ws88Kh_Oio_L4Myhvg.png)

* Props bị override mà không biết override ở chỗ nào

  Nếu là HOC ta tự viết thì có thể không vấn đề, nhưng với những HOC của thư viện, khi gặp bug, có lẽ phải mất cả ngày để mò.

Hiện tại React 16.8 đã cho ra mắt Hook, nó giúp giải quyết những vấn đề trên, tuy nhiên nếu bạn định thông đêm để refactor tất cả class component sang hook thì đừng nên làm như vậy.