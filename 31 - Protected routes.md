### Protected Routes

Often times when building a web app, you'll need to protect certain routes in your application from users who don't have the proper authentication. Protected routes let us choose which routes users can visit based on whether they are logged in. For example, you might have public routes that you want anyone accessing, like a landing page, a pricing page, and the login page. Protected routes should only be available to users that are logged in, like a dashboard or settings page.

Though React Router doesn't provide any functionality for this out of the box, because it was built with composability in mind, adding it is fairly straight forward.

#### Warning
Note that this, or any other solution you write on the front-end, is going to be for UX purposes only. You should have proper checks in place on the server side to make sure users aren't getting access to data they shouldn't be.

Remember, any JavaScript that is in your client (front-end) code is not only accessible, but anyone can update it via the console. This is why it's not good enough to only check a user's authentication status using client-side JavaScript, because any developer could open up the console and update it.

This is why it's important to also have server-side checks in place **before** you send any data down to your client. No user should get access to private data unless they have the proper permissions, and by checking on the server, you ensure that's the case.


Before we go about creating our protected routes, we'll need a way to figure out if the user is authenticated. Because this is a tutorial about React Router protected routes and not about authentication, we'll use a fake `useAuth` Hook to determine our user's auth "status".

Though it's fake, it follows a good pattern of how you might want to implement a `useAuth` Hook for yourself.

```jsx
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

  return (
    <authContext.Provider value={auth}>
      {children}
    </authContext.Provider>
  );
}

export default function AuthConsumer() {
  return React.useContext(authContext);
}
```

Now whenever we want to know if the user is `authed`, `login`, or `logout`, we can use the `useAuth` Hook.

#### **More on useAuth**
There are lots of different ways the `useAuth` Hook could work.

Perhaps it makes an HTTP `Fetch` request to an API endpoint to validate a cookie. Or maybe it decodes a JWT token stored in the browser's localstorage. Or you could be using a third-party auth solution, like Firebase, and the `useAuth` Hook just exposes values from that library.

In any case, the goal is the same: find out if the user is currently authenticated.


Now that that's out of the way, let's start building out the rest of our app. We'll have 5 components, `Home` (lines 78 and 107, Home), `Pricing` (lines 79 and 108, Pricing), `Dashboard` (lines 81 and 109, Dashboard), `Settings` (lines 82 and 110, Settings), and `Login` (lines 84 and 111, Login), which will map nicely to our 5 routes,`/`, `/pricing`, `/dashboard`, `/settings`, and `/login`.

The `/`, `/pricing`, and `/login` routes will be publicly accessible while our `/dashboard` and `/settings` route will be private. For now, we'll just render them like normal `Route`s though.

```js
/src/App.js
import * as React from "react";
import { Link, Routes, Route } from "react-router-dom";

const Home = () => <h1>Home (Public)</h1>;
const Pricing = () => <h1>Pricing (Public)</h1>;

const Dashboard = () => <h1>Dashboard (Private)</h1>;
const Settings = () => <h1>Settings (Private)</h1>;

const Login = () => <h1>TODO</h1>

function Nav() {
  return (
    <nav>
      <ul>
        <li>
          <Link to="/">Home</Link>
        </li>
        <li>
          <Link to="/pricing">Pricing</Link>
        </li>
      </ul>
    </nav>
  );
}

export default function App() {
  return (
    <div>
      <Nav />

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/pricing" element={<Pricing />} />
        <Route path="/dashboard" element={<Dashboard />} />
        <Route path="/settings" element={<Settings />} />
        <Route path="/login" element={<Login />} />
      </Routes>
    </div>
  );
}
```

At this point we're not doing anything fancy. We've successfully mapped the app's location to a few components, typical React Router stuff.


Now let's start working on some auth. First, we'll build out our `Login` (line 127, Login) component. The goal of this component is, naturally, to allow the user to login. Because we already have our `useAuth` (lines 125 and 129, useAuth) Hook, most of the heavy lifting is already done.

```jsx
import { useNavigate } from 'react-router-dom'
import useAuth from './useAuth'

const Login = () => {
  const navigate = useNavigate();
  const { login } = useAuth();

  const handleLogin = () => {
    login().then(() => {
      navigate("/dashboard");
    });
  };

  return (
    <div>
      <h1>Login</h1>
      <button onClick={handleLogin}>
        Log in
      </button>
    </div>
  );
};
```

Our (simple) `Login` (line 127, Login) component renders a `header` (line 139, h1) and a `button` (lines 140 and 142, button). When the user clicks on the button, we call `login` (lines 129 and 132, login) (which we got from our `useAuth` (lines 125 and 129, useAuth) Hook), then once they're logged in, using `navigate` (lines 128 and 133, navigate), we send them to their `/dashboard` (line 133, "/dashboard").

