### (Practice) The Fundamentals

App.js
```js
/*
  You're given three components, Home, About, and Jobs.
  
  1. Using those three components, create the following
  path -> Component mapping.

  / -> Home
  /about -> About
  /jobs -> Jobs


  2. Create a navbar which allows you to navigate
  between your three routes.

  *React Router is already installed.
*/

import * as React from "react";
import { BrowserRouter as Router } from "react-router-dom";

const Home = () => <h1>Home</h1>;
const About = () => <h1>About</h1>;
const Jobs = () => <h1>Jobs</h1>;

export default function App() {
  return <div>See the instructions above</div>;
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


