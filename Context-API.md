## 🧩 Context API

### What is Context API?

Context API lets a parent component provide data to any component below it in the component tree without passing it through every intermediate component via props.

### Why?

Context API helps avoid **prop drilling**, where props must be passed through components that don't actually use them.

### Concepts

Context API uses three main APIs:

- **`createContext()`** – creates a new context.

- **`<Context value={...}>`** – provides data to components below it.

- **`useContext()`** – gets data from a context.

### Before (Prop Drilling)

Imagine an online store where each product card has its own **Like** button. We also want to display the total number of liked products in the `Header`.

Now, a new intermediate component (`ProductList`) is introduced between `App` and `ProductCard`. It doesn't need the liked data itself—it only renders the product cards.

**Before using Context API**, the liked state still lives in `App`, but it must be passed through `ProductList` to reach `ProductCard`.

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

📄 `src/components/ProductList.jsx`

```jsx
import ProductCard from "./ProductCard";

// This component doesn't need the liked data,
// but it must receive it just to pass it down.
function ProductList({ products, likedItems, toggleLike }) {
  return (
    <div className="list">
      {products.map((name) => (
        <ProductCard
          key={name}
          name={name}
          liked={!!likedItems[name]}
          onToggleLike={() => toggleLike(name)}
        />
      ))}
    </div>
  );
}

export default ProductList;
```

📄 `src/App.jsx`

```jsx
import { useState } from "react";

import Header from "./components/Header";
import ProductList from "./components/ProductList";

function App() {
  const [likedItems, setLikedItems] = useState({});

  const toggleLike = (name) =>
    setLikedItems((prev) => ({
      ...prev,
      [name]: !prev[name],
    }));

  const likedCount = Object.values(likedItems).filter(Boolean).length;

  return (
    <div>
      <Header likedCount={likedCount} />

      <ProductList
        products={["Sneakers", "Jacket", "Backpack"]}
        likedItems={likedItems}
        toggleLike={toggleLike}
      />
    </div>
  );
}

export default App;
```

#### What's Wrong?

`ProductList` receives `likedItems` and `toggleLike` even though it never uses them.

Its only purpose is to pass those props to `ProductCard`. This is called **prop drilling**.

Imagine having several intermediate components (`Layout → Page → Section → List → Card`). Every one of them would have to receive and forward the same props, making the code harder to maintain.


### After (Context API)

The shared data is now provided through Context, so intermediate components no longer need to forward props.

📄 `src/context/LikesContext.js`

```jsx
import { createContext } from "react";

export const LikesContext = createContext({
  likedItems: {},
  toggleLike: () => {},
});
```

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
import { useContext } from "react";

import { LikesContext } from "../context/LikesContext";

function ProductCard({ name }) {
  const { likedItems, toggleLike } = useContext(LikesContext);

  const liked = !!likedItems[name];

  return (
    <div className="card">
      <h3>{name}</h3>

      <button onClick={() => toggleLike(name)}>
        {liked ? "❤️ Liked" : "🩶 Like"}
      </button>
    </div>
  );
}

export default ProductCard;
```

📄 `src/components/ProductList.jsx`

```jsx
import ProductCard from "./ProductCard";

// This component is now completely unaware of the liked state.
function ProductList({ products }) {
  return (
    <div className="list">
      {products.map((name) => (
        <ProductCard key={name} name={name} />
      ))}
    </div>
  );
}

export default ProductList;
```

📄 `src/App.jsx`

```jsx
import { useState } from "react";

import Header from "./components/Header";
import ProductList from "./components/ProductList";

import { LikesContext } from "./context/LikesContext";

function App() {
  const [likedItems, setLikedItems] = useState({});

  const toggleLike = (name) =>
    setLikedItems((prev) => ({
      ...prev,
      [name]: !prev[name],
    }));

  const likedCount = Object.values(likedItems).filter(Boolean).length;

  return (
    <LikesContext value={{ likedItems, toggleLike }}>
      <Header likedCount={likedCount} />

      <ProductList products={["Sneakers", "Jacket", "Backpack"]} />
    </LikesContext>
  );
}

export default App;
```

#### What Changed?

- A new `LikesContext.js` file was created using `createContext()`.

- `App` now provides the shared data through `<LikesContext value={...}>` instead of passing props down the tree.

- `ProductList` no longer receives `likedItems` or `toggleLike` and becomes a clean intermediate component.

- `ProductCard` consumes the context via `useContext()` instead of receiving props.

### 📚 Docs

- https://react.dev/learn/passing-data-deeply-with-context