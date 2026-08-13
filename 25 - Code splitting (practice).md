### (Practice) Code Splitting

App.js
```js
import * as React from "react";
import { BrowserRouter as Router, Link, Routes, Route } from "react-router-dom";
import Dashboard from "./Dashboard";
import Home from "./Home";
import Newsletters from "./Newsletters";
import Loading from "./Loading";

export default function App() {
  return (
    <Router>
      <ul>
        <li>
          <Link to="/">Home</Link>
        </li>
        <li>
          <Link to="/newsletters">Newsletters</Link>
        </li>
        <li>
          <Link to="/dashboard">Dashboard</Link>
        </li>
      </ul>

      <hr />

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/newsletters" element={<Newsletters />} />
        <Route path="/dashboard" element={<Dashboard />} />
      </Routes>
    </Router>
  );
}
```


Dashboard.js
```js
export default function Dashboard() {
  return <h1>Dashboard</h1>;
}
```


Home.js
```js
export default function Home() {
  return <h1>Home</h1>;
}
```


Loading.js
```js
export default function Loading() {
  return <h1>Loading</h1>;
}
```


Newsletters.js
```js
export default function Newsletters() {
  return <h1>Newsletters</h1>;
}
```


index.js
```js
import React from "react";
import ReactDOM from "react-dom/client";
import "./theme.css";
import "./styles.css";
import App from "./App";

const rootElement = document.getElementById("root");
const root = ReactDOM.createRoot(rootElement);

root.render(
  <React.StrictMode>
    <div className="yellow-border" />
    <div className="container">
      <App />
    </div>
  </React.StrictMode>
);
```
