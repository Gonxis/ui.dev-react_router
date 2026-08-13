### (Practice) Sidebars

App.js
```js
/*
  1. Create a `routes` array that has three routes.
      / which renders ð  in the sidebar and "Home" in the body
      /rainbows which renders ð in the sidebar and "Rainbows" in the body
      /bears which renders ð» in the sidebar and "Bears" in the body

  2. Utilize your `routes` array to render the proper
    `Route`s both in the sidebar and in the body.
*/

import * as React from "react";
import { BrowserRouter as Router, Route, Routes, Link } from "react-router-dom";

export default function App() {
  return (
    <Router>
      <div className="wrapper">
        <div className="sidebar">
          <ul className="nav">
            <li>
              <Link to="/">Home</Link>
            </li>
            <li>
              <Link to="/rainbows">Rainbows</Link>
            </li>
            <li>
              <Link to="/bears">Bears</Link>
            </li>
          </ul>

          {/* Sidebar Routes */}
        </div>

        {/* Main Routes */}
      </div>
    </Router>
  );
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

styles.css
```css
.wrapper {
  display: flex;
}

.nav {
  display: flex;
  flex-direction: column;
  padding: 0;
}

.nav li {
  list-style-type: none;
}

.sidebar {
  width: 200px;
  padding-right: 20px;
  margin-right: 50px;
  border-right: 1px solid var(--white);
  color: #babcc1;
}

.body {
  margin: 0 50px;
}
```
