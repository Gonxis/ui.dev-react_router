### Nested Routes

A solid understanding of how, when, and why to create nested routes is foundational to any developer using React Router. However, in order to help us better answer those questions, there are some topics we need to cover first. Namely, you need to be comfortable with two of React Router's most foundational components – `Route` and `Routes`.

Let's start with `Route`. Put simply, `Route` allows you to map your app's location to different React components. For example, say we wanted to render a `Dashboard` component whenever a user navigated to the `/dashboard` path. To do so, we'd render a `Route` that looked like this.

```jsx
<Route path='/dashboard' element={<Dashboard />} />
```

The mental model I use for `Route` is that it always has to render something – either its `element` prop if the `path` matches the app's current location or `null`, if it doesn't.

> Slow and steady wins the race
I realize we're starting off off slow here, but in doing so we'll set the proper foundation that we can build off of later. Pinky promise.

With `Route` out of the way, lets look at its friend – `Routes`.

```jsx
<Routes>
  <Route path="/" element={<Home />} />
  <Route path="/messages" element={<Messages />} />
  <Route path="/settings" element={<Settings />} />
</Routes>
```

You can think of `Routes` as the metaphorical conductor of your routes. Its job is to understand all of its `children` `Route` elements, and intelligently choose which ones are the best to render. It's also in charge of constructing the appropriate urls for any nested `Link`s and the appropriate `path`s for any nested `Route`s – but more on that in a bit.

Playing off our `<Routes>` above, say not only do we want a `/messages` page, but we also want a page for each individual conversation, `/messages/:id`. There are a few different approaches to accomplish this. Your first idea might be to just create another `Route`.

```jsx
<Routes>
  <Route path="/" element={<Home />} />
  <Route path="/messages" element={<Messages />} />
  <Route path="/messages/:id" element={<Chat />} />
  <Route path="/settings" element={<Settings />} />
</Routes>
```

Assuming the UI for `<Chat>` had nothing to do with `<Messages>`, this would work. However, this is a post about nested routes, not just rendering normal routes.

Typically with nested routes, the parent `Route` acts as a wrapper over the child `Route`. This means that both the parent **and** the child `Route`s get rendered. In our example above, only the child `Route` is being rendered.

![Route Style: Normal](imgs/Screenshot%202026-08-11%20at%2016.20.23.png)
![Route Style: Nested](imgs/Screenshot%202026-08-11%20at%2016.21.00.png)

So to make a **truly** nested route, when we visit a URL that matches the `/messages/:id` pattern, we want to render `Messages` which will then be in charge of rendering `Chat`.


##### Real world example

A real-life example of this UI could look similar to Twitter's [/messages](https://twitter.com/messages) route. When you go to `/messages`, you see all of your previous conversations on the left side of the screen. Then, when you go to `/messages/:id`, you still see all your messages, but you also see your chat history for `:id`.

So how could we adjust our code to do this? Well, what's stopping us from just rendering another `Routes` component inside our `Messages` component? Something like this:

```jsx
// App.js
function App () {
  return (
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/messages" element={<Messages />} />
      <Route path="/settings" element={<Settings />} />
    </Routes>
  )
}
```

```jsx
// Messages.js
function Messages () {
  return (
    <Container>
      <Conversations />

      <Routes>
        <Route path='/messages/:id' element={<Chat />} />
      </Routes>
    </Container>
  )
}
```

Now when the user navigates to `/messages`, React Router renders the `Messages` component. From there, `Messages` shows all our conversations via the `Conversations` (line 73, <Conversations />) component and then renders another `Routes` (lines 75 and 77, Routes) with a `Route` (line 76, Route) that maps /`messages/:id` (line 76, /messages/:id) to the `Chat` (line 76, <Chat />) component.

Looks good, but there's one subtle issue. Can you spot it?

`Messages` only gets rendered when the user is at `/messages`. When they visit a URL that matches the `/messages/:id` pattern, `Messages` no longer matches and therefore, our nested `Routes` never gets rendered.

To fix this, naturally, we need a way to tell React Router that we want to render `Messages` both when the user is at `/messages` or any other location that matches the `/messages/*` pattern.

