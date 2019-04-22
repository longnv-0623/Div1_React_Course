# List và key

#### Mở đầu

Khi làm việc với React, có lẽ mọi người cũng không lạ lẫm với props `key` nữa

ví dụ:

```jsx
const list = [1, 2, 3].map((num, index) => <li key={index}>{num}</li>)
```

Nhìn có vẻ rất ổn đúng không nào?

Không, nếu dùng ESLint `airbnb`, đoạn code trên sẽ đỏ lòm ngay, nó bảo ta

```
Prevent usage of Array index in keys
```

Tại sao lại như vậy, ta sẽ cùng tìm hiểu thêm về key.

#### Render list

VD:

```jsx
function NumberList(props) {
  // body
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```

Có nhiều cách để render 1 danh sách

Nếu theo phong cách Imperative

```jsx
function NumberList(props) {
  const numbers = props.numbers;
  const listNumbers = []
  numbers.forEach((number) => listNumbers.push(<li>{number}</li>))
	return <ul>{listNumbers}</ul>;
}
```

Phong cách Declarative (recommend)

```jsx
function NumberList(props) {
  const numbers = props.numbers;
	return <ul>{numbers.map(number => <li>{number}</li>)}</ul>;
}
```

Tuy nhiên khi chạy đoạn code trên, ta sẽ bị React nhắc nhở 

```
Warning: Each child in a list should have a unique "key" prop.
```

Nhanh trí thấy index của mảng chả phải là unique hay sao, thêm luôn vào cho khỏe.

```jsx
return <ul>{numbers.map((number, index) => <li key={index}>{number}</li>)}</ul>;
```

OK, không còn warning nữa.

#### Indexes as List keys Anti-pattern

Nhiều khi lười, ta vội viết key index cho hết warning. Tuy nhiên đời sẽ chỉ màu hồng nếu component của ta không có state.

Key là props đặc biệt để React nhận biết 1 phần tử trong danh sách bị remove đi, hay thêm mới vào, hoặc bị thay đổi. Một khi key của component bị thay đổi, component đó sẽ lập tức unmount rồi sau đó tạo component mới thay vì chỉ re-render. Còn nếu key của component không thay đổi, tất nhiên nó chỉ re-render.

Lấy ví dụ với 1 đoạn code như sau:

```jsx
class PostListItem extends React.Component {
  state = {
    isLiked: this.props.post.isLiked
  };

  render() {
    const { post } = this.props;
    const { isLiked } = this.state;

    return (
      <li>
        {post.id}, {isLiked && <span>&hearts;</span>}
      </li>
    );
  }
}

// ...codes
const postList = posts.map((post, index) => (
  <PostListItem key={index} post={post} />
))
```

Ví dụ hiện tại ta đang có 1 mảng

```js
posts = [{ id: 1, isLiked: true }, { id: 2, isLiked: false }]
```

Sau đó ta muốn prepend thêm 1 phần tử vào đầu danh sách, mảng `posts` của ta trở thành

```js
posts = [{ id: 3, isLiked: false }, { id: 1, isLiked: true }, { id: 2, isLiked: false }]
```

Hãy quan sát các component của ta trước và sau khi thêm post id=3

![](https://images.viblo.asia/fc789727-db41-4b6c-9f06-8f704d6e6539.jpg)

Hình như là có gì đó sai sai. Tại sao post id=3 của ta có `isLiked = false` nhưng state của nó lại có giá trị `true`?

Và post id=1 state sao lại bị chuyển thành `false` mất rồi?

Câu trả lời là vì React đã re-render lại component thay vì re-mount phần tử đầu tiên, vì vậy 

```js
state = {
  isLiked: this.props.post.isLiked
};
```

chỉ chạy duy nhất 1 lần tại constructor, sẽ không bao giờ được gọi lần thứ 2.

List render lần đầu:

* key = 0
* key = 1

Sau khi prepend:

- key = 0
- key = 1
- key = 2

Do key không đổi, nên với 2 phần tử đầu, React nghĩ chỉ có props/state nó thay đổi mà thôi, re-render cho phát, state vẫn giữ nguyên => dẫn đến trường hợp post id=3 (key = 0 mới) đang sử dụng state của post id=1 (key = 0 cũ)

Còn với phần tử key = 2, thực chất trước đã mount rồi, nhưng do key này chưa tồn tại, nên React re-mount phát cho sướng.

Vì những lý do trên, chúng ta nên sử dụng key là id của post, hay uuid, hay bất cứ thứ gì unique của nó, điều này sẽ giúp ta tránh được những case trên.

Ta hãy viết lại đoạn code trên:

```jsx
const postList = posts.map(post => (
  <PostListItem key={post.id} post={post} />
))
```

Khi đó hãy xem list của ta trong lần render đầu và sau khi prepend

Render lần đầu

- key = 1
- key = 2

Render lần 2:

* key = 3
* key = 1
* key = 2

2 element có key = 1, key = 2 không thay đổi gì, phần tử key=3 được thêm mới. Đúng với ý của ta.

Bạn có thể vào đây để xem ví dụ đầy đủ <https://codesandbox.io/s/2z8y7oop7y>