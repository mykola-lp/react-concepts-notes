### Overview

---

`useState` is a React Hook for storing **local state inside a component** — keeps the state separate from the component function.

---

**Its role:**

- store data that can change over time  
- trigger component re-renders when data changes  

**Problem it solves:**

- UI must be dynamic, but functional components are stateless by default. `useState` gives them memory.

---

### Concept

- **state** — a value that persists between renders
- **setter function** — a function used to update the state
- **initial state** — the initial value
- **re-render** — re-execution of the component after state changes

No installation required.

```jsx
import { useState } from "react";
```

#### Example:

```jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  // count — current value
  // setter (setCount) - function used to update state
  // each click → triggers a new render (re-render)

  return (
    <button onClick={() => setCount(count + 1)}>
      {count}
    </button>
  );
}
```

### Docs:

- https://react.dev/reference/react/useState