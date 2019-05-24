# Lifting state up

**Lifting state up** (dịch nôm na là ***Nâng trạng thái lên***) là cách mà React dùng để chia sẻ các state nhằm giúp các component đều có khả năng sử dụng tùy vào mục đích của các component. Những state cần được sử dụng chung sẽ được đặt ở vị trí component cha chung gần nhất của các phần tử cần sử dụng. Để hiểu rõ hơn về nó, ta sẽ đến với một bài toàn đơn giản dưới đây.

***Bài toán:*** Viết một giao diện để tính điểm trung bình 3 môn học Toán, Lí, Hóa của sinh viên. Với mỗi điểm môn học sẽ được nhập vào một input tương ứng. Điểm trung bình sẽ được hiển thị khi các input được nhận vào.

Trước tiên ta cần xây dựng component  `ScoreInput` để nhập điểm cho từng môn:



```jsx
class ScoreInput extends Component {
  constructor(props) {
    super(props);
    this.state = {
      score: ""
    };
  }

  handleChange = e => {
    this.setState({ score: e.target.value });
  };

  render() {
    const { subject } = this.props;
    return (
      <div className="scores-field">
        <div className="subject">{subject}</div>
        <input value={this.state.score} onChange={this.handleChange} />
      </div>
    );
  }
}
```

Ở đây ta có thể thấy. Mỗi đối tượng `ScoreInput` sẽ nhận đầu vào thuộc tính `subject`, tương ứng với tên môn học. Mỗi đối tượng còn chứ state tương ứng với số điểm mỗi môn học. 

Ta tiếp tục xây dựng nên một component `AverageScore` để biểu diễn điểm trung bình của 3 môn:

```jsx
class AverageScore extends Component {
  render() {
    return (
      <div>
        <p>Average score:</p>
      </div>
    );
  }
}
```



Khi đó ta có:

```jsx
class App extends Component {
  render() {
    return (
      <div className="app">
        <h1>Please fill your scores!</h1>
        <ScoreInput subject="Math" />
        <ScoreInput subject="Physics" />
        <ScoreInput subject="Chemistry" />
        <AverageScore />
      </div>
    );
  }
}

ReactDOM.render(<App />, document.getElementById("root"));
```

Như vậy ta đã hoàn thành được những bước đầu tiên khi tạo được nên giao diện tổng quan. Tuy nhiên, ở đây lại xuất hiện một vấn đề, đó là: Khi điểm số của các môn thay đổi, chúng chỉ được caaph nhật trong state của component `ScoreInput` mà mục đích của chúng ta đó là phải hiển thị được số điểm trung bình của 3 môn ở component `AverageScore`. Vậy làm thế nào để component `ScoreInput` có thể lấy được giá trị của điểm số các môn học?

Rất đơn giản với cách làm như sau: 

- Ở component `App`, ta đặt các state tương ứng với điểm số các môn học, và truyền chúng tới các component `ScoreInput` dưới dạng props, đồng thời cũng truyền tới `AverageScore` các điểm số của các môn:

```jsx
class App extends Component {
  constructor(props) {
    super(props);
    this.state = {
      math: "",
      physics: "",
      chemistry: ""
    };
  }
  render() {
    const { math, physics, chemistry } = this.state;
    return (
      <div className="app">
        <h1>Please fill your scores!</h1>
        <ScoreInput subject="Math" score={math} />
        <ScoreInput subject="Physics" score={physics} />
        <ScoreInput subject="Chemistry" score={chemistry} />
        <AverageScore scores={this.state} />
      </div>
    );
  }
}
```

```jsx
class ScoreInput extends Component {
  render() {
    const { subject, score } = this.props;
    return (
      <div className="scores-field">
        <div className="subject">{subject}</div>
        <input value={score} onChange={...} />
      </div>
    );
  }
}
```

```jsx
class AverageScore extends Component {
  render() {
    const { scores } = this.props;
    return (
      <div>
        <p>Average score: {calculateAverageScore(scores)}</p>
      </div>
    );
  }
}

function calculateAverageScore(scores) {
  let averageScore = 0;
  Object.keys(scores).forEach(key => {
    averageScore += parseFloat(scores[key]);
  });
  return averageScore;
}
```

Tại sao chúng ta lại phải đẩy các state đang từ các component con `ScoreInput` lên cho component cha `App`? Câu trả lời vô cùng đơn giản, đó là: Khi chúng ta đặt các state ở component `App` - là nơi cha gần nhất của các component con `ScoreInput` và `AverageScore`, các component con hoàn toàn có thể truy cập tới giá trị của các state của cha bằng cách truyền chúng qua các props của componenmt con. Hành động này chính là ***Lifting state up*** mà chúng ta đang cập nhật tới.

- Sau khi chuyển các state lên component cha, chúng ta  cần định nghĩa lại các thao tác khi thay đổi input trong component con. Do các state được đặt trong component cha nên các thao tác thay đổi cũng cần được định nghĩa ở component cha và truyền tới các component con như một props:

```jsx
class App extends Component {
  ...
  handleChange = e => {
    const { name, value } = e.target;
    this.setState({ [name]: value });
  };

  render() {
    ...
        <ScoreInput
          subject="math"
          score={math}
          handleChange={this.handleChange}
        />
        <ScoreInput
          subject="physics"
          score={physics}
          handleChange={this.handleChange}
        />
        <ScoreInput
          subject="chemistry"
          score={chemistry}
          handleChange={this.handleChange}
        />
    ...
  }
}
```

```jsx
class ScoreInput extends Component {
  render() {
    const { subject, score, handleChange } = this.props;
    return (
      <div className="scores-field">
        <div className="subject">{subject}</div>
        <input
          name={subject}
          type="number"
          value={score}
          onChange={e => handleChange(e)}
        />
      </div>
    );
  }
}
```

Và kết quả là chúng ta đã có thể hoàn thành bài toán:

![Imgur](https://i.imgur.com/qjrVN8l.png)

Ta có thể thấy `App` là component mà bao chứa cả 2 component muốn dùng chung state: `ScoreInput` & `AverageScore`. Vì vậy, đây cũng sẽ chính là nơi đặt state chung của 2 component này. Tương tự với các bài toán khác, với các component muốn sử dụng chung state, ta chỉ cần đặt các state chung ở các component cha của các component đó, mà nên là các component cha gần nhất của chúng.

Tuy nhiên, một nhược điểm dễ dàng có thể nhận thấy của phương pháp này, đó là: Nếu component cha chung nhất cảu các component ở mức quá xa so với các component con, chúng ta sẽ phải truyền state và các handle event có liên quan qua các lớp component ở giữa chúng, gây nên một sự thừa thãi cũng như khó kiểm soát. Để giải quyết vấn đề này, chúng ta có thể sử dụng các phương pháp khác như context hay redux,... Tùy theo từng trường hợp riêng biệt, ta sẽ áp dụng các phương pháp một cách hợp lí.

Nguồn tham khảo: https://reactjs.org/docs/lifting-state-up.html

