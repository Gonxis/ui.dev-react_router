### Recursive Paths

To understand recursion, one must first <s>understand recursion</s> experience months of pain and confusion. The same may be true for understanding recursive routes – though hopefully this post can take the edge off.

It may seem impractical, but having the ability to render recursive routes will serve as both a solid exercise to solidify your understanding of React Router as well as give you the ability to solve potentially tricky UI problems down the road. When would you ever want to render recursive routes? Well, like porn, you'll know it when you see it.

#### Pre-reqs
This is an advanced post. Before you read this, make sure you're familiar with [URL Parameters](https://fireship.dev/c/react-router/'/react-router/react-router-url-parameters') and [Nested Routes with React Router](https://fireship.dev/c/react-router/react-router-nested-routes) before continuing.

The main idea here is that since React Router is just components, theoretically, you can create recursive, and therefore infinite, routes. The secret here lies in setting up the correct data structure. In this example, we'll use an array of `users` (line 13, users) who all have an `id` (lines 14-17, id), a `name` (lines 14-17, name), and an array of `friends` (lines 14-17, friends).

```js
const users = [
  { id: 0, name: 'Michelle', friends: [1, 2, 3] },
  { id: 1, name: 'Sean', friends: [0, 3] },
  { id: 2, name: 'Kim', friends: [0, 1, 3], },
  { id: 3, name: 'David', friends: [1, 2] }
]
```

By having our data structure set up this way, when we render a `Person`, we'll render all of their friends as `Link`s. Then, when a `Link` is clicked, we'll render all of that person's friends as `Link`s - then it's turtles all the way down. 🐢

Each time a `Link` is clicked, the app's pathname will become progressively longer.

Here's how it'll look. Initially, we'll be at `/` and the UI will look like this

```text
Michelle's Friends

  * Sean
  * Kim
  * David
```

