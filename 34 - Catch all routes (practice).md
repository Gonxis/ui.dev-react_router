### (Practice) Catch All Routes

App.js
```js
/*
  You're given three components, `Home`, `Settings`, and
  `FourZeroFour`.

  1. Create a navbar to navigate between `/` 
    and `/settings`.

  2. Render your `Route`s according to the table below

    URL    Component
    `/` -> `Home`
    `/settings` -> `Settings`
    anything else -> `FourZeroFour`
 */

    import * as React from "react";
    import { BrowserRouter as Router, Routes, Route, Link } from "react-router-dom";
    
    const Home = () => <h1>Home</h1>;
    const Settings = () => <h1>Settings</h1>;
    const FourZeroFour = () => <h1>404</h1>;
    
    export default function App() {
      return (
        <Router>
          {/* Nav here */}
    
          {/* Routes here */}
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
