### Query Strings

If you've ever [jammed with the console cowboys in cyberspace](https://www.youtube.com/watch?v=BNtcWpY4YLY), you've almost certainly run into query strings before. They're the `?` and `&` you see appended onto URLs.

> Query String Example
twitter.com/search?q=ui.dev&src=typed_query&f=live

They're a fundamental aspect of how the Web works as they allow you to pass state via the URL. Above is an example of a query string you'd see if you searched for `ui.dev` on Twitter.

The query portion of this URL has three keys, `q`, `src`, and `f`. `q` represents the text we type into Twitter's search bar, `src` tells Twitter how we did it (via typing into the search bar), and `f` filters the results of the query by "Latest".

What's nice about this is it's sharable. You could copy and paste that URL into your browser right now and it would work. All the data Twitter needs to properly render the UI is right there in the URL.

With all that said, odds are you're not here to learn **what** query strings are but instead **how** to use them with React Router. The good news is that if you're already comfortable with React Router, there are just a few small details you need to know.

Let's say we were Twitter and we were building the `Route` for the URL above. It would probably look something like this.

```jsx
<Route path='/search' element={<Results />} />
```

Notice at this point there's nothing new. We don't account for the query string when we create the `Route`. Instead, we get and parse it inside the component that is being rendered when that path matches - in this case, `Results`.

Now the question becomes, how do we actually do this? Before we can answer that question, we first need to learn about the `URLSearchParams` API.


#### `URLSearchParams`

The [URLSearchParams](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams) API is built into all browsers (except for IE) and gives you utility methods for dealing with query strings.

When you create a new instance of URLSearchParams (line 37 - new URLSearchParams), you pass it a query string (lines 36 and 37 - queryString) and what you get back is on object with a bunch of methods (lines 39-51 - has, get, getAll, append, toString, set, delete) for working with that query string.

Take our Twitter query string for example,

```js
const queryString = "?q=ui.dev&src=typed_query&f=live";
const sp = new URLSearchParams(queryString);

sp.has("q"); // true
sp.get("q"); // ui.dev
sp.getAll("src"); // ["typed_query"]
sp.get("nope"); // null

sp.append("sort", "ascending");
sp.toString(); // "?q=ui.dev&src=typed_query&f=live&sort=ascending"

sp.set("q", "bytes.dev");
sp.toString(); // "?q=bytes.dev&src=typed_query&f=live&sort=ascending"

sp.delete("sort");
sp.toString(); // "?q=bytes.dev&src=typed_query&f=live"
```


#### `useSearchParams`

As of v6, React Router comes with a custom `useSearchParams` Hook which is a small wrapper over the browser's `URLSearchParams` API.

`useSearchParams` returns an array with the first element being an instance of `URLSearchParams` (with all the properties we saw above) and the second element being a way to update the query string.

Going back to our example, here's how we would get (lines 69-71 - get) the values from our query string using `useSearchParams` (lines 64 and 67 - useSearchParams).

```jsx
import { useSearchParams } from 'react-router-dom'

const Results = () => {
  const [searchParams, setSearchParams] = useSearchParams();

  const q = searchParams.get('q')
  const src = searchParams.get('src')
  const f = searchParams.get('f')

  return (
    ...
  )
}
```

Then if we needed to update the query string, we could use `setSearchParams` (lines 85 and 92 - setSearchParams), passing it an object whose key/value pair will be added to the url as `&key=value`.

```jsx
import { useSearchParams } from 'react-router-dom'

const Results = () => {
  const [searchParams, setSearchParams] = useSearchParams();

  const q = searchParams.get('q')
  const src = searchParams.get('src')
  const f = searchParams.get('f')

  const updateOrder = (sort) => {
    setSearchParams({ sort })
  }

  return (
    ...
  )
}
```


---------------------------------------------------------------------------------

Comments:

- Hello, could you elaborate more on how to set query strings. It was briefly shown "setSearchParams({sort})" but I couldn't make out an understanding. Thank you.

    - It basically was going to add in the queryString something like: &sort=something. You send an object { key: value } and it becomes &key=value.

- Sorry what is the use case for query string in a react application. I have researched online and I am struggling to find one. Thank you.

    - Honestly the best option for understanding query strings is just to browse popular websites and watch how they implement them in the URL.
    - Thanks so much. Please do you have any or know references I can check out?
    - Wondering if a bookmarked google search is an example of a good use case. The search parameters are in the query string. Your search results can be reproduced again by google when you access the bookmarked url because the search parameters are in the query string of the url.

        https://reactrouter.com/en/main/hooks/use-search-params At this link there is a react example of an app that has a form that sets the search params on submit. You can imagine this app could be modified to show the search results under the form based on the url search params. If a user bookmarked the url the user could return to the page with the search results displayed in the results section

    - They are extensions of the URL that are used to help define specific content or actions based on the data being passed. Example of personalized greeting:

    // Contact.js import { useLocation } from "react-router-dom"

    export default function Contact() { const queryString = useLocation().search // console.log(queryString)

    const queryParams = new URLSearchParams(queryString) const name = queryParams.get("name")

    return ( <div> <h2>Hey {name}, Contact Us</h2> <p>Lorem ipsum, dolor sit amet consectetur adipisicing elit. Ipsam explicabo laudantium nemo voluptas cum omnis error voluptate. Nihil numquam ipsum necessitatibus hic odit neque consequuntur dolor. Magni quos ratione iste.</p> </div> ) }
