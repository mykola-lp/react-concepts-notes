## 🧩 Redux

* **Created by** Dan Abramov
* **2015–present** (core library, foundational state management tool)
* originally popularized as a practical implementation of **Flux architecture ideas**
* widely adopted in early React ecosystem and large-scale applications
* designed to provide predictable state management via:
  * single store
  * pure reducers
  * explicit state transitions

Can be implemented without a library or via Redux:

```js
npm install redux react-redux redux-thunk redux-devtools-extension
```

### Solve

---

* **Centralized state:** no prop drilling, single store
* **Predictable updates:** actions → pure reducers, immutable flow
* **Debugging:** action history, replay, time-travel debugging
* **No built-in** async handling (requires middleware like `redux-thunk`)

---

### Issues
---

* heavy boilerplate
* complex setup
* manual actions/types/reducers structure

---

### ⚙️ Core ideas

Single source of truth + predictable state transitions

| Layer      | Responsibility                    |
| ---------- | --------------------------------- |
| actions    | Describe **what happened**        |
| reducers   | Define **how state changes**      |
| store      | Holds the **global state tree**   |
| state      | Current data inside the store     |
| dispatch   | Mechanism for sending actions     |
| subscriber | Listening to state changes        |
| app code   | Triggers updates via `dispatch()` |

### 📁 Folder Layout

```plaintext
src/
  redux/
    store.js
    reducers/
      accountReducer.js
      rootReducer.js
    actions/
      accountActions.js

  components/
  App.js
  index.js
```

#### 1. Reducer (State Logic)

📄 `redux/reducers/accountReducer.js`

```js
const initialState = {
  balance: 0,
};

export function accountReducer(state = initialState, action) {
  switch (action.type) {
    case "account/deposit":
      return {
        ...state,
        balance: state.balance + action.payload,
      };

    case "account/withdraw":
      return {
        ...state,
        balance: state.balance - action.payload,
      };

    default:
      return state;
  }
}
```
**Concept:**

* Reducers must be **pure functions**
* No side effects (no API calls, no mutations)
* Always return a **new state object**

---

#### 2. Root Reducer (Combining State Slices)

📄 `redux/reducers/rootReducer.js`

```js
import { combineReducers } from "redux";
import { accountReducer } from "./accountReducer";

export const rootReducer = combineReducers({
  account: accountReducer,
});
```

**Concept:**

* Splits global state into **independent slices**
* Each slice is managed by its own reducer

#### 3. Store (Single Source of Truth)

📄 `redux/store.js`

```js
import { createStore } from "redux";
import { rootReducer } from "./reducers/rootReducer";

export const store = createStore(rootReducer);
```

**Concept:**

* Holds the entire application state
* Provides:
  * `getState()`
  * `dispatch(action)`
  * `subscribe(listener)`

---

#### 4. Actions (Action Creators)

📄 `redux/actions/accountActions.js`

```js
export function deposit(amount) {
  return {
    type: "account/deposit",
    payload: amount,
  };
}

export function withdraw(amount) {
  return {
    type: "account/withdraw",
    payload: amount,
  };
}
```

**Concept:**

* Actions are **plain JavaScript objects**
* `type` → what happened
* `payload` → data required for the update

#### 5. Application Usage

📄 `index.js`

```js
import { store } from "./redux/store";
import { deposit, withdraw } from "./redux/actions/accountActions";

store.subscribe(() => {
  console.log("State:", store.getState());
});

store.dispatch(deposit(100));
store.dispatch(withdraw(40));
```

**Concept:**

1. `dispatch(action)`
2. Reducer processes action
3. Store updates state
4. Subscribers are notified

---

### 📚 Docs

* https://www.freecodecamp.org/news/redux-and-redux-toolkit-for-beginners/
* https://redux.js.org/tutorials/fundamentals/part-1-overview