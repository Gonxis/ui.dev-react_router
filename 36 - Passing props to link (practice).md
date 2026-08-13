### (Practice) Passing Props to Link

App.js
```js
/*
  The App component has a `friends` array on its state. You need to do
  two things - refactor the `Link` to pass the array through to the `Friends`
  component then use it inside of that component.
*/

import * as React from "react";
import { BrowserRouter as Router, Routes, Route, Link } from "react-router-dom";

const Friends = () => {
  const friends = [];
  return (
    <React.Fragment>
      <h1>Friends</h1>
      <ul>
        {friends.map(({ name, handle, avatar }) => (
          <li key={name} className="user">
            <img alt={`Avatar for ${name}`} src={avatar} />
            <a href={`https://twitter.com/${handle}`}>{name}</a>
          </li>
        ))}
      </ul>
    </React.Fragment>
  );
};

const Home = () => <h1>Home</h1>;

export default function App() {
  const [friends] = React.useState([
    {
      name: "Tyler McGinnis",
      handle: "tylermcginnis",
      avatar:
        "https://res.cloudinary.com/uidotdev/image/twitter_name/tylermcginnis"
    },
    {
      name: "Sarah Drasner",
      handle: "sarah_edo",
      avatar: "https://res.cloudinary.com/uidotdev/image/twitter_name/sarah_edo"
    },
    {
      name: "Alex Anderson",
      handle: "ralex1993",
      avatar: "https://res.cloudinary.com/uidotdev/image/twitter_name/ralex1993"
    }
  ]);

  return (
    <Router>
      <div>
        <ul>
          <li>
            <Link to="/">Home</Link>
          </li>
          <li>
            <Link to="/friends">Friends</Link>
          </li>
        </ul>

        <hr />

        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/friends" element={<Friends />} />
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
