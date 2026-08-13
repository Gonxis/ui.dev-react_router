### (Practice) Protected Routes

App.js
```js
/*
  1. Finish implementing the RequireAuth component.

  2. Make the /notifications and the /settings routes private.

  3. Finish implementing the Login component such that 
  users can log in, then, if needed, be re-directed 
  to the route they were initially trying to visit.
*/

import * as React from "react";
import {
  Link,
  Routes,
  Route,
  useNavigate,
  useLocation,
  BrowserRouter as Router
} from "react-router-dom";
import useAuth, { AuthProvider } from "./useAuth";

const Home = () => <h1>Home (Public)</h1>;
const About = () => <h1>About (Public)</h1>;

const Notifications = () => <h1>Notifications (Private)</h1>;
const Settings = () => <h1>Settings (Private)</h1>;

const Login = () => {
  const { login } = useAuth();

  const handleLogin = () => {
    login();
  };

  return (
    <div>
      <h1>Login</h1>
      <button onClick={handleLogin}>Log in</button>
    </div>
  );
};

function RequireAuth() {
  const { authed } = useAuth();

  return null;
}

function Nav() {
  const { authed, logout } = useAuth();
  const navigate = useNavigate();

  const handleLogout = () => {
    logout();
    navigate("/");
  };

  return (
    <nav>
      <ul>
        <li>
          <Link to="/">Home</Link>
        </li>
        <li>
          <Link to="/pricing">About</Link>
        </li>
      </ul>
      {authed && <button onClick={handleLogout}>Logout</button>}
    </nav>
  );
}

export default function App() {
  return (
    <Router>
      <AuthProvider>
        <Nav />

        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/pricing" element={<About />} />
          <Route path="/notifications" element={<Notifications />} />
          <Route path="/settings" element={<Settings />} />
          <Route path="/login" element={<Login />} />
        </Routes>
      </AuthProvider>
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


useAuth.js
```js
import * as React from "react";

const authContext = React.createContext();

function useAuth() {
  const [authed, setAuthed] = React.useState(false);

  return {
    authed,
    login() {
      return new Promise((res) => {
        setAuthed(true);
        res();
      });
    },
    logout() {
      return new Promise((res) => {
        setAuthed(false);
        res();
      });
    }
  };
}

export function AuthProvider({ children }) {
  const auth = useAuth();

  return <authContext.Provider value={auth}>{children}</authContext.Provider>;
}

export default function AuthConsumer() {
  return React.useContext(authContext);
}
```


styles.css
```css
ul {
  display: flex;
  padding: 0;
}

li {
  list-style-type: none;
  margin-right: 10px;
}

nav {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

a {
  text-decoration: none;
}

nav > button {
  background: transparent;
  color: var(--white);
  border: none;
}
```
