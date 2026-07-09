### Customizing React Router's Link Component

One thing I love about React Router is how composable it is. React Router doesn't give you a house - it gives you some nails, plywood, and a hammer and trusts that you can do the rest. A more technical way to say that is React Router gives you the routing primitives upon which you can build your app. This concept really shines when it comes to what we're going to do in this post – build our own custom `Link` component.

What we want to do is create our own "old school" navbar. Basically what that means is we'll add a `>` to the front of whatever `Link` is 
active (In this case, a `Link` will be active when it's `to` prop matches the app's current location.). If our two routes were `/` and `/about`, the two states of our navbar would look like this

> Home
About

Before we dive into our custom `Link`, let's build out the skeleton of our app. We'll have two components, `Home` (lines 22 and 34, Home) and `About` (lines 23 and 35, About), which will map to our two `Route`s (lines 18, 34 and 35 - Route, Route and "/", Route and "/about"), `/` (line 34 - /) and `/about` (line 35 - /about).

```jsx
import * as React from 'react'
import {
  BrowserRouter as Router,
  Routes,
  Route,
  Link
} from 'react-router-dom'

const Home = () => <h2>Home</h2>
const About = () => <h2>About</h2>

export default function App () {
  return (
    <Router>
      <div>
        {/* Links */}

        <hr/>

        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/about" element={<About />} />
        </Routes>
      </div>
    </Router>
  )
}
```

Now the reason we're here, implementing our custom `Link` component. We'll call it `OldSchoolMenuLink` (lines 50, 52, 53 and 55 - OldSchoolMenuLink). Let's start with the API and work backwards. Here's what it'll look like

```jsx
export default function App() {
  return (
    <Router>
      <div>
        <OldSchoolMenuLink to="/">
          Home
        </OldSchoolMenuLink>
        <OldSchoolMenuLink to="/about">
          About
        </OldSchoolMenuLink>

        <hr/>

        <Routes>
          <Route path="/" element={<Home />}>
          <Route path="/about" element={<About />}>
        </Routes>
      </div>
    </Router>
  )
}
```

Notice it's the `OldSchoolMenuLink` that will be in charge of adding and removing the `>`, but its API is the same as React Router's Link component.

Now let's build it out. We know what props `OldSchoolMenuLink` is going to be taking in, so we can build out the skeleton of the component before we worry about its implementation.

```js
function OldSchoolMenuLink ({ children, to }) {}
```

> WTF is children?
If you're not familiar with the "children" prop in React, it's a placeholder for whatever is between the opening and closing element.
```jsx
<Hover>Children can be anything</Hover>
<Hover>{true}</Hover>
<Hover>{() => console.log('Even functions')}</Hover>
```

Now the main question becomes, what is `OldSchoolMenuLink` going to render? Remember, the whole point of this component is, based on the active route, to make this navbar UI work.

> Home
About

With that said, we know we're going to render a `Link` and if the app's current location matches the `Link`s `to` prop, we'll pre-pend it with a `>`.

In order to do that, we need to get the "app's current location". To do that we can use React Router's `useLocation` (line 96 - useLocation()) Hook. From there, all we need to do is compare the `location`'s (lines 96 and 97 - location) `pathname` (line 97 - pathname) with the `to` (lines 95 and 97 - to) prop.

```jsx
function OldSchoolMenuLink ({ children, to }) {
  const location = useLocation()
  const match = location.pathname === to

  return ()
}
```

Now that we know if the app's current location matches the `Link`s path, all that's left to do is render some UI, toggling the `>` based on our `match` (lines 109, 112 and 113 - match) variable.

src/App.js
```js
function OldSchoolMenuLink ({ children, to }) {
  const location = useLocation()
  const match = location.pathname === to

  return (
    <div className={match ? 'active' : ''}>
      {match ? '> ' : ''}
      <Link to={to}>
        {children}
      </Link>
    </div>
  )
}
```

Just like that, we've created our own custom `OldSchoolMenuLink` component by composing React Router's `Link` component.


---------------------------------------------------------------------------------

Comments:

- And this course might talk about it later, but we can also use the NavLink component to set an active class on the Link that matches the current path.

- I think you can use the useMatch hook to achieve the same thing. Alternative solution to useLocation Hook:

```jsx
const match = useMatch(to);
return (
    <div className={match ? 'active' : ''}>
      {match && '> '}
       <Link to={to}>
          {children}
        </Link>
    </div>
);
```

  - Wow, that's great!
  - This approach is a bit cleaner because it directly leverages useMatch, which is specifically intended for this purpose, making the code slightly more readable and idiomatic in React Router.
