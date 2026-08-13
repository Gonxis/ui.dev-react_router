### Sidebars

One thing I love about React Router is how composable and "React like" it is. If you're familiar with the mental model of React, then you're probably already familiar with the mental model of React Router. This is demonstrated perfectly in the example we're going to walk through – building a sidebar with React Router.

Specifically, here's an example of what we'll be building.

It's a fairly basic app. We'll have three components – `Home`, `Profile`, and `Settings` – which will map nicely to our three paths – `/`, `/profile`, and `/settings`.

What I want you to notice though is the text inside the sidebar. Just like the body of the app, it's also dynamic based on the app's current location. You're probably used to seeing React Router dynamically swap out single components based on the app's current location, but you usually don't see it happen in multiple places. Let's walk through how to do it.

First, we'll start with the basics – our simple components and navbar.

```jsx
import { Link } from 'react-router-dom'

const Home = () => <h1>Home</h1>;
const Profile = () => <h1>Profile</h1>;
const Settings = () => <h1>Settings</h1>;

export default function App() {
  return (
    <div className="wrapper">
      <div className="sidebar">
        <ul className="nav">
          <li><Link to="/">Home</Link></li>
          <li><Link to="/profile">Profile</Link></li>
          <li><Link to="/settings">Settings</Link></li>
        </ul>
      </div>
    </div>
  );
}
```

Next, we need to render some `Route`s so React Router can know which component to render when the user visits certain a location. Before we worry about the sidebar, let's render the main `Route`s (lines 41 and 56-58, Route) for the body of the app.

```jsx
import {
  Link,
  Routes,
  Route
} from 'react-router-dom'

export default function App() {
  return (
    <div className="wrapper">
      <div className="sidebar">
        <ul className="nav">
          <li><Link to="/">Home</Link></li>
          <li><Link to="/profile">Profile</Link></li>
          <li><Link to="/settings">Settings</Link></li>
        </ul>
      </div>

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/profile" element={<Profile />} />
        <Route path="/settings" element={<Settings />} />
      </Routes>
    </div>
  );
}
```

At this point our app is working fine, but we haven't really done anything special. We've simply mapped our app's location to a few components using React Router. Now we need to figure out the sidebar. Any ideas?

Remember, the goal here is, just like we did with the main body of our app, to make our sidebar dynamic based on the app's location. Well, what if we just render another `Routes` component inside the navbar itself? Then we could create another set of `Route` components, mapping the app's current location to the text we want to show in the navbar.

Can we do that...? Why not?

```js
/src/App.js
export default function App() {
  return (
    <div className="wrapper">
      <div className="sidebar">
        <ul className="nav">
          <li><Link to="/">Home</Link></li>
          <li><Link to="/profile">Profile</Link></li>
          <li><Link to="/settings">Settings</Link></li>
        </ul>

        <Routes>
          <Route
            path="/"
            element={
              <p>
                This is your home page. You'll see your feed which is made up of
                the people you follow.
              </p>
            }
          />
          <Route
            path="/profile"
            element={
              <p>
                This is your profile page. You'll be able to see all your
                profile information as well as the people you follow.
              </p>
            }
          />
          <Route
            path="/settings"
            element={
              <p>
                This is your settings page. You can change your name, image, and
                anything else associated with your account.
              </p>
            }
          />
        </Routes>
      </div>

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/profile" element={<Profile />} />
        <Route path="/settings" element={<Settings />} />
      </Routes>
    </div>
  );
}
```

Your brain might be thinking there's something magic going on here but there's really not. With React Router, we can render as many `Routes` as we'd like and the outcome is the same. Whenever the app's location changes, any `Routes`component will look through all its children `Route` elements to find the best match to render. This process is true whether we have 1 `Routes` or 20.


We can even clean this up a bit if you'd like. Right now we're having to duplicate our `path`s in multiple places. It's not a huge deal, but as our app grows larger, this might become an inconvenience later on.

Because React is "Just JavaScript™", there's nothing stopping us from encapsulating all our routing logic into its `own array` (line 132, routes).

```js
const routes = [
  {
    path: "/",
    main: () => <Home />,
    sidebar: () => (
      <p>
        This is your home page. You'll see your feed which is made up of the
        people you follow.
      </p>
    )
  },
  {
    path: "/profile",
    main: () => <Profile />,
    sidebar: () => (
      <p>
        This is your profile page. You'll be able to see all your profile
        information as well as the people you follow.
      </p>
    )
  },
  {
    path: "/settings",
    main: () => <Settings />,
    sidebar: () => (
      <p>
        This is your settings page. You can change your name, image, and
        anything else associated with your account.
      </p>
    )
  }
];
```

Now that we have our `routes` array, we can map over it whenever we want to create a new collection of `Route`s.

```js
/src/App.js
export default function App() {
  return (
    <div className="wrapper">
      <div className="sidebar">
        <ul className="nav">
          <li><Link to="/">Home</Link></li>
          <li><Link to="/profile">Profile</Link></li>
          <li><Link to="/settings">Settings</Link></li>
        </ul>

        <Routes>
          {routes.map(({ path, sidebar }) => (
            <Route
              key={path}
              path={path}
              element={sidebar()}
            />
          ))}
        </Routes>
      </div>

      <Routes>
        {routes.map(({ path, main }) => (
          <Route
            key={path}
            path={path}
            element={main()}
          />
        ))}
      </Routes>
    </div>
  );
}
```

Because React Router allows us to render as many `Routes` as we'd like, and because we abstracted our `routes` to their own array, we can render different components at different sections of our page whenever the app's location matches the `Route`'s `path`.
