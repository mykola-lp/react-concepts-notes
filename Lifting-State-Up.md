## 🧩 Lifting State Up

### What is it?

Sometimes, you want the state of two or more components to always stay in sync. To achieve this, move the shared state from individual components to their closest common parent and pass it down via props.

This pattern is called **lifting state up** and is one of the most common patterns in React.

### Before (Issue)

Imagine an online store where each product card has its own **Like** button. We also want to display the total number of liked products in the `Header`.

**Before lifting state up**, each `ProductCard` manages its own state independently.

📄 `src/components/Header.jsx`

```jsx
function Header() {
  return (
    <header>
      <h1>Store</h1>

      <p>❤️ Liked products: ?</p>
    </header>
  );
}

export default Header;
```

📄 `src/components/ProductCard.jsx`

```jsx
import { useState } from "react";

function ProductCard({ name }) {
  const [liked, setLiked] = useState(false);

  return (
    <div className="card">
      <h3>{name}</h3>

      <button onClick={() => setLiked(!liked)}>
        {liked ? "❤️ Liked" : "🩶 Like"}
      </button>
    </div>
  );
}

export default ProductCard;
```

📄 `src/App.jsx`


```jsx
import Header from "./components/Header";
import ProductCard from "./components/ProductCard";

function App() {
  return (
    <div>
      <Header /> {/* No access to the liked state */}

      <ProductCard name="Sneakers" />
      <ProductCard name="Jacket" />
      <ProductCard name="Backpack" />
    </div>
  );
}

export default App;
```

#### What's Wrong?

Each `ProductCard` stores its own `liked` state.

The `Header` has **no access** to those values because they are isolated inside each card.

As a result, it is impossible to calculate the total number of liked products without changing the component architecture.

### After (Lifting State Up)

The shared state has been moved to the closest common parent (`App`).

📄 `src/components/Header.jsx`

```jsx
function Header({ likedCount }) {
  return (
    <header>
      <h1>Store</h1>

      <p>❤️ Liked products: {likedCount}</p>
    </header>
  );
}

export default Header;
```

📄 `src/components/ProductCard.jsx`

```jsx
function ProductCard({ name, liked, onToggleLike }) {
  return (
    <div className="card">
      <h3>{name}</h3>

      <button onClick={onToggleLike}>
        {liked ? "❤️ Liked" : "🩶 Like"}
      </button>
    </div>
  );
}

export default ProductCard;
```

📄 `src/App.jsx`

```jsx
import { useState } from "react";

import Header from "./components/Header";
import ProductCard from "./components/ProductCard";

function App() {
  const [likedItems, setLikedItems] = useState({});

  const toggleLike = (name) => {
    setLikedItems((prev) => ({
      ...prev,
      [name]: !prev[name],
    }));
  };

  const likedCount = Object.values(likedItems).filter(Boolean).length;

  return (
    <div>
      <Header likedCount={likedCount} />

      <ProductCard
        name="Sneakers"
        liked={!!likedItems["Sneakers"]}
        onToggleLike={() => toggleLike("Sneakers")}
      />

      <ProductCard
        name="Jacket"
        liked={!!likedItems["Jacket"]}
        onToggleLike={() => toggleLike("Jacket")}
      />

      <ProductCard
        name="Backpack"
        liked={!!likedItems["Backpack"]}
        onToggleLike={() => toggleLike("Backpack")}
      />
    </div>
  );
}

export default App;
```

#### What Changed?

- `useState` now exists only in `App` (the state was **lifted up**).

- `ProductCard` became a **controlled component** — it no longer owns its own state. Instead, it receives `liked` and `onToggleLike` via props.

- `App` became the **single source of truth** for the liked state.

- `Header` can now display the correct `likedCount` because it receives data from the same parent component.

### 📚 Docs

- https://react.dev/learn/sharing-state-between-components
- https://react.dev/learn/passing-data-deeply-with-context