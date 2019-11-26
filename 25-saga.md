# Mở đầu

Như chúng ta đã biết thì khi làm việc với redux, concept của redux là chia side effect ra một nơi riêng để quản lý nhằm giúp việc scale ứng dụng được dễ dàng hơn.

Có rất nhiều thư viện để giúp chúng ta thực hiện side effect được hiệu quả hơn, trong đó mình thấy có 3 library khá phổ biến đó là redux-thunk, redux-saga, redux-observable.

Trong phạm vi bài này mình sẽ giới thiệu về 2 thư viện đó là redux-thunk và redux-saga.

# Middleware

Đầu tiên để hiểu được về các library ở trên thì chúng ta phải nắm được về khái niệm middleware trong redux trước đã.

Vậy middleware là gì ?

Trong redux thì middleware sẽ được gọi sau khi action được dispatch và trước khi reducer nhận được action. Middleware thường sẽ thực hiện các async action (tác vụ bất đồng bộ), nó sẽ là các hành động call api hoặc send tracking google analystic...

#### Implement middleware trong redux

```Javascript
// store.js

import { createStore, combineReducers, applyMiddleware } from 'redux';

const app = combineReducers(reducers);
const store = createStore(
  app,
  // applyMiddleware() tells createStore() how to handle middleware
  applyMiddleware(logger, crashReporter),
);

export default store;
```
