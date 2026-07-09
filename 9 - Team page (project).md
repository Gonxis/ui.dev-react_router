### (Project) Team Page

The full code for this video can be found [here](https://github.com/uidotdev/react-router-course/tree/e2acc2ae314ef280f36713bfb9302e606e38567c)

The single commit for this video can be found [here](https://github.com/uidotdev/react-router-course/commit/4-teampage)

**Note**: I'll be working on this project in a separate repository. This is only intended to keep all the related links referenced in one place.


---------------------------------------------------------------------------------

Comments:

- The useLocation on Players page is redundant , right? 'cause useSearchParams already does the work of getting the location.search for us.

    - I have the same question
    - Same question too 👀
    - Yes, Raphael is right.

        - `useSearchParams()`: This hook returns an object representing the query parameters of the current URL. It doesn't need `useLocation()` because it directly uses the location information provided by `react-router-dom` internally.

        - `useLocation()`: This hook returns the current location object, which includes the `pathname`, `search` (query string), `hash`, and `state`. In this case, you're using `location.search` to get the query string, but `useSearchParams()` already handles that for you.

        So the code without redundancy should be:

        ```js
        import { useSearchParams } from "react-router-dom"

        export default function Players() {
            const [searchParams] = useSearchParams();
            const team = searchParams.get('teamId');

            return (
                <div className="container">
                    Players for team {team}
                </div>
            );
        }
        ```

- I am getting below error when following the code

    Access to fetch at 'https://api.ui.dev/hash-history-basketball-league/articles' from origin 'http://localhost:3000' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.

    Can someone help with this?

    Thanks!

    - weird, I've tried and didnt have this problem, are you fetching using the right method? Which is POST and using the right payload? for example:

        ```js
        {
            "team": "hedgehogs"
        }
        ```

        ??
