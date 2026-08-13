### Passing Props To Link

Here's a scenario I want you to think through. Say you were in charge of building out the onboarding flow for your app. Unlike a generic onboarding flow, this one's a little more personalized. The reason for that is because each step is going to change based on the result of the previous step.

For example, say the first step was to get a user's occupation and years experience. From there, we take them to the next step which, based on their occupation and experience, collected more relevant information. How would you go about solving this?

There are a few different approaches, but the one we'll focus on in this post is centered around being able to pass data from one route to another. To do this with React Router, we can pass data from the `Link` component through to the new `Route` that is being rendered.

Now the only question is, how? You're probably familiar with the most common way to use `Link`. It looks something like this,

```jsx
<Link to={`/onboarding/profile`}>
  Next Step
</Link>
```

When the user clicks on `Next Step`, they'll be taken to `/onboarding/profile`. But as we talked about above, we also want to be able to include what route they're coming from.

To do this (and to pass along any other data as well), all you have to do is include a `state` (line 24, state) prop with the data you want to pass along – in our case, where the user is coming `from` (line 24, from).

```jsx
<Link
  to='/onboarding/profile'
  state={{ from: 'occupation' }}
>
  Next Step
</Link>
```

Now the only question that remains is, how do we get access to the data on `state` so we can update the UI based on it? To answer that, we need to look at the component that's being rendered at the `/onboarding/profile` route. For our example, let's call it `Profile`.

```js
import { useLocation } from 'react-router-dom'

function Profile () {
  const location = useLocation()
  const { from } = location.state

  return (
    ...
  )
}
```

Anytime you pass data along via the `state` property, that data will be available on the `location`'s (lines 36 and 37, location) `state` (line 37, state) property, which you can get access to by using the custom `useLocation` (lines 33 and 36, useLocation) Hook that comes with React Router.

Also, if the user were to use their browser's "Back" and "Forward" buttons to navigate through their route history, the state that we passed in would remain intact. That way, if they changed their mind about one of their selections, they can click "Back" without losing any of the state from their previous choices.


To recap, if you need to pass data from `Link` through to the new component that's being rendered, pass `Link`s a `state` prop with the data you want to pass through.

Then, to access the `Link`s `state` property from the component that's being rendered, use the `useLocation` Hook to get access to `location.state`.
