# lab_recap-building-dynamic-react-app

![logo_ironhack_blue 7](https://user-images.githubusercontent.com/23629340/40541063-a07a0a8a-601a-11e8-91b5-2f13e4e6b441.png)

# LAB | Building a Dynamic React App

## Learning Goals

After this exercise, you will be able to:

- Set up a new React project from scratch using Vite.
- Fetch data from a public API using the `fetch` API.
- Manage application state, including lists of data and loading status, with the `useState` hook.
- Handle side effects like data fetching with the `useEffect` hook.
- Create and render components dynamically from a list of data.
- Implement a loading state to improve user experience.

<br>

## Requirements

- You will be creating a new React project from scratch for this lab.
- No repository is provided. You will be creating your own.
- You need [Node.js](https://nodejs.org/en/) installed on your machine.

<br>

## Submission

- Upon completion, create a new repository on GitHub.
- Add your local project to the remote repository.
- Run the following commands:

```shell
$ git add .
$ git commit -m "Solved lab"
$ git push origin main
```

- Share the repository link with your TAs.

<br>

## Test Your Code

For this lab, you will test your code by running the application and observing its behavior in the browser. There are no pre-written unit tests.

To run your React application, run the following command from the root of the project:

```shell
$ npm run dev
```

To see the outputs of `console.log` in your JavaScript code, open the [Console in the Developer Tools](https://developer.chrome.com/docs/devtools/open/#console) of your browser.

<br>

## Instructions

The goal of this exercise is to build a small React application that fetches a list of products from an API and displays them. You will apply the concepts of state management, side effects, and component rendering that you've just reviewed.

This exercise is split into multiple iterations:

- **Setup**: You will create a new React project and clean it up for a fresh start.
- **Fetching Data**: You will fetch data from an API and store it in the component's state.
- **Component Rendering**: You will create a dedicated component to display each product.
- **Loading State**: You will add a loading indicator to provide feedback to the user.

<br>

### Iteration 0 - Project Setup

First, we need a new React project. We'll use Vite for a fast and modern setup.

1.  **Create the project.**
    In your terminal, navigate to where you want to create your project and run the following command. Name your project `lab-react-product-list`.

    ```shell
    # Creates a new Vite project with a React template
    $ npm create vite@latest lab-react-product-list -- --template react
    ```

2.  **Navigate into the project and install dependencies.**

    ```shell
    $ cd lab-react-product-list
    $ npm install
    ```

3.  **Clean up the project.**
    Open the project in your code editor. We want to start fresh.

    - Delete everything inside `src/App.css`.
    - Replace the content of `src/App.jsx` with a basic functional component:

    ```jsx
    // src/App.jsx
    import './App.css';

    function App() {
      return (
        <div className="App">
          <h1>My Product List</h1>
        </div>
      );
    }

    export default App;
    ```

4.  **Run the app.**
    Start the development server to make sure everything is working.

    ```shell
    $ npm run dev
    ```

    You should see your "My Product List" heading in the browser.

<br>

### Iteration 1 - Fetch and Display Products

Now, let's fetch some data from the [DummyJSON API](https://dummyjson.com/) and display it.

1.  **Create state for products.**
    In `src/App.jsx`, import the `useState` and `useEffect` hooks from React. Then, create a piece of state to hold the array of products. The initial state should be an empty array.

2.  **Fetch data with `useEffect`.**
    Use a `useEffect` hook to fetch the products when the component first mounts.

    - The effect should run only once. How do you ensure that?
    - Use `async/await` with the `fetch` API to get data from `https://dummyjson.com/products`.
    - Once you get the data, update the `products` state with the array of products from the response (it will be in `jsonResponse.products`).

3.  **Display the product titles.**
    For now, just render the titles of the products in an unordered list (`<ul>`). Use the `.map()` method on your `products` state array to generate the list items (`<li>`).

> [!IMPORTANT]
> Don't forget to add a unique `key` prop to each `<li>` element when mapping over the array. React uses this for performance. The product's `id` is a perfect candidate.

<details>
  <summary>Click for Solution</summary>

```jsx
// src/App.jsx
import { useState, useEffect } from 'react';
import './App.css';

function App() {
  const [products, setProducts] = useState([]);

  useEffect(() => {
    const fetchProducts = async () => {
      try {
        const response = await fetch('https://dummyjson.com/products');
        const data = await response.json();
        setProducts(data.products);
      } catch (error) {
        console.error('Failed to fetch products:', error);
      }
    };

    fetchProducts();
  }, []); // Empty dependency array ensures this runs only once on mount

  return (
    <div className="App">
      <h1>My Product List</h1>
      <ul>
        {products.map(product => (
          <li key={product.id}>{product.title}</li>
        ))}
      </ul>
    </div>
  );
}

export default App;
```

</details>

<br>

### Iteration 2 - Create a `ProductCard` Component

Displaying just the title is a bit boring. Let's create a dedicated component to render each product in a more visually appealing card format.

1.  **Create the `ProductCard` component.**

    - Create a new folder `src/components`.
    - Inside it, create a new file `ProductCard.jsx`.
    - This component should accept a single product object as a prop.
    - The component should render the product's image (`thumbnail`), `title`, and `price`.

2.  **Style the `ProductCard`.**

    - Create a new CSS file `src/components/ProductCard.css` and add some basic styles to make it look like a card (e.g., border, padding, box-shadow).
    - Import this CSS file into your `ProductCard.jsx` component.

3.  **Use the `ProductCard` in `App.jsx`.**
    - In `App.jsx`, import your new `ProductCard` component.
    - Instead of rendering an `<li>`, map over your `products` state and render a `<ProductCard>` for each product, passing the `product` object as a prop.
    - Wrap the list of cards in a container `div` and add some flexbox styles in `App.css` to arrange them in a grid.

<details>
  <summary>Click for Solution</summary>

**`src/components/ProductCard.jsx`**

```jsx
import './ProductCard.css';

function ProductCard({ product }) {
  return (
    <div className="product-card">
      <img src={product.thumbnail} alt={product.title} />
      <h3>{product.title}</h3>
      <p>${product.price}</p>
    </div>
  );
}

export default ProductCard;
```

**`src/components/ProductCard.css`**

```css
.product-card {
  border: 1px solid #ccc;
  border-radius: 8px;
  padding: 16px;
  margin: 8px;
  width: 200px;
  text-align: center;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
  background-color: white;
}

.product-card img {
  width: 100%;
  height: 150px;
  object-fit: cover;
  border-radius: 4px;
}
```

**`src/App.jsx` (updated)**

```jsx
import { useState, useEffect } from 'react';
import './App.css';
import ProductCard from './components/ProductCard'; // Import the component

function App() {
  const [products, setProducts] = useState([]);

  useEffect(() => {
    // ... fetch logic remains the same
    const fetchProducts = async () => {
      const response = await fetch('https://dummyjson.com/products');
      const data = await response.json();
      setProducts(data.products);
    };
    fetchProducts();
  }, []);

  return (
    <div className="App">
      <h1>My Product List</h1>
      <div className="product-container">
        {products.map(product => (
          <ProductCard key={product.id} product={product} />
        ))}
      </div>
    </div>
  );
}

export default App;
```

**`src/App.css` (updated)**

```css
.product-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 16px;
  padding: 20px;
}
```

</details>

<br>

### Iteration 3 - Add a Loading State

When the app first loads, there's a brief moment where no products are shown. Let's add a loading indicator to give the user feedback that something is happening.

1.  **Create a loading state.**
    In `App.jsx`, add a new piece of state called `loading`, initialized to `true`.

2.  **Update the loading state.**

    - Set `loading` to `false` inside your `fetchProducts` function after you have successfully set the products state.
    - If an error occurs, you should also set `loading` to `false`. A `finally` block in your `try...catch` is a great place for this.

3.  **Conditionally render the UI.**
    - If `loading` is `true`, display a simple "Loading..." message.
    - If `loading` is `false`, display the product container.

> [!TIP]
> You can use a ternary operator (`condition ? <JSX_A /> : <JSX_B />`) inside your return statement to easily switch between the loading message and the product list.

<details>
  <summary>Click for Solution</summary>

```jsx
// src/App.jsx (updated with loading state)
import { useState, useEffect } from 'react';
import './App.css';
import ProductCard from './components/ProductCard';

function App() {
  const [products, setProducts] = useState([]);
  const [loading, setLoading] = useState(true); // New loading state

  useEffect(() => {
    const fetchProducts = async () => {
      try {
        const response = await fetch('https://dummyjson.com/products');
        const data = await response.json();
        setProducts(data.products);
      } catch (error) {
        console.error('Failed to fetch products:', error);
      } finally {
        setLoading(false); // Set loading to false in both cases
      }
    };

    fetchProducts();
  }, []);

  return (
    <div className="App">
      <h1>My Product List</h1>
      {loading ? (
        <p>Loading products...</p>
      ) : (
        <div className="product-container">
          {products.map(product => (
            <ProductCard key={product.id} product={product} />
          ))}
        </div>
      )}
    </div>
  );
}

export default App;
```

</details>

<br>

**Happy coding!** :heart:

<br>

## Extra Resources

- [React Docs: `useState` hook](https://react.dev/reference/react/useState)
- [React Docs: `useEffect` hook](https://react.dev/reference/react/useEffect)
- [MDN: Using the Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch)
- [DummyJSON API](https://dummyjson.com/docs/products) - The API used in this lab. Explore other endpoints for more practice
