## 🧩 Redux Toolkit (RTK)

* **Maintained by the Redux team**
* **2019–present** (current official Redux standard)
* official recommended way to write Redux applications
* part of the **modern Redux ecosystem used in production applications**
* designed to standardize Redux usage and eliminate legacy boilerplate patterns

```jsx
npm install @reduxjs/toolkit react-redux
```

### Solve

---

* **All Redux problems in one layer (simplified architecture)**
  * replaces `actions + reducers + types` with **slices**
* **Less boilerplate**
  * auto-generated action creators + action types
* **Immutable updates made simple**
  * built-in **Immer** → allows “mutating” syntax safely
* **Opinionated setup**
  * `configureStore` includes good defaults automatically

---

### Issues

---

* **Still complex mental model**
  * Actions → reducers → store flow remains hard for beginners
* **Overkill for small apps**
  * Requires slices, store setup, and structure even for simple state
* **Server state needs a separate RTK layer**
  * base Redux Toolkit does not handle fetching/caching by itself; for that, the official solution is **RTK Query**
* **Extra architectural overhead**
  * Feature folders, slices, selectors add structure even when unnecessary
* **Async logic still manual**
  * `createAsyncThunk` helps, but loading/error states are still manual unless you use **RTK Query**

---

### ⚙️ Core ideas

Redux Toolkit is built around a **feature-based state design**:

| Layer      | Responsibility                              |
| ---------- | ------------------------------------------- |
| state      | Current data inside the store               |
| reducers   | Define **how state changes**                |
| actions    | Describe **what happened**                  |
| slice      | state + reducers + actions (feature module) |
| store      | global configuration of all slices          |
| app code   | dispatches actions / reads state            |

### 📁 Folder Layout

```plaintext id="rtk1"
src/
  app/
    store.js

  features/
    account/
      accountSlice.js

  components/
  main.jsx
```

#### 1. Slice (State + Logic + Actions)

📄 `features/account/accountSlice.js`

```js
import { createSlice } from "@reduxjs/toolkit";

const accountSlice = createSlice({
  name: "account",
  initialState: {
    balance: 0,
  },
  reducers: {
    deposit(state, action) {
      state.balance += action.payload;
    },
    withdraw(state, action) {
      state.balance -= action.payload;
    },
  },
});

export const { deposit, withdraw } = accountSlice.actions;
export default accountSlice.reducer;
```

**Concept:**

A **slice = self-contained feature module** that includes:

* state
* reducers
* auto-generated actions

---

#### 2. Store (Global Configuration)

📄 `app/store.js`

```js
import { configureStore } from "@reduxjs/toolkit";
import accountReducer from "../features/account/accountSlice";

export const store = configureStore({
  reducer: {
    account: accountReducer,
  },
});
```

**Concept:**

* `configureStore` replaces manual `createStore`
* automatically adds:

  * Redux DevTools support
  * thunk middleware
  * development checks and sensible defaults

---

#### 3. Usage (Dispatch / App Entry)

📄 `main.jsx` or `index.js`

```js
import { store } from "./app/store";
import { deposit, withdraw } from "./features/account/accountSlice";

store.subscribe(() => {
  console.log("State:", store.getState());
});

store.dispatch(deposit(100));
store.dispatch(withdraw(40));
```
---

### 📚 Docs

* https://redux.js.org/redux-toolkit/overview/
* https://www.freecodecamp.org/news/redux-and-redux-toolkit-for-beginners/
* https://redux.js.org/tutorials/fundamentals/part-1-overview
