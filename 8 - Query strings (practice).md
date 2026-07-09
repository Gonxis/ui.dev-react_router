### (Practice) Query Strings

App.js
```js
/*
  Inside the User component you need to parse the query string from the 
  URL and then show the information about the selected user (which you can 
    get from the `users` object).

  If no user is selected, show "Select a user".
*/

import * as React from "react";
import { BrowserRouter as Router, Routes, Route, Link } from "react-router-dom";

const users = {
  tylermcginnis: {
    name: "Tyler McGinnis",
    handle: "tylermcginnis",
    avatar:
      "https://res.cloudinary.com/uidotdev/image/twitter_name/tylermcginnis"
  },
  sarah_edo: {
    name: "Sarah Drasner",
    handle: "sarah_edo",
    avatar: "https://res.cloudinary.com/uidotdev/image/twitter_name/sarah_edo"
  },
  ralex1993: {
    name: "Alex Anderson",
    handle: "ralex1993",
    avatar: "https://res.cloudinary.com/uidotdev/image/twitter_name/ralex1993"
  }
};

function User() {
  return <div />;
}

export default function App() {
  return (
    <Router>
      <div>
        <h1>Users</h1>
        <ul>
          <li>
            <Link to="/?id=tylermcginnis">Tyler</Link>
          </li>
          <li>
            <Link to="/?id=sarah_edo">Sarah</Link>
          </li>
          <li>
            <Link to="/?id=ralex1993">Alex</Link>
          </li>
        </ul>

        <hr />

        <Routes>
          <Route path="/" element={<User />} />
        </Routes>
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


style.css
```css
a {
  color: var(--yellow);
  font-weight: 600;
  font-size: 21px;
}

.user {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

img {
  width: 150px;
  height: 150px;
  border-radius: 50%;
  margin: 50px 0 20px 0;
}
```
