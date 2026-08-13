### Route Configs

React Router has gone through a few different iterations over the years. Though the current API (v6) takes a declarative, component-based, `<Route />` as you go approach – this hasn't always been the case.

In fact, in the first versions of React Router (v1-v3), instead of composing your `Route`s as you do now throughout your application, you'd declare them up front in a central route config then pass that to `ReactDOM.render`.

```jsx
const routes = (
  <Router>
    <Route path='/' component={Main}>
      <IndexRoute component={Home} />
      <Route path='battle' component={ConfirmBattle} />
      <Route path='results' component={Results} />
    </Route>
  </Router>
)

ReactDOM.render(routes, document.getElementById('app'))
```

Though React Router has moved away from this central route config approach, it still had its benefits. Namely, when server rendering or doing static analysis.

The good news is, as of v6, React Router now comes with a `useRoutes` Hook that makes collocating your routes into a central route config not only possible, but simple with a first class API as well.

Say we had the following paths in our application.

```text
/
/invoices
  :id
  pending
  complete
/users
  :id
  settings
```

Typically if you wanted to map those paths to different React components, you'd render something like this.

```jsx
return (
  <Routes>
    <Route path='/' element={<Home />} />
    <Route path='/invoices' element={<Invoices />}>
      <Route path=':id' element={<Invoice />} />
      <Route path='pending' element={<Pending />} />
      <Route path='complete' element={<Complete />} />
    </Route>
    <Route path='/users/*' element={<Users />} />
  </Routes>
)
```

Notice that we're rendering the nested routes for `invoices/:id` (line 45, :id), `invoices/pending` (line 46, pending), and `invoices/complete` (line 47, complete) here but the nested routes for `/users/:id` (line 49, /users/*) and `/users/settings` (line 49, /users/*) are going to be rendered inside the `Users` (line 49, Users) component.

Now what `useRoutes` allows us to do is, instead of declaring our routes using React elements, we can do it using JavaScript objects all in one location.

`useRoutes` takes in an array of JavaScript objects which represent the routes in your application. Similar to the React element API with `<Route>`, each route has a `path`, `element`, and an optional `children` property.

```js
import { useRoutes } from 'react-router-dom'

const routes = useRoutes([
  { path: '/', element: <Home /> },
  {
    path: '/invoices',
    element: <Invoices />,
    children: [
      { path: ':id', element: <Invoice /> },
      { path: '/pending', element: <Pending /> },
      { path: '/complete', element: <Complete /> },
    ]
  },
  {
    path: '/users',
    element: <Users />,
    children: [
      { path: ':id', element: <Profile /> },
      { path: '/settings', element: <Settings /> },
    ]
  }
])

export default function App () {
  return (
    <div>
      <Navbar />
      {routes}
    </div>
  )
}
```

What makes `useRoutes` even more interesting is how React Router uses it internally. In fact, when you use the React element API to create your `Routes`, it's really just a [wrapper around `useRoutes`](https://github.com/ReactTraining/react-router/blob/f59ee5488bc343cf3c957b7e0cc395ef5eb572d2/packages/react-router/index.js#L234-L237).


---------------------------------------------------------------------------------

Comments:

- It would be nice to have the course updated with the 6.4v API. Do you guys have any plans to do that?

- If I try something similar, I get an error: /App.js: Absolute route path "/pending" nested under path "/invoices" is not valid. An absolute child route path must start with the combined path of all its parent routes. (45:27)
