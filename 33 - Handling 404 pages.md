### Handling 404 Pages

React Router is all about mapping URL paths to React components. However, sometimes you want React Router to render a component when **none** of the `Route`s match.

The most common use case for this is showing a 404 page. Regardless of if you want to show off your [creative side](https://ui.dev/nopenope) or not, having a 404 page in place is a small thing that will go a long way for the UX of your site. Luckily, this is pretty simple to do with React Router's `Routes` component.

`Routes` is the powerhouse of React Router. Whenever the app's location changes, any `Routes` (lines 10 and 14, Routes) component will look through all its children `Route` (lines 11-13, Route) elements to find the best match to render.

```jsx
<Routes>
  <Route path="/" element={<Home />} />
  <Route path="/about" element={<About />} />
  <Route path="/settings" element={<Settings />} />
</Routes>
```

Unlike previous versions of React Router, the order of the children `Route`s doesn't matter since `Routes` is intelligent – meaning an algorithm now determines which is the best `Route` to render. This makes rendering a 404 component pretty simple.

All you have to do is render a `Route` (line 23, Route) with a `path` (line 23, path) of `*` (line 23, *), and React Router will make sure to only render the `element` (line 23, element) if none of the other `Route`s match.

```jsx
<Routes>
  <Route path='*' element={<NotFound />} />
  <Route path="/" element={<Home />} />
  <Route path="/about" element={<About />} />
  <Route path="/settings" element={<Settings />} />
</Routes>
```
