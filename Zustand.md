## 🧩 Zustand

* **Maintained by the pmndrs ecosystem (independent open-source collective)**
* **2020–present** (actively maintained and widely adopted)
* minimalist alternative to Redux for global state management
* part of the **modern React ecosystem used in production apps (startups → mid-scale apps, some large-scale cases)**
* designed to remove boilerplate and simplify state management patterns

```js
npm install zustand
```
---

### Solve

---

* **Collapses Redux layers into a single store**
  * replaces `actions + reducers + dispatch` with **state + functions**
* **Minimal boilerplate**
  * no action types, no switch-cases, no store configuration
* **Direct state updates**
  * `set()` handles updates without needing reducers
* **Selective subscriptions (performance-friendly)**
  * components subscribe to **specific slices** of state
* **Simple mental model**
  * closer to React’s `useState`, but global

---

### Issues

---

* **Lack of enforced structure**
  * no architectural constraints → easy to create **uncontrolled state growth**
* **Scaling requires discipline**
  * large apps need manual patterns (feature stores, separation of concerns)
* **No built-in conventions**
  * unlike Redux Toolkit, there is no “standard way” → teams must define their own
* **State can become tightly coupled**
  * logic and state live together → harder to decouple in complex domains
* **No built-in async/data layer**
  * no caching, deduplication, or server-state handling (use React Query alongside)
* **Debugging & tooling weaker by default**
  * DevTools exist, but not as structured/powerful as Redux ecosystem out of the box

### ⚙️ Core ideas

Zustand follows a **minimalistic state container model**:

| Layer    | Responsibility                          |
| -------- | --------------------------------------- |
| store    | Holds state **and** update logic        |
| app code | Directly calls state-updating functions |

No separation into actions/reducers — everything lives in one place.

## 📁 Folder Layout

```plaintext
src/
  store/
    accountStore.js

  components/
  App.js
  index.js
```

#### 1. Store (State + Logic Together)

📄 `store/accountStore.js`

```js
import { create } from "zustand";

export const useAccountStore = create((set) => ({
  balance: 0,

  deposit: (amount) =>
    set((state) => ({
      balance: state.balance + amount,
    })),

  withdraw: (amount) =>
    set((state) => ({
      balance: state.balance - amount,
    })),
}));
```

**Concept:**

* Single source of truth (like Redux)
* Logic is colocated with state
* No reducers, no action types
* State updates are done via `set()`

---

#### 2. Application Usage (Direct Calls Instead of Dispatch)

📄 `App.js`

```js
import { useAccountStore } from "./store/accountStore";

function App() {
  const balance = useAccountStore((state) => state.balance);
  const deposit = useAccountStore((state) => state.deposit);
  const withdraw = useAccountStore((state) => state.withdraw);

  return (
    <div>
      <h1>{balance}</h1>

      <button onClick={() => deposit(100)}>Deposit</button>
      <button onClick={() => withdraw(40)}>Withdraw</button>
    </div>
  );
}
```

**Concept:**

* Component subscribes to store via hook
* Calls function (`deposit`, `withdraw`)
* Zustand updates state
* Component re-renders automatically

---

### 📚 Docs

* https://zustand.docs.pmnd.rs/learn/getting-started/introduction
* https://dev.to/jaredm/zustand-101-a-beginners-guide-to-global-state-management-in-react-lml