## 🧩 Flux

* **Created by Facebook (now Meta)**
* **2014–present as an architectural pattern, but the official Flux project is archived**
* original React-era architecture for **unidirectional data flow**
* designed to solve problems of **two-way bindings / cascading updates** in large UIs
* **not the current standard recommendation** for new React apps, but historically important because it shaped Redux and similar tools

```js
npm install flux
```

### Solve

---

* **Unidirectional data flow**
  * user action → dispatcher → stores → view update
* **Clear separation of responsibilities**
  * actions describe intent, stores own state and business logic
* **Predictable update cycle**
  * all updates go through a central dispatcher
* **Better scaling than classic MVC in complex UIs**
  * avoids many implicit cross-model updates

---

### Issues

---

* **A pattern, not a complete standard toolchain**
  * many implementation details are left to the team
* **More boilerplate than modern state tools**
  * dispatcher, constants, actions, stores, subscriptions
* **Can become verbose in real apps**
  * especially with multiple stores and event handling
* **Less ergonomic than modern Redux Toolkit / Zustand approaches**
  * more manual wiring, fewer defaults
* **Official Flux package is archived**
  * useful mainly for learning, legacy systems, and architecture history

---

### ⚙️ Core ideas

Flux is built around a **one-way update cycle**:

| Layer          | Responsibility                                |
| -------------- | --------------------------------------------- |
| action         | Describes **what happened**                   |
| action creator | Creates and dispatches actions                |
| dispatcher     | Sends actions to all registered stores        |
| store          | Holds state + business logic for a domain     |
| view           | Reads store state and triggers user actions   |

Unlike Redux, Flux usually has **multiple stores** and a **central dispatcher**.

### 📁 Folder Layout

```plaintext
src/
  flux/
    dispatcher.js
    actions/
      accountActions.js
    stores/
      AccountStore.js

  components/
    AccountView.jsx
  main.jsx
```

#### 1. Dispatcher (Central Hub)

📄 `flux/dispatcher.js`

```js
import { Dispatcher } from "flux";

export const AppDispatcher = new Dispatcher();
```

**Concept:**

* Dispatcher is the **central event hub**
* Stores register callbacks in it
* Every action is sent through the dispatcher first

---

#### 2. Actions (Intent + Dispatch)

📄 `flux/actions/accountActions.js`

```js
import { AppDispatcher } from "../dispatcher";

export const AccountActionTypes = {
  DEPOSIT: "ACCOUNT_DEPOSIT",
  WITHDRAW: "ACCOUNT_WITHDRAW",
};

export const AccountActions = {
  deposit(amount) {
    AppDispatcher.dispatch({
      type: AccountActionTypes.DEPOSIT,
      payload: amount,
    });
  },

  withdraw(amount) {
    AppDispatcher.dispatch({
      type: AccountActionTypes.WITHDRAW,
      payload: amount,
    });
  },
};
```

**Concept:**

* Actions are plain objects with `type`
* Action creators wrap dispatch logic
* Views usually call action creators, not stores directly

---

#### 3. Store (State + Update Logic)

📄 `flux/stores/AccountStore.js`

```js
import { EventEmitter } from "events";
import { AppDispatcher } from "../dispatcher";
import { AccountActionTypes } from "../actions/accountActions";

class AccountStore extends EventEmitter {
  balance = 0;

  getState() {
    return {
      balance: this.balance,
    };
  }

  emitChange() {
    this.emit("change");
  }

  addChangeListener(listener) {
    this.on("change", listener);
  }

  removeChangeListener(listener) {
    this.removeListener("change", listener);
  }
}

export const accountStore = new AccountStore();

AppDispatcher.register((action) => {
  switch (action.type) {
    case AccountActionTypes.DEPOSIT:
      accountStore.balance += action.payload;
      accountStore.emitChange();
      break;

    case AccountActionTypes.WITHDRAW:
      accountStore.balance -= action.payload;
      accountStore.emitChange();
      break;

    default:
      break;
  }
});
```

**Concept:**

* Store owns the data and update rules
* It reacts to dispatched actions
* After update, it emits a change event so the UI can re-read state

---

#### 4. View (Subscribe + Render)

📄 `components/AccountView.jsx`

```jsx
import { useEffect, useState } from "react";
import { accountStore } from "../flux/stores/AccountStore";
import { AccountActions } from "../flux/actions/accountActions";

export function AccountView() {
  const [state, setState] = useState(accountStore.getState());

  useEffect(() => {
    const handleChange = () => {
      setState(accountStore.getState());
    };

    accountStore.addChangeListener(handleChange);

    return () => {
      accountStore.removeChangeListener(handleChange);
    };
  }, []);

  return (
    <div>
      <h1>{state.balance}</h1>

      <button onClick={() => AccountActions.deposit(100)}>Deposit</button>
      <button onClick={() => AccountActions.withdraw(40)}>Withdraw</button>
    </div>
  );
}
```

**Flow:**

1. User clicks button
2. View calls an action creator
3. Action is dispatched through dispatcher
4. Store updates its state
5. Store emits change event
6. View reads fresh state and re-renders

---

### 📚 Docs

* https://facebookarchive.github.io/flux/docs/in-depth-overview
* https://facebookarchive.github.io/flux/docs/overview/
