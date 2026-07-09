### (Practice) Customizing Link

App.js
```js
/*
  Your application has three routes, `/`, `/notifications`,
  and `/settings`. 
  
  Your job is to implement the `EmojiLink`
  custom `Link` component so that it renders the `Emoji`
  along with the name (`children`) if the app's location
  matches the `Link`s `path`.
*/

import * as React from "react";
import { BrowserRouter as Router, Routes, Route, Link } from "react-router-dom";

const Home = () => <h2>Home</h2>;
const Settings = () => <h2>Settings</h2>;
const Notifications = () => <h2>Notifications</h2>;

const EmojiLink = ({ children, to, emoji }) => <div />;

export default function App() {
  return (
    <Router>
      <div>
        <EmojiLink exact={true} to="/" emoji={`ð `}>
          Home
        </EmojiLink>
        <EmojiLink to="/notifications" emoji={`ð`}>
          Notifications
        </EmojiLink>
        <EmojiLink to="/settings" emoji={`âï¸`}>
          Settings
        </EmojiLink>

        <hr />

        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/notifications" element={<Notifications />} />
          <Route path="/settings" element={<Settings />} />
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


---------------------------------------------------------------------------------

Comments:

- Initial emoji are weird ... I replace them with 🏠🟢⚙️
