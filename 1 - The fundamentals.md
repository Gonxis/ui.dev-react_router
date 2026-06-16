### The fundamentals

It's probably no surprise to you that React Router is the most popular 3rd party library in the React ecosystem. In fact, during the last 6 months, React Router has been included in [44%](https://www.npmtrends.com/react-router-dom-vs-react-dom) of all React projects. This statistic alone is enough to declare React Router as essential knowledge for any serious React developer.

The problem, honestly, lies in the fact that no one wants to spend their weekend learning about a router – regardless of how popular it is. To make that pill a little easier to swallow, this course is a culmination of everything you'll need to know in order to be effective with React Router.

In this course, we'll start off with a high-level look at what React Router is. From there we'll dive into the fundamentals of the API. Finally, we'll finish off looking at a <s>few</s> lot of different use cases you may face in the real-world.


## What is React Router?

First created in 2014, React Router is a declarative, component based, client and server-side routing library for React. Just as React gives you a declarative and composable API for adding to and updating application state, React Router gives you a declarative and composable API for adding to and updating the user's navigation history.

If you're new to React, it may come as a surprise to know that a router isn't baked into the library itself, but that's foundational to React's ethos. React focuses on giving you UI primitives for building your application, and nothing more.

Poetically, React Router follows a similar ethos, except instead of UI primitives, they give you routing primitives. To align with React, naturally, these "routing primitives" are really just a collection of React components and Hooks.

Let's dive into the most important ones before we look at specific use cases.


## BrowserRouter

Naturally, in order to do its thing, React Router needs to be both aware and in control of your app's location. The way it does this is with its `BrowserRouter` component.

Under the hood, `BrowserRouter` uses both the [history](https://github.com/ReactTraining/history) library as well as [React Context](https://fireship.dev/c/react/react-context). The history library helps React Router [keep track of](https://github.com/ReactTraining/react-router/blob/dev/packages/react-router-dom/index.tsx#L98) the browsing history of the application using the browser's built-in history stack, and React Context helps make history [available wherever](https://github.com/ReactTraining/react-router/blob/dev/packages/react-router/index.tsx#L245) React Router needs it.

There's not much to `BrowserRouter`, you just need to make sure that if you're using React Router on the web, you wrap your app inside of the `BrowserRouter` component.

```js
import ReactDOM from 'react-dom'
import * as React from 'react'
import { BrowserRouter } from 'react-router-dom'
import App from './App`

ReactDOM.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>
, document.getElementById('app))
```


### The other <Routers />

If you're using React Router in an environment that isn't the browser, check out `MemoryRouter` and `StaticRouter`.

`MemoryRouter` keeps track of the history of the application in memory, rather than in the URL. Use this instead of `BrowserRouter` if you're developing a React Native application.

`StaticRouter`, as the name implies, is useful in environments where the app's location never actually changes, like when rendering a single route to static HTML on a server.

Now that you know how to enable React Router via the `BrowserRouter` component, let's look at how you can actually tell React Router to create a new route.


## Route

Put simply, `Route` allows you to map your app's location to different React components. For example, say we wanted to render a `Dashboard` component whenever a user navigated to the `/dashboard` path. To do so, we'd render a `Route` that looked like this.

```jsx
<Route path='/dashboard' element={<Dashboard />} />
```

The mental model I use for `Route` is that it always has to render something – either its `element` prop if the `path` matches the app's current location or `null`, if it doesn't.

You can render as many `Route`s as you'd like.

```jsx
<Route path="/" element={<Home />} />
<Route path="/about" element={<About />} />
<Route path="/settings" element={<Settings />} />
```

You can even render nested routes, which we'll talk about later on in this post.

With our `Route` elements in this configuration, it's possible for multiple routes to match on a single URL. You might want to do that sometimes, but most often you want React Router to only render the route that matches best. Fortunately, we can easily do that with `Routes`.


## Routes

You can think of `Routes` as the metaphorical conductor of your routes. Whenever you have one or more `Route`s, you'll most likely want to wrap them in a `Routes`.

```jsx
import {
  Routes,
  Route
} from 'react-router-dom'

function App () {
  return (
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/about" element={<About />} />
      <Route path="/settings" element={<Settings />} />
      <Route path="*" element={<NotFound />} />
    </Routes>
  )
}
```

The reason for this is because it's `Routes` job is to understand all of its `children` `Route` elements, and intelligently choose which ones are the best to render.

Though it's not shown in the simple example above, once we start adding more complex `Route`s to our application, `Routes` will start to do more work like enabling intelligent rendering and relative paths. We'll see these scenarios in a bit.

Next up, linking between pages.


## Link

Now that you know how to map the app's location to certain React components using `Routes` and `Route`, the next step is being able to navigate between them. This is the purpose of the `Link` component.

To tell `Link` what path to take the user to when clicked, you pass it a `to` prop.

```jsx
<nav>
  <Link to="/">Home</Link>
  <Link to='/about'>About</Link>
  <Link to="/settings">Settings</Link>
</nav>
```

If you need more control over `Link`, you can also pass `to` as an object. Doing so allows you to add a query string via the `search` property or pass along any data to the new route via `state`.

```jsx
<nav>
  <Link to='/'>Home</Link>
  <Link to='/about'>About</Link>
  <Link to={{
    pathname: '/settings',
    search: '?sort=date',
    state: { fromHome: true },
  }}>Settings</Link>
</nav>
```

We'll cover `state`, Query Strings, and how React Router supports relative paths in more depth later on in this course.


At this point we've covered both the history and the absolute fundamentals of React Router, but one thing should already be clear - by embracing composition, React Router is truly a router for React. I believe React will make you a better JavaScript developer and React Router will make you a better React developer.

Now, instead of just walking you through the rest of the API, we'll take a more practical approach by breaking down all of the common use cases you'll need when using React Router.


--------------------------------------------------------------------------------------

Conmments:

- Since React-Router v7 can be used as a framework (Remix is now React Router). Is there any plan to cover that (in a different course maybe) ?
  - No Response..

- Will v6 courses be offered?
    - This course is v6.