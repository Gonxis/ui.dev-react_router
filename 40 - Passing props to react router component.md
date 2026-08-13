### Passing Props to React Router Components

React Router uses a declarative, component-based approach to routing. What that means is when you want to create a new route, you render a `Route` component. `Route` allows you to map URL paths to different React components. For example, say we wanted to render a `Dashboard` (line 8, Dashboard) component whenever a user navigates to the `/dashboard` (line 7, /dashboard) path. To do that, we'd render a `Route` (line 6, Route) that looked like this.

```jsx
<Route
  path='/dashboard'
  element={<Dashboard />}
/>
```

Now, what if we also wanted to pass the `Dashboard` component a prop?

In previous versions of React Router (v4), this was [non-trivial](https://fireship.dev/c/react-router/react-router-v4-pass-props-to-components) since React Router was in charge of creating the React element. To get around this, you'd have to use `Route`s `render` (line 20, render) prop.

```jsx
// React Router v4
<Route
  path='/dashboard'
  render={(props) => (
    <Dashboard {...props} authed={true} />
  )}
/>
```

However, with React Router v6, since you're in charge of creating the element, you just pass a prop to the component as `you normally would` (line 21, authed).

```jsx
<Route
  path='/dashboard'
  element={<Dashboard authed={true}/>}
/>
```


---------------------------------------------------------------------------------

Comments:

- can we get more info on this loader and errorElement prop? https://reactrouter.com/en/main/route/error-element