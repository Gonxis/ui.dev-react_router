### (Practice) Route Configs

App.js
```js
/*
  Using useRoutes, create a central route config 
  that has the following path -> component mapping.

  / -> Home
  /newsletters -> Newsletters
    /newsletters/react -> Publication
    /newsletters/bytes -> Publication
*/

import * as React from "react";
import {
  BrowserRouter as Router,
  Link,
  Outlet,
  useRoutes,
  useParams
} from "react-router-dom";

const Home = () => <h1>Home</h1>;

function Publication() {
  const { id } = useParams();

  return <h3>Publication: {id}</h3>;
}

const Newsletters = () => (
  <div>
    <h1>Newsletters</h1>
    <ul>
      <li>
        <Link to="/newsletters/react">React</Link>
      </li>
      <li>
        <Link to="/newsletters/bytes">Bytes</Link>
      </li>
    </ul>
  </div>
);

function App() {
  return (
    <div>
      <ul>
        <li>
          <Link to="/">Home</Link>
        </li>
        <li>
          <Link to="/newsletters">Newsletters</Link>
        </li>
      </ul>

      <hr />
    </div>
  );
}

export default function Index() {
  return (
    <Router>
      <App />
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
