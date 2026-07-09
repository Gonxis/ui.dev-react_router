### (Project) Players

The full code for this video can be found [here](https://github.com/uidotdev/react-router-course/tree/5-players)

The single commit for this video can be found [here](https://github.com/uidotdev/react-router-course/commit/3ed94ea23a4d2525f2e7a5f399df536b84a643f6)

**Note**: I'll be working on this project in a separate repository. This is only intended to keep all the related links referenced in one place.


---------------------------------------------------------------------------------

Comments:

- Following the code suggested by Indika, the `CustomLink` can also be simplified to:

```jsx
function CustomLink({ to, children }) {
    const match = useMatch(to);

    const styles = match
        ? { fontWeight: 900, color: 'var(--white)' }
        : {};

    return (
        <li>
            <Link
                style={{ ...styles }}
                to={to}
            >
                {children}
            </Link>
        </li>
    );
}
```

- @TylerMcGinnis - the issue you mention in the video for which you were unsure the cause of seems to be that you've passed the `useSearchParams()` method an argument of `location.search`. This does not follow from the previous videos, since you just used `useSearchParams()` sans arguments there. Removing this argument allows the Players page to show all players regardless of the previously rendered view, as expected.

- Is is true that at the end of this portion of the project, if you click on a specific player's name on the sidebar, nothing will render under the nav? I assume that we solve that when we get into the nested routes section? (Assuming my understanding is correct) It's unfortunate that we can't validate that we've correctly done the work to style a player's name when it matches the url until the next lesson. I've been spinning my wheels trying to figure out why the body of the page goes blank when I click on a player instead of seeing the updated styling, but then I realized we never actually saw it work for Tyler in the video either.
  
    - If there's no way around that then it would at least be good to mention that it shouldn't be working until the next portion is done.

- I believe the useSearchParams Hook already has access to the url and doesn't need to be passed the search params explicitly. The following seems to work without introducing state or effect:
  ```jsx
  export default function Players() {
    const [searchParams] = useSearchParams();
    const team = searchParams.get('teamId');
    
    const {
        response: names,
        loading
    } = usePlayerNames(team);

    if(loading) {
        return null;
    }

    return (
        <div className="container">
        <Sidebar
            title="Players"
            list={names}
        />
        </div>
    )
  }
  ```

    - I think this brings back to the question, whether useLocation needs to be used along with useSearchParams? I think this whole state thing comes in because useLocation hook being used.
    - `useSearchParams()` has access to the "current" search parameters through calling `useLocation()` internally, but the function also accepts an optional argument for default search parameters. If included, these persist internally with `useRef()` and are always added to the "current" search parameters unless `setSearchParams()` is called, which would then cause any default search parameters to be lost. (Calling `useSearchParams(location.search)` is setting the search parameters available in `location.search` at that moment as default search parameters.)

    The reason why going from `/teams?teamId=hedgehogs` to `/teams` continues to show the same list of players from the hedgehogs team is because of 1) how this code is setup to access a `teamId` search parameter in `const team = searchParams.get("teamId")` (which in this project is then used in a fetch call that only fetches all players if `teamId` is `null` or players of a specific team if `teamId` is one of the team IDs) and 2) the value of `teamId` is still available as `hedgehogs` because default search parameters are always added to the "current" search parameters unless `setSearchParams()` is called. In fact, `location.search` changes to an empty string ("") when clicking from `/players?teamId=hedgehogs` to `/players`, but that default parameter is still being added in.

    So it seems that you do not need to pass in `location.search` in `useSearchParams()` (at least in this case) as `searchParams` is updated whenever `location.search` changes, but it's not updating exactly in the way you would think by adding in those default parameters.
