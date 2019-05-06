# JSX

Hãy cùng xem cách khai báo biến sau:

```
const element = <h1>Hello, world!</h1>;
```

Cú pháp trên không phải là cách khai báo một string cũng không phải là cú pháp HTML. Nó được gọi là JSX - cú pháp mở rộng cho JavaScript. JSX là cú pháp giống như XML/HTML được dùng bởi React để  cung cấp khả năng viết XML/HTML chung với JavaScript/React chung trong code.


JSX is an XML/HTML-like syntax used by React that extends ECMAScript so that XML/HTML-like text can co-exist with JavaScript/React code. React không bắt buôc bạn phải dùng JSX nhưng JSX được khuyến khích sử dụng để miêu tả, hình dung UI dễ dàng hơn và đồng thời cũng giúp React show ra các error và warning messages dễ hiểu hơn.

Cú pháp sẽ được biên dịch bởi các preprocessors (như Babel) để biết các đoạn text giống HTML được tìm thấy trong các file JavaScript thành các object JavaScript.

Bằng cách sử dụng JSX bạn sẽ có thể viết như sau bên trong code của mình:

```
var nav = (
    <ul id="nav">
      <li><a href="#">Home</a></li>
      <li><a href="#">About</a></li>
      <li><a href="#">Clients</a></li>
      <li><a href="#">Contact Us</a></li>
    </ul>
);
```

Và Babel sẽ biến chúng thành cú pháp giúp các JavaScript engine có thể hiểu được.

```
var nav = React.createElement(
   "ul",
   { id: "nav" },
   React.createElement(
      "li",
      null,
      React.createElement(
         "a",
         { href: "#" },
         "Home"
      )
   ),
   React.createElement(
      "li",
      null,
      React.createElement(
         "a",
         { href: "#" },
         "About"
      )
   ),
   React.createElement(
      "li",
      null,
      React.createElement(
         "a",
         { href: "#" },
         "Clients"
      )
   ),
   React.createElement(
      "li",
      null,
      React.createElement(
         "a",
         { href: "#" },
         "Contact Us"
      )
   )
);
```

Như ta thấy ở trên một trong các cách nguyên thủy trong React để miêu tả, tạo component đó là sử dụng `React.createElement()`. Nhưng ta có thể thấy rằng việc dùng `React.createElement()` là khá dài và khó hiểu nên JSX thường được khuyến khích sử  dụng hơn. Ta cũng có thể coi JSX là cú pháp tắt để sử dụng `React.createElement()`.

### Khai báo khai báo Attribtes/Props

```
var styles = {backgroundColor:'red'};
var tested = true;
var text = 'text';

var reactNodeLi = <li id=""
                      data-test={tested?'test':'false'}
                      className="blue"
                      aria-test="test"
                      style={styles}
                      foo="bar">
                          {text}
                  </li>;

ReactDOM.render(reactNodeLi, document.getElementById('app1'));
```

Và Babel sẽ chuyển nó thành

```
var reactNodeLi = React.createElement(
    'li',
    { id: '',
        'data-test': tested ? 'test' : 'false',
        className: 'blue',
        'aria-test': 'test',
        style: styles,
        foo: 'bar' },
    text
);
```

Và kết quả HTML cuối cùng

```
<div id="app1">
    <li id="true"
        data-test="test"
        class="blue"
        aria-test="test"
        style="background-color:red;"
        data-reactid=".0">
            text
    </li>
</div>
```

***Ta cần phải chú ý những điều sau***
* Khi để  trống value của một attribute/prop value của nó sẽ thành true (Ví dụ: `id=""` sẽ thành `id="true"`)
* Có thể truyền các JavaScript value vào trong JSX sử dụng `{}` (Ví dụ: `test={test}` hay `data-test={tested? 'test' : 'false'}`)
* Nếu 2 attribute/prop bị khai báo trùng tên nhau thì cái khai báo sau sẽ được nhận.
* Thuộc tính `class` phải được khai báo là `className`,  thuộc tính`for` phải khai báo là `htmlFor`, thuộc tính `style` chỉ nhận vào một object chứa các thuộc tính CSS viết ở dạng camel cased.

### Khi bạn định nghĩa Event

Nếu ta sử dụng `React.createElement()`:

```
var mouseOverHandler = function mouseOverHandler() {
		console.log('you moused over');
	};
var clickhandler = function clickhandler() {
		console.log('you clicked');
	};
var reactNode = React.createElement(
		'div',
		{ onClick: clickhandler, onMouseOver: mouseOverHandler },
		'click or mouse over'
	);

ReactDOM.render(reactNode, document.getElementById('app'));
```

Nếu viết sử dụng JSX:

```
var mouseOverHandler = function mouseOverHandler() {
		console.log('you moused over');
	};
var clickHandler = function clickhandler() {
		console.log('you clicked');
	};

var reactNode = <div onClick={clickHandler} onMouseOver={mouseOverHandler} >click or mouse over</div>;

ReactDOM.render(reactNode, document.getElementById('app'));
```

Hãy để ý rằng ta sử dụng `{ }` để kết nối các hàm JS với các event.

### JSX giúp chống tấn công Injection
Ta có thể yên tâm khi embed user input bên trong JSX

```
const title = response.potentiallyMaliciousInput;
// An toàn:
const element = <h1>{title}</h1>;
```

Bởi mặc định, React DOM sẽ tự động escapes các giá trị embbed bên trong JSX trước khi render chúng. Tất cả đều được convert thành string trước khi được render. Điều này giúp phòng chống được tấn công XSS.


## Một số lưu ý khi làm việc với JSX

Vì các component viết trong JSX sẽ được compile để sử dụng `React.createElement()`, thế nên JSX luôn cần có sử xuât hiện của`React` library.

Ví dụ, cả 2 import sau đều là cần thiết trong đoạn code dưới đây:

```
import React from 'react';
import CustomButton from './CustomButton';

function WarningButton() {
  // return React.createElement(CustomButton, {color: 'red'}, null);
  return <CustomButton color="red" />;
}
```
**Các component được định nghĩa bởi user đều phải được viết hoa chữ cái đầu**
Khi một element được viết với toàn chữ cái thường thì sẽ được hiểu là các built-in component như `<div>` hay `<span>` và kết quả sẽ là các string `'div'`, `'span'` được truyền vào hàm `React.createElement()`. Còn khi ta viết hoa chữ cái đầu như `<Foo/>`  sẽ tương ứng với việc tạo ra component Foo sử dụng `React.createElement(Foo).

**Chỉ định Type cho component trong lúc RunTime**
Bạn có thể chỉnh định loại component nào được sử dụng tùy vào props truyền vào trong RunTime bằng cách sau:

```
import React from 'react';
import { PhotoStory, VideoStory } from './stories';

const components = {
  photo: PhotoStory,
  video: VideoStory
};

function Story(props) {
  // Correct! JSX type can be a capitalized variable.
  const SpecificStory = components[props.storyType];
  return <SpecificStory story={props.story} />;
}
```

Theo như đoạn code trên, component `<SpecificStory />` có thể đóng vai trò là `PhotoStory` hay `VideoStory` tùy theo story được truyền vào cho component.