If `Kim` is clicked, then the URL will change to `/2` (Kim's `id`) and the UI will look like this

```text
Michelle's Friends

  * Sean
  * Kim
  * David

Kim's Friends

  * Michelle
  * Sean
  * David
```

If `David` is clicked, then the URL will change to `/2/3` (Kim's `id` then David's `id`) and the UI will look like this

```text
Michelle's Friends

  * Sean
  * Kim
  * David

Kim's Friends

  * Michelle
  * Sean
  * David

David's Friends

  * Sean
  * Kim
```

And this process repeats for as long as the user wants to click on `Link`s.

Now that we have the right data structure and mental model in place, the next thing to do it construct our initial `Route`s. As we just saw, we want the main kickoff point of our app to be `/:id`. The component that's going to be rendered at that path (and eventually do all the heavy lifting of creating our nested `Route`s and `Link`s) is our `Person` component.

```jsx
import {
  BrowserRouter as Router,
  Routes,
  Route,
  Link
} from 'react-router-dom'

const users = [
  { id: 0, name: 'Michelle', friends: [1, 2, 3] },
  { id: 1, name: 'Sean', friends: [0, 3] },
  { id: 2, name: 'Kim', friends: [0, 1, 3], },
  { id: 3, name: 'David', friends: [1, 2] }
]

const Person = () => {
  return (
    <div>
      PERSON
    </div>
  )
}

export default function App() {
  return (
    <Router>
      <Routes>
        <Route path="/:id" element={<Person />} />
      </Routes>
    </Router>
  )
}
```

Now, before we continue with our `Person` component, let's make one small addition. As we just saw, the main kickoff point of our app is `/:id`. This is what we want, but it's a little strange to have nothing at the main index route, `/`. Let's set up a simple `redirect` (lines 115 and 123, Navigate) so if the user visits `/`, they'll be taken to `/0`.

```jsx
import {
  ...
  Navigate
  ...
} from 'react-router-dom'

export default function App() {
  return (
    <Router>
      <Routes>
        <Route path="/" element={<Navigate to="/0" />} />
        <Route path="/:id" element={<Person />} />
      </Routes>
    </Router>
  )
}
```

Now comes the fun part, implementing our `Person` component.

Remember, there are a few things this component needs to be responsible for.

1. Using the `id` URL parameter, it needs to find that specific person in the `users` array.
2. It should render a `Link` for every one of that specific person's friends.
3. It should render a `Route` which will match for the current pathname + `/:id`.

Let's tackle #1. We know the `id` (lines 149 and 150, id) of the person we need to grab because of the URL parameter. Next, using that `id`, we can use `Array.find` (line 150, find) to grab the person out of the `users` (line 150, users) array.

```jsx
import {
  ...
  useParams
  ...
} from 'react-router-dom'

const Person = () => {
  const { id } = useParams()
  const person = users.find((p) => p.id === Number(id))

  return (
    <div>
      PERSON
    </div>
  )
}
```

Next up we need to `map` (line 171, map) over the `person`'s (lines 165, 169 and 171, person) `friends` (line 171, friends) and create a `Link` (lines 173 and 175, Link) for each one of them. Because React Router supports relative `Link`s, we don't need to do anything fancy here, just leave off the `/` so React Router knows to append `id` (line 173, id) to the current URL.

```jsx
const Person = () => {
  const { id } = useParams()
  const person = users.find((p) => p.id === Number(id))

  return (
    <div>
      <h3>{person.name}’s Friends</h3>
      <ul>
        {person.friends.map((id) => (
          <li key={id}>
            <Link to={id}>
              {users.find((p) => p.id === id).name}
            </Link>
          </li>
        ))}
      </ul>
    </div>
  )
}
```

Finally, as stated in #3, we need to render a nested `Route` (lines 204-206, Routes and Route) to match the pattern of our newly created `Link`s (lines 197 and 199, Link). Similar to what we did with our nested `Link`, we'll leave off the beginning `/` (line 205, :id) so React Router knows we want our `Route` to be relative.

```jsx
const Person = () => {
  const { id } = useParams()
  const person = users.find((p) => p.id === Number(id))

  return (
    <div>
      <h3>{person.name}’s Friends</h3>
      <ul>
        {person.friends.map((id) => (
          <li key={id}>
            <Link to={id}>
              {users.find((p) => p.id === id).name}
            </Link>
          </li>
        ))}
      </ul>

      <Routes>
        <Route path={`:id`} element={<Person />} />
      </Routes>
    </div>
  )
}
```

At this point, we're **very** close to being done. However, if you try to run our app as is, you'll notice it doesn't work.

There are two important changes we need to make to our code, and they both have to do with how React Router handles nested routes. Whenever you render a `Route` that is going to have a nested `Routes` somewhere in its descendant tree, you need to append `/*` (lines 238 and 249, :id/* and /:id/*) to the URL to tell React Router to build upon the current path.

We'll need to make this change in both areas where we render a `Route` (lines 238 and 249, Route).

```js
/src/App.js
const Person = () => {
  const { id } = useParams()
  const person = users.find((p) => p.id === Number(id))

  return (
    <div>
      <h3>{person.name}’s Friends</h3>
      <ul>
        {person.friends.map((id) => (
          <li key={id}>
            <Link to={id}>
              {users.find((p) => p.id === id).name}
            </Link>
          </li>
        ))}
      </ul>

      <Routes>
        <Route path={`:id/*`} element={<Person />} />
      </Routes>
    </div>
  )
}

export default function App() {
  return (
    <Router>
      <Routes>
        <Route path="/" element={<Navigate to="/0" />} />
        <Route path="/:id/*" element={<Person />} />
      </Routes>
    </Router>
  )
}
```

That's it. `Person` renders a list of `Link`s as well as a `Route` matching any of those `Link`s. When a `Link` is clicked, the `Route` matches which renders another `Person` component which renders a list of `Link`s and another `Route`. This process continues as long as the user continues to click on any `Link`s.
