### URL Parameters

If you're reading this, you're probably familiar with the idea of function parameters. They allow you to declare placeholders (line 6 - handle) when you define a function that will be set when the function is invoked.

```js
function getProfile (handle) {
  // `handle` is a placeholder
  // for when `getProfile` is invoked
}

getProfile('tylermcginnis')
getProfile('cassidoo')
```

URL parameters solve a similar problem, except instead of declaring placeholders for a function, you can declare placeholders for a URL. In terms of React and React Router, this means what you render can be dynamic based on the "placeholder" portion of the URL.

Take Twitter for example. Instead of defining a route for every user on the platform, they can declare one route with a placeholder of the user's `handle` (line 20 - :handle). The syntax would look something like this,

```jsx
<Route path=':handle' element={<Profile />} />
```

Notice that the path (line 20 - path) has a `:` in front of it. That's how you tell React Router that this portion of the URL is the "placeholder". Instead of matching literally for `twitter.com/handle`, it's matching for the specific pattern.

Now whenever anyone visits a URL that matches the `:handle` (line 20 - :handle) pattern (`/tylermcginnis`, `/cassidoo`, `/anything`) , the `Profile` (line 20 - <Profile />) component.

Now the question becomes, how do you access the dynamic portion of the URL – in this case, `handle` (line 20 - :handle) – in the component that's rendered?

As of v5.1, React Router comes with a `useParams` (line 33 and line 38 - useParams) Hook that returns an object with a mapping between the URL parameter and its value.

```jsx
import * as React from 'react'
import { useParams } from 'react-router-dom'
import { getProfile } from '../utils'

function Profile () {
  const [user, setUser] = React.useState(null)
  const { handle } = useParams()

  React.useEffect(() => {
    getProfile(handle)
      .then(setUser)
  }, [handle])

  return (
    ...
  )
}
```


Now that we have the fundamentals out of the way, let's look at an example where we'd need to use URL parameters in an app – building a blog.

Our blog will be simple. On the `/` page we'll list out and link to all of our blog posts and we'll create a URL parameter for each post at `/blog/:id`.

With only that information, we can already render our `Route`s (lines 62, 78, 82 - Route).

```jsx
import * as React from "react";
import {
  BrowserRouter as Router,
  Route,
  Routes,
} from "react-router-dom";

function Home() {
  return ();
}

function Post() {
  return ();
}

export default function App() {
  return (
    <Router>
      <Routes>
        <Route
          path="/"
          element={<Home />}
        />
        <Route
          path="blog/:id"
          element={<Post />}
        />
      </Routes>
    </Router>
  );
}
```

Next let's build out the `Home` component. As we learned earlier, this component will "list out and link to all of our blog posts". To do this, we'll need a way to get all of the `id`s (lines 113, 114 and 115 - id) and `title`s (lines 113 and 115) for our posts. Because this is a post about URL parameters, let's pretend we already had a helper function to give us this info - `getPosts` (line 103).

```jsx
import * as React from "react";
import {
  BrowserRouter as Router,
  Route,
  Routes,
  Link,
} from "react-router-dom";

import { getPosts } from "./api";

function Home() {
  const posts = getPosts();

  return (
    <div>
      <h1>Posts</h1>
      <nav>
        <ul>
          {posts.map(({ id, title }) => (
            <li key={id}>
              <Link to={`blog/${id}`}>{title}</Link>
            </li>
          ))}
        </ul>
      </nav>
    </div>
  );
}

...
```

The biggest thing to note in the code above is the `<Link>` component. Notice we're linking to `blog/${id}` because that's the pattern (line 130 - blog/:id) that matches our `Route` we created previously -

```jsx
<Route path="blog/:id" element={<Post />} />
```

The final thing we need is to build out our `Post` component that gets rendered when a user visits a URL that matches the `blog/:id` pattern. To do this, we'll need to first, get the `id` of the post the user is visting (via the URL parameter) and second, use that `id` to get the contents of the post.

To get the `id` (lines 150 and 151 - id) of the post (via the URL parameter), we can use React Router's `useParams` (lines 144 and 150 - useParams) Hook. To then get the post's content, we'll pretend we have a `getPost` (lines 147 and 151 - getPost) function we can use.

```js
import * as React from "react";
import {
  BrowserRouter as Router,
  Link,
  Route,
  Routes,
  useParams
} from "react-router-dom";

import { getPost, getPosts } from "./api";

function Post() {
  const { id } = useParams();
  const post = getPost(id);

  return (
    <div>
      <h1>{post.title}</h1>
      <p>{post.content}</p>
    </div>
  );
}
```


To recap, you can think of URL parameters similar to how you think of function parameters. However, instead of creating a placeholder for a function value, you're creating a placeholder for a portion of a URL.

Using React Router, when you want to create a `Route` (line 168 - Route) that uses a URL parameter, you do so by including a `:` (line 168 - :id) in front of the value you pass to `Route`'s (line 168 - Route) `path` (line 168 - path) prop.

```jsx
<Route path=':id' element={<Invoice />} />
```

Finally, to access the value of the URL parameter from inside of the component that is rendered by React Router, you can use React Router's `useParams` (lines 174 and 177 - useParams) Hook.

```jsx
import { useParams } from 'react-router-dom'

export default function Invoice () {
  const { id } = useParams()

  ...
}
```


---------------------------------------------------------------------------------

Comments:

- Hey Tyler - Something I find confusing about this is my mental model for breaking down a URL is as so:
  Query string = the whole chunk after ?
  URL parameters = the key-value pairs inside that chunk
  Path parameters = part of the URL path, not the query string
  So would you agree that in your lesson here, you are defining URL parameters as what I would typically think of as path parameters?
  Thanks for you help!

    - Yeah it seems like there's no consistent naming convention here (or at least, there wasn't when I wrote this post). I usually try to keep the same conventions as the docs, in this case – [these ones](https://v5.reactrouter.com/web/example/url-params) where they say "Params are placeholders in the URL that begin with a colon, like the :id param defined in the route in this example. A similar convention is used for matching dynamic segments in other popular web frameworks like Rails and Express."
