### (Project) Loading

The full code for this video can be found [here](https://github.com/uidotdev/react-router-course/tree/9-loading)

The single commit for this video can be found [here](https://github.com/uidotdev/react-router-course/commit/50b10b58615e3c2a352867ae169a7bd734d9b73e)

**Note**: I'll be working on this project in a separate repository. This is only intended to keep all the related links referenced in one place.


---------------------------------------------------------------------------------

Comments:

- I don't understand how the re-render happens after loading. If loading is initially true, and the component shows the loader(after 500ms) - that's fine. But the article/player component doesn't have any logic to re-render when response has actually come back. How then does the loading disappear and the actual response from the API appear on the UI?

     - This is because we are update the state with the useState hook. The React documentation says: "The set function that lets you update the state to a different value and trigger a re-render." https://react.dev/reference/react/useState
