# Mở đầu

Javascript là một ngôn ngữ vô cùng phổ biến hiện nay, nên nó đang được cộng đồng quan tâm rất nhiều, và tạo ra vô số tool hay ho, tiện lợi.

Convention, Code Formatting? Khỏi cần bận tâm, ta chỉ cần code thôi, mấy chuyện nhỏ kia cứ để tool lo.

Dưới đây sẽ giới thiệu về 2 công cụ:

* Code Convention: ESLint
* Code Formatting: Prettier

Code editor thì nên dùng VSCode vì có extension cho cả 2 thằng trên.

# ESLint

Nếu đã code Ruby, mọi người chắc hẳn đều biết tới Rubocop phải không nào?

Đối với JS thì trước kia ta có jshint, giờ ta có ESLint xịn xò hơn.

Đa số dự án React đều sử dụng nó để thống nhất 1 chuẩn chung khi code.

Dựa trên config của ta, nó sẽ báo lỗi hoặc cảnh báo (warn) ta cần phải sửa code. Ví dụ như phải sử dụng  destructuring assignment của ES6, hay khai báo hàm rồi mới được dùng (mặc dù JS có hoisting).

```js
const name = this.props.name 	// not good
const { name } = this.props 	// good

// not good
doSomething()
function doSomething() {}
// good
function doSomething() {}
doSometing()
```

Convention của ta có chặt chẽ hay lỏng lẻo đều phụ thuộc vào config, nếu mà disable đi hết thì có cũng như không =)) ESLint sử dụng file `.eslintrc` (extension có thể là `.yml`, `.json`, hay không có extension cũng được) để chứa config.

Chi tiết config có thể xem tại [đây](<https://eslint.org/docs/rules/>).

Lời khuyên là cứ cài eslint rồi chạy `eslint --init`, sau đó sử dụng `airbnb` config. Khi code, cảm thấy convention nào hơi bó buộc thì có thể disable nó đi.

Ngoài ra nếu không muốn ESLint kiểm tra 1 hay nhiều file nào đó, hãy sử dụng `.eslintignore`

Để tiến hành lint project ta sử dụng:

```shell
eslint <path>
```

Với VSCode, bạn có thể cài thêm extension ESLint để nó tự động kiểm tra ngay khi code.

Còn với những editor khác, có thể sẽ phải viết thêm hook để sau khi save file sẽ tự động chạy `eslint`

Túm váy:

```shell
yarn install eslint --dev
npx eslint --init
npx eslint .
```

# Prettier

Prettier là một công cụ format code, không cần lo về indent, trailing comma hay code 1 line quá dài nữa. Prettier sẽ lo hết cho bạn. Tuy nhiên prettier không có quá nhiều config có thể thay đổi, một khi đã dùng, ta phải làm quen với style code của nó.

Hãy cài nó

```shell
yarn install prettier --dev
```

Sau đó hãy tạo file config cho prettier - `.prettierrc`. Giống như ESLint, Prettier chấp nhận cả `.json` và `.yml`

```yml
# .prettierrc.yml
trailingComma: es5
tabWidth: 2
semi: false
singleQuote: true
```

Để ignore file, ta dùng `.prettierignore`

Sau đó, ta bật editor lên và code thôi, VSCode thì save cái là auto đẹp...

Hoặc chạy thủ công bằng CLI

```shell
prettier --write ./**/*.{js,jsx,scss,json,yml}
```

Tuy nhiên khi sử dụng chung với ESLint Airbnb config thì sẽ có chút conflict, đúng là code đã đẹp rồi, nhưng mà sao đỏ lòm thế này. Đó là bởi vì airbnb config cũng có những rule về code "đẹp". Vì vậy trước hết phải disable những rule của airbnb đi, và thêm vào những rule phù hợp với style của prettier. Prettier cũng tính cả rồi, hãy cài thêm

```shell
yarn add --dev eslint-config-prettier eslint-plugin-prettier
```

Sửa file config cho ESLint.

```json
// .eslintrc.json
{
  "extends": ["plugin:prettier/recommended"]
}
```

Có thể xem thêm tại [đây](<https://prettier.io/docs/en/integrating-with-linters.html>).

Túm váy

```shell
yarn install prettier eslint-config-prettier eslint-plugin-prettier --dev
touch .prettierrc
npx prettier --write ./**/*.{js,jsx,scss,json,yml}
```