Wait. What if we just update our `path` to be `/messages/*` (line 99, /messages/*)?

```jsx
// App.js
function App () {
  return (
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/messages/*" element={<Messages />} />
      <Route path="/settings" element={<Settings />} />
    </Routes>
  )
}
```

Much to our delight, that'll work. By appending a `/*` to the end of our `/messages` path, we're essentially telling React Router that `Messages` has a nested `Routes` component and our parent `path` (line 99, path) should match for `/messages` as well as any other location that matches the `/messages/*` pattern. Exactly what we wanted.

There's even one small improvement we can make to our nested `Routes`. Right now inside of our `Messages` component, we're matching for the whole path – `/messages/:id` (line 112, /messages/:id).

```jsx
<Routes>
  <Route path='/messages/:id' element={<Chat />} />
</Routes>
```

This seems a bit redundant. The only way `Messages` gets rendered is if the app's location is already at `/messages`. It would be nice if we could just leave off the `/messages` part all together and have our `path` be **relative** to where it's rendered. Something like this (line 125, :id).

```jsx
function Messages () {
  return (
    <Container>
      <Conversations />

      <Routes>
        <Route path=':id' element={<Chat />} />
      </Routes>
    </Container>
  )
}
```

As you probably guessed, you can do that as well since `Routes` supports relative paths. Notice we also didn't do `/:id`. Leaving off the `/` is what tells React Router we want the `path` to be relative.


At this point, we've looked at how you can create nested routes by appending `/*` to our `Route`'s `path` and rendering, literally, a nested `Routes` component. This works when you want your child `Route` in control of rendering the nested `Routes`, but what if we didn't want that?

Meaning, what if we wanted our `App` component to contain all the information it needed to create our nested routes rather than having to do it inside of `Messages`?

Because this is a common preference, React Router supports this way of creating nested routes as well. Here's what it looks like.

```jsx
function App () {
  return (
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/messages" element={<Messages />}>
        <Route path=':id' element={<Chats />} />
      </Route>
      <Route path="/settings" element={<Settings />} />
    </Routes>
  )
}
```

You [declaratively](https://fireship.dev/c/react/imperative-vs-declarative-programming) nest the child `Route` (line 147, Route) as a `children` of the parent `Route` (lines 146 and 148, Route). Like before, the child `Route` is now relative to the parent, so you don't need to include the parent (`/messages`) path.

Now, the last thing you need to do is tell React Router **where** in the parent `Route` (`Messages`) should it render the child `Route` (`Chats`).

To do this, you use React Router's `Outlet` component.

```jsx
import { Outlet } from 'react-router-dom'

function Messages () {
  return (
    <Container>
      <Conversations />

      <Outlet />
    </Container>
  )
}
```

If the app's location matches the nested `Route`'s `path`, this `Outlet` component will render the `Route`'s `element`. So based on our `Routes` above, if we were at `/messages`, the `Outlet` component would render `null`, but if we were at `/messages/1`, it would render the `<Chats />` component.

#### Cool... but which one is better?
Opinion Time: Though there's no objective benefit to one approach over the other, I'd probably favor using the latter approach with `<Outlet />` over the former nested `Routes` approach as it feels a little cleaner, IMO.


At this point, there's nothing new about nested routes with React Router that you need to learn. However, it may be beneficial to see it used in a real app.

Here's what we'll be building. As you navigate around, keep an eye on the navbar. You'll notice that we have the following URL structure.

```text
/
/topics
  :topicId
    :resourceId
```

Now before we get started, let's get a few housekeeping items out of the way first.

We'll have an "API" which is responsible for getting us our data. It has three methods we can use, `getTopics` (line 198, getTopics), `getTopic` (line 202, getTopic), and `getResource` (line 206, getResource).

```js
/src/api.js
export function getTopics() {
  return topics;
}

export function getTopic(topicId) {
  return topics.find(({ id }) => id === topicId);
}

export function getResource({ resourceId, topicId }) {
  return topics
    .find(({ id }) => id === topicId)
    .resources.find(({ id }) => id === resourceId);
}
```

If you'd like to see what `topics` (lines 199, 203 and 207, topics) looks like, you can do so [here](https://codesandbox.io/s/nested-routes--vln9x?file=/src/api.js:6-12) - spoiler alert, it's just an array of objects which maps closely to our routes.

Next, our `Home` component for when the user is at the `/` route. Nothing fancy here either.

```js
/src/App.js:193-430
function Home() {
  return (
    <React.Fragment>
      <h1>Home</h1>
      <p>
        Welcome to our content index.
        Head over to{" "}
        <Link to="/topics">
          /topics
        </Link> to see our catalog.
      </p>
    </React.Fragment>
  );
}
```

#### Por qué no los dos?
Because we've seen both patterns for creating nested routes, let's see them both in our example as well. We'll start out with the nested `Routes` pattern, then we'll refactor to use the `<Outlet />` pattern.

Next, we'll build out our top level `App` component which will have our main navbar as well as `Route`s for `/` and `/topics`.

Looking at our final app, we know that `/` is going to map to our `Home` component and `/topics` is going to map to a component that shows our top level topics (which we can get from calling `getTopics`).

We'll name this component `Topics` and since it'll contain a nested `Routes`, we'll make sure to append `/*` (line 267, /topics/*) to the parent `path` (line 267, path).

```js
/src/App.js:1554-1984
function Topics () {
  return null
}

export default function App() {
  return (
    <Router>
      <div>
        <ul>
          <li>
            <Link to="/">Home</Link>
          </li>
          <li>
            <Link to="/topics">Topics</Link>
          </li>
        </ul>

        <hr />

        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/topics/*" element={<Topics />} />
        </Routes>
      </div>
    </Router>
  );
}
```

Now we need to build out the `Topics` component. As I just mentioned, `Topics` needs to show (line 288, map) our top level `topics` (lines 282 and 288, topics) which it can get from `getTopics` (lines 279 and 282, getTopics). Let's do that before we worry about its nested routes.

```jsx
import { Link } from 'react-router-dom'
import { getTopics } from './api'

function Topics() {
  const topics = getTopics();

  return (
    <div>
      <h1>Topics</h1>
      <ul>
        {topics.map(({ name, id }) => (
          <li key={id}>
            <Link to={id}>{name}</Link>
          </li>
        ))}
      </ul>

      <hr />
    </div>
  );
}
```

Notice that because we're using nested routes, our `Link` (lines 278 and 290, Link) is relative to the location it's rendered – meaning we can just do `to={id}` rather than having to do `to={'/topics/${id}'}`

Now that we know we're linking `to={id}` (which is really `/topics/react`, `/topics/typescript`, or `/topics/react-router`), we need to render a nested `Route` that matches that same pattern.

We'll call the component that gets rendered at the route `Topic` and we'll build it out in the next step.

The only thing we need to remember about `Topic` is it's going to also render a nested `Routes`, which means we need to append `/*` to the `Route`'s `path` (line , :topicId/*) we render in `Topics`.

```js
/src/App.js:1184-1552
function Topic () {
  return null
}

function Topics() {
  const topics = getTopics();

  return (
    <div>
      <h1>Topics</h1>
      <ul>
        {topics.map(({ name, id }) => (
          <li key={id}>
            <Link to={id}>{name}</Link>
          </li>
        ))}
      </ul>

      <hr />

      <Routes>
        <Route path=":topicId/*" element={<Topic />} />
      </Routes>
    </div>
  );
}
```

We're one level deeper and a pattern is starting to emerge.

Let's build out our `Topic` component now. `Topic` will show the topic's `name` (line 353, name), `description` (line 354, description), and then link its `resources` (line 357, resources). We can get the topic by passing our `topicId` (lines 348 and 349, topicId) [URL parameter](https://fireship.dev/c/react-router/'react-router/react-router-url-parameters') we set up in the previous step to `getTopic` (lines 345 and 349, getTopic).

```jsx
import { useParams } from 'react-router-dom'
import { getTopic } from './api'

function Topic() {
  const { topicId } = useParams();
  const topic = getTopic(topicId);

  return (
    <div>
      <h2>{topic.name}</h2>
      <p>{topic.description}</p>

      <ul>
        {topic.resources.map((sub) => (
          <li key={sub.id}>
            <Link to={sub.id}>{sub.name}</Link>
          </li>
        ))}
      </ul>

      <hr />
    </div>
  );
}
```

Notice that even though we're a few layers deep, our nested `Link`s are still smart enough to know the current location so we can just link `to={sub.id}` (line 359, to) rather than `to={/topics/${topicId}/${sub.id}}`

We're almost there. Now we need to render our last nested `Routes` that matches the pattern we just saw. Again, because `Routes` is smart and supports relative paths, we don't need to include the whole `/topics/:topicId/` path.

```js
/src/App.js:719-1182
function Resource () {
  return null
}

function Topic() {
  const { topicId } = useParams();
  const topic = getTopic(topicId);

  return (
    <div>
      <h2>{topic.name}</h2>
      <p>{topic.description}</p>

      <ul>
        {topic.resources.map((sub) => (
          <li key={sub.id}>
            <Link to={sub.id}>{sub.name}</Link>
          </li>
        ))}
      </ul>

      <hr />

      <Routes>
        <Route path=":resourceId" element={<Resource />} />
      </Routes>
    </div>
  );
}
```

Finally, we need to build out the `Resource` component. We're all done with nesting, so this component is as simple as grabbing our `topicId` (lines 412 and 418,topicId) and `resourceId` (lines 412 and 418, resourceId) URL parameters, using those to grab the `resource` (lines 415-417 and 422-423, name, description, id) from `getResource` (line 418, getResource), and rendering some simple UI.

```js
/src/App.js:432-717
function Resource() {
  const { topicId, resourceId } = useParams();

  const {
    name,
    description,
    id
  } = getResource({ topicId, resourceId });

  return (
    <div>
      <h3>{name}</h3>
      <p>{description}</p>
      <a href={`https://ui.dev/${id}`}>Read Post</a>
    </div>
  );
}
```

Well, that was fun. You can find all the [final code here](https://codesandbox.io/s/nested-routes--vln9x?file=/src/App.js).


Now, let's throw all that out the window and refactor our app using the `Outlet` component. First, instead of having nested `Routes` sprinkled throughout our app, we'll put all of them inside of our `App` component.

```js
/src/App.js:1420-2008
export default function App() {
  return (
    <Router>
      <div>
        <ul>
          <li><Link to="/">Home</Link></li>
          <li><Link to="/topics">Topics</Link></li>
        </ul>

        <hr />

        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/topics" element={<Topics />}>
            <Route path=":topicId" element={<Topic />}>
              <Route
                path=":resourceId"
                element={<Resource />}
              />
            </Route>
          </Route>
        </Routes>
      </div>
    </Router>
  );
}
```

Now, we need to swap out the nested `Routes` inside of `Topics` and `Topic` for the `<Outlet />` component.

```js
/src/App.js:729-1418
function Topic() {
  const { topicId } = useParams();
  const topic = getTopic(topicId);

  return (
    <div>
      <h2>{topic.name}</h2>
      <p>{topic.description}</p>

      <ul>
        {topic.resources.map((sub) => (
          <li key={sub.id}>
            <Link to={sub.id}>{sub.name}</Link>
          </li>
        ))}
      </ul>

      <hr />

      <Outlet />
    </div>
  );
}

function Topics() {
  const topics = getTopics();

  return (
    <div>
      <h1>Topics</h1>
      <ul>
        {topics.map(({ name, id }) => (
          <li key={id}>
            <Link to={id}>{name}</Link>
          </li>
        ))}
      </ul>

      <hr />

      <Outlet />
    </div>
  );
}
```

And with that, we're done. You can find the final code for using `<Outlet>` [here](https://codesandbox.io/s/nested-routes-outlet-lpllv?file=/src/App.js).


To recap, nested routes allow you to, at the route level, have a parent component control the rendering of a child component. Twitter's [/messages](https://twitter.com/messages) route is the perfect example of this.

With React Router, you have two options for creating nested routes. The first is using the `/*` with nested `<Routes>` pattern and the second is using the `<Outlet />` pattern.