#### Programmatically Navigate
If you're not familiar with React Router's `useNavigate` Hook or their `Navigate` component, now might be a good time to check out [How to Programmatically Navigate with React Router](https://fireship.dev/c/react-router-programmatically-navigate).


Next, let's add the ability to `logout`. Again, we already have our `logout` (lines 161 and 165, logout) method from our `useAuth` (lines 158 and 161. useAuth) Hook, so this too should simply be adding in some UI. All of the changes will be to our `Nav` (line 160, Nav) component.

```jsx
import { useNavigate } from 'react-router-dom'
import useAuth from './useAuth'

function Nav () {
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
          <Link to="/pricing">Pricing</Link>
        </li>
      </ul>
      {authed && (
        <button onClick={handleLogout}>
          Logout
        </button>
      )}
    </nav>
  );
}
```


Now the fun part, time to make our `/dashboard` and `/settings` routes private so only users who are authenticated can access them.

Let me propose what the final API might look like, before we dive into the implementation. What if, for every route we want to be private, instead of giving our `Route`s `element` prop the component we want it to render directly, we wrap it inside of a new component we'll call `RequireAuth`.

Something like this -

```jsx
<Routes>
  <Route path="/" element={<Home />} />
  <Route path="/pricing" element={<Pricing />} />
  <Route
    path="/dashboard"
    element={
      <RequireAuth>
        <Dashboard />
      </RequireAuth>
    }
  />
  <Route
    path="/settings"
    element={
      <RequireAuth>
        <Settings />
      </RequireAuth>
    }
  />
  <Route path="/login" element={<Login />} />
</Routes>
```

At this point, we know two main things about `RequireAuth`. First, it's only api is a `children` (lines 204 and 212, Dashboard, Settings) element. Second, if the user is authenticated, it should render that `children` element, if not, it should redirect the user to a page where they can authenticate (in our case, `/login`).

Using our `useAuth` Hook from earlier, `RequireAuth` becomes pretty simple.

```jsx
function RequireAuth({ children }) {
  const { authed } = useAuth();

  return authed === true
    ? children
    : <Navigate to="/login" replace />;
}
```

Again, there's nothing fancy going on here. If you're familiar with JavaScript and React, the solution should feel relatively simple. React Router gives you the routing primitives upon which you can build your app – nothing more, and nothing less.


At this point, everything is working fine. When a user who isn't authenticated tries to go to `/dashboard` or `/settings`, they're redirected to `/login`. Then once they login, we redirect them back to `/dashboard`.

Do you notice any problems with our current implementation though? It's small, but it's a UX anti-pattern. Instead of always redirecting the user to `/dashboard`, we should redirect them to the route they were originally trying to visit.

For example, if they try to visit `/settings` but aren't logged in, after we redirect them and they log in, we should take them back to `/settings`, not `dashboard`.

To do this, we'll need to make our `RequireAuth` component a little smarter. It'll need to know what route the user was originally trying to visit, so it can redirect them back there after they authenticate.

First, we'll use React Router's `useLocation` Hook to grab the user's `location` (line 255, location) they were trying to visit.

```jsx
import { useLocation } from 'react-router-dom'

...


function RequireAuth({ children }) {
  const { authed } = useAuth();
  const location = useLocation();

  return authed === true
    ? children
    : <Navigate to="/login" replace />;
}
```

Now that we have the `location` they were trying to visit, we'll need to pass that along to our `/login` route. This is simple as `Navigate` has a `state` (line 275, state) prop we can use to do just this.

```jsx
function RequireAuth({ children }) {
  const { authed } = useAuth();
  const location = useLocation();

  return authed === true
    ? children
    : <Navigate
        to="/login"
        replace
        state={{ path: location.pathname }}
      />;
}
```

And finally, inside our `Login` component, we can use React Router's `useLocation` Hook to get access to `location.state` (lines 290 and 294, state), which will have our `path` (line 294, path) property.

After they authenticate, we'll redirect the user back to the original `path` (line 294, path) if it exists, and if it doesn't, we'll take them to `/dashboard`.

```jsx
import { useLocation } from 'react-router-dom'

const Login = () => {
  const navigate = useNavigate();
  const { login } = useAuth();
  const { state } = useLocation();

  const handleLogin = () => {
    login().then(() => {
      navigate(state?.path || "/dashboard");
    });
  };

  return (
    <div>
      <h1>Login</h1>
      <button onClick={handleLogin}>Log in</button>
    </div>
  );
};
```


This is just one example of how you can use React Router to add protected routes to your React application. Because React Router embraces React's composition model, you can compose it together in any way that makes sense for your app.


---------------------------------------------------------------------------------

Comments:

- Very interesting, i've learnt more about useLocation with saved state and applied it in my project. :D

- Why isn't browser router being used here?

- I tried making a protected route the way it is shown and it created a render loop. What could have gone wrong?

- Typo at: Warning - More info. - last sentence: "privte".

- I believe we should pass all the properties of a URL in the location state for redirect and not just the pathname. It will allow to go to the exact URL after redirect.

Something like this:

```js
function RequireAuth({ children }) {
  const { authed } = useAuth();
  const location = useLocation();

  return authed === true ? (
    children
  ) : (
    <Navigate
      to="/login"
      replace
      state={{
        pathname: location.pathname,
        search: location.search,
        hash: location.hash,
      }}
    />
  );
}
```

- AuthConsumer looks like it is a function component (capitalized) but instead returns authContext value? Could you give an example how this would this be used?

export default function AuthConsumer() { return React.useContext(authContext); }
  
  -  You could use a hook for this, for example:

    const useAuthConsumer = () => React.useContext(authContext);

    then, to use it:

    const { login, logout, authed } = useAuthConsumer();