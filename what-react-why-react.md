# React là gì?

Một thư viện Javascript dùng để xây dựng giao diện người dùng. Ta có thể hiểu React giống như "phần V ở trong mô hình MVC". Trên thực tế, các framework như Angular, Backbone, Ember... cũng đã vốn cung cấp cho chúng ta đầy đủ về tầng View. Điều này dẫn đến câu hỏi: "Tại sao chúng ta lại cần đến React để thay thế cho phần V?"
Câu trả lời đó là: React không hẳn thực sự thay thế các views mà nó làm tăng cường view bằng cách cho phép chúng ta tạo ra các UI component có tính sử dụng lại cao (như Tab bars, comment box, pop up, modals, lists, button, table v.v...). Ý tưởng chủ đạo của React là: "Bạn có thể tạo cho chính bạn các phần tử HTML có khả năng customized một cách dễ dàng".

# Tại sao nên sử dụng React?

React cho phép các lập trình viên tạo ra các Web application lớn có thể thay đổi data mà không phải reload lại trang. Mục đích chủ yếu của React là nhanh, dễ dàng scale up và đơn giản. React chỉ động ở phần interface trong một ứng dụng.

### Simplicity - Tính Đơn giản

Với đặc tính thiết kế dựa trên component với các vòng đời (lifecycle) được định nghĩa rõ ràng và sử dụng Plain Javascript khiến cho React trở nên rất dễ dàng sử dụng. React sử dụng syntax đặc biệt là JSX cho phép chúng ta viết trộn HTML và JavsScript cùng nhau. Ta không bắt buộc phải sử dụng JSX, developer vẫn có thể viết Plain JavaScript nhưng JSX sẽ dễ dàng sử dụng hơn rất nhiều.

### Easy to learn - Dễ dàng học

Bất cứ ai với kiến thức lập trình cơ bản cũng có thể dễ dàng hiểu được React trong khi đó Angular và Ember thường được biết đến là DSL - Domain specific Language nên có rất nhiều tính đặc thù riêng khó học hơn. Với React thì bạn chỉ cần biết các kiến thức cơ bản về CSS và HTML. React vô cùng đơn giản do nó chỉ thao tác với tầng View.

### Native Approach - Dễ dàng tiếp cận với native

React có thể được dùng để tạo ra các ứng dụng điện thoại sử dụng React Native. Hỗ trợ việc tái sử dụng code và được coi là thư viện 'learn's once write anywhere' - ' học một lần viết ở khắp nơi'. Với native thì bạn có thể hiểu là React Native cung cấp cho bạn khả năng viết các native app có thể chạy được trên cả Android lẫn iOS.

### Flux and Redux - Sở hữu Flux và Redux
![redux](/images/what-react-why-react/redux.png)

Ý tưởng cơ bản là tạo ra các action được điều phối bởi một dispatcher trung tâm để update stores (nơi tập trung dữ liệu trên view). Và sau đó các view sẽ được update theo sự thay đổi của store đó. Tất cả các data được display thông qua các component đều được lưu trong store và sẽ không bị duplicate giống như models trong cấu trúc MVC, điều này sẽ giúp bạn dễ dàng đồng bộ dữ liệu với nhau trên toàn bộ ứng dụng.

### Testability - Dễ dàng Test

Ta có thể test một ứng dụng ReactJS cực kì dễ dàng. Các view trong React có thể được coi như là các function xử lý state, thế nên ta có thể thao tác với chúng bằng cách truyền vào view của ReactJS các state và để ý output trên view hay trigger các actions, events v.v...

### Reusable Components - Khả năng tái sử dụng code

React cung cấp cho ta cấu trúc dựa trên component. Các component ở đây đóng vai trò như những miếng lego. Ta có thể bắt đầu với các component nhỏ như button, checkbox, dropdown v.v... và tiến đến tạo các wrapper component để bọc, chứa các component nhỏ hơn đó. Và rồi lại tiếp tục viết các component bọc to hơn cho đến khi tiến tới component root.
Mỗi component sẽ quyết định ứng dụng  được render như thế nào và mỗi chúng đều có một logic riêng của mình. Phương pháp này đã cho thấy tính hiệu quả tuyệt vời. Ta có thể sử dụng lại các component ở bất kì đâu cần thiết. Và kết quả là ta có một ứng dụng có sự đồng nhất, thống nhất trong giao diện, thao tác. Việc sử dụng lại code khiến cho việc maitain vô cùng dễ dàng và việc phát triển thêm cho ứng dụng lại càng dễ hơn.

### Fast render with Virtual DOM - Render nhanh hơn nhờ Vitural DOM

Mặc dù ngày nay các JavaScript engine vốn đã khá đủ nhanh để xử lý với các hệ thống, ứng dụng phức tạp nhưng thao tác với DOM vẫn chưa thể nhanh như mong muốn. Việc cập nhật DOM vẫn thường gặp hiện tượng bottleneck (nghẽn cổ chai) khi nói đến performance của web. React sẽ giải quyết vấn đề này bằng phương pháp **Virtual DOM** - DOM được lưu trong memory. Bất cứ sự thay đổi nào trên View đều được thực hiện trên virtual DOM trước rồi bằng thuật toán so sánh sự khác nhau giữa state cũ và mới của virtual DOM, React sẽ tính toán ra được cách tốt nhất (Lượng update cần thiết ít nhất) để thực hiện các thay đổi. Cuối cùng các update đó được áp vào DOM để đảm bảo thời gian ghi/đọc ít nhất.

### Great Developer Tools - Developer Tools tiện nhất
Khi code React có hai tool sẽ thường xuyên được sử dụng mà ta cần để ý đến:  **React Developer Tools** và **Redux Developer Tools**. Cả hai tool này đều có thể được cài đặt thông qua Chrome extensions.
**React Developer Tools** là công cụ tuyệt vời để inspect React component theo đúng cây nó được sắp xếp và dễ dàng để quan sát được `props` và `states` hiện tại của component đó. Component lựa chọn sẽ được highlight phía bên trái, vị trí của nó trong cây và props của nó được hiển thị bên cột phải.

![react-dev-tools](/images/what-react-why-react/react-dev-tools.png)

Nếu bạn đang sử dụng library Redux thì chắc chắn nên xem, tìm kếm **Redux Developer Tools**. Bạn có thể quan sát được các action được dispatch, các state lưu trong store và theo dõi các thay đổi trong store. Đồng thời ta có thể dispatch một action hoặc thay đổi store và theo dõi thay đổi được áp dụng lên page ngay lập tức

![redux-dev-tools](/images/what-react-why-react/redux-dev-tools.png)

## References

https://stories.jotform.com/7-reasons-why-you-should-use-react-ad420c634247

https://medium.freecodecamp.org/yes-react-is-taking-over-front-end-development-the-question-is-why-40837af8ab76

https://www.c-sharpcorner.com/article/what-and-why-reactjs/
