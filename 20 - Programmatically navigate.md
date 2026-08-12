### Programmatically Navigate

What I love about React Router is its dedication to declarative, "React like" code. The whole goal of the redesign from React Router v3 to v4+ was to align React Router's vision with React's. Fundamentally, what this means is that the same mental model that applies to React should also apply to React Router.

If you broke React down into three core principles, you'd get **component composition, declarative UI**, and **state management** - specifically, that **user events trigger state changes, which then causes a re-render**. Because React Router's vision is aligned with React's, programmatically navigating with React Router should, by definition, align with those three core concepts. The reason I'm emphasizing this is because your initial reaction to what's coming next might be negative.


#### Declarative Navigation with `<Navigate />`

The primary way you programmatically navigate using React Router is by using a `<Navigate />` component. Now I know what you're thinking - how could navigating with a component be a good idea? But let's first look at an example, and then we'll walk through why this isn't as crazy as it may first appear.

The typical use case for routing programmatically is routing on some sort of user event that isn't a `Link` click. So in our example, let's `Navigate` (lines 15 and 21, Navigate) the user to `/dashboard` (line 21, /dashboard) once they've registered for our app.

```jsx
import { Navigate } from 'react-router-dom'

function Register () {
  const [toDashboard, setToDashboard] = React.useState(false)

  if (toDashboard === true) {
    return <Navigate to='/dashboard'/>
  }

  return (
    <div>
      <h1>Register</h1>
      <Form afterSubmit={() => toDashboard(true)} />
    </div>
  )
}
```

After reading that, there's at least a small chance that you hate it. Instead of using an imperative API, something like `navigate('/dashboard')`, we're using a [declarative`Navigate`](https://fireship.dev/c/react/imperative-vs-declarative-programming) (lines 15 and 21, Navigate) component. Again, the reason for this is because it aligns exactly with the principles of React itself.

`<Navigate />` is composable, declarative, and follows the flow of React – namely a user event triggers a state change which causes a re-render.

What are the downsides to this approach? The most often heard criticism is that you need to create a new property on the component's state in order to know when to render the `Navigate` component. That's valid, but again, that's pretty much the whole point of React - state changes update the UI.

"It's more typing", yes. Naturally, by explicitly defining and modifying your state, you have to type more. However, I'd argue that explicit state leading to a declarative API is better than implicit state handled by an imperative API.

**Steps off high horse**

Let's take a look at the other approach now.


#### Imperative Navigation with `useNavigate`

React Router also comes with a `useNavigate` (lines 53 and 56, useNavigate) Hook which gets you access to `navigate` (lines 56 and 61, navigate) - React Router's imperative API for programmatically navigating.

Going back to our example from earlier, if we wanted to use the imperative `navigate` (lines 56 and 61, navigate) method, it would look like this.

```jsx
import { useNavigate } from 'react-router-dom

function Register () {
  const navigate = useNavigate()

  return (
    <div>
      <h1>Register</h1>
      <Form afterSubmit={() => navigate('/dashboard')} />
    </div>
  )
}
```

Easy peasy. Worse, IMO. But, easy peasy.


There are two ways to programmatically navigate with React Router - `<Navigate />` and `navigate()`.

You can get access to `Navigate` by importing it from the `react-router-dom` package and you can get access to `navigate` by using the custom `useNavigate` Hook.

Which you use is mostly up to you and your specific use case, though I try to favor `Navigate` as it's more declarative.


---------------------------------------------------------------------------------

Comments:

- There's an error in the first example, in the afterSubmit - it should invoke `setToDashboard`

- The Navigate component, while I understand the reasoning, still gives me a funny feeling. I think it's because navigating implies an action in my mind. And every action either used effects or was done through javascript code so far.

    - Yeah that's reasonable. The React Router team has slowly changed their minds over the years as well to align more closely with what you described.