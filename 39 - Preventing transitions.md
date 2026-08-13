### Preventing Transitions

As of today, React Router v6 [doesn't ship with support](https://github.com/remix-run/react-router/issues/8139#issuecomment-954425560) for preventing transitions. Once [this issue](https://github.com/remix-run/react-router/issues/8139) is resolved, we'll update this post with the recommended way to prevent transitions in your app.

For now, here's a _very hacky not "approved"_ approach that "works".

```js
/src/usePropmt.js:77-853
import * as React from "react";
import {
  UNSAFE_NavigationContext as NavigationContext
} from "react-router-dom";

export function useBlocker(blocker, when = true) {
  const { navigator } = React.useContext(NavigationContext);

  React.useEffect(() => {
    if (!when) return;

    const unblock = navigator.block((tx) => {
      const autoUnblockingTx = {
        ...tx,
        retry() {
          unblock();
          tx.retry();
        }
      };

      blocker(autoUnblockingTx);
    });

    return unblock;
  }, [navigator, blocker, when]);
}

export default function usePrompt(message, when = true) {
  const blocker = React.useCallback(
    (tx) => {
      if (window.confirm(message)) tx.retry();
    },
    [message]
  );

  useBlocker(blocker, when);
}
```

Now you can `usePrompt` in your app. `usePrompt` receives two arguments – `when` and `message`. `when` is a boolean that if `true`, will show the user a prompt with the `message` when they try to navigate away.

```js
/src/App.js
function Form() {
  const [name, setName] = React.useState("");
  const [email, setEmail] = React.useState("");
  const [note, setNote] = React.useState("");

  const isDirty = () => {
    return name.length > 0
      || email.length > 0
      || note.length > 0;
  };

  usePrompt(
    "Are you sure you want to leave?",
    isDirty()
  )

  return (
    <form
      onSubmit={(e) => {
        e.preventDefault();
        alert("Submitted!");
        setName("");
        setEmail("");
        setNote("");
      }}
    >
      ...
    </form>
  );
}
```

[Here's a Codesandbox](https://codesandbox.io/s/preventing-transitions-hack-714le?file=/src/App.js) with the above implementation.


---------------------------------------------------------------------------------

Comments:

- UNSAFE_NavigationContext.block does not exist anymore. We need another workaround here. Is there really no official way to handle transitions like that anymore?

    - I asked ChatGPT and it suggested me:

    ```js
    import React from "react";
    import { useNavigate } from "react-router-dom";

    function useUnsavedChangesWarning(isDirty) {
        React.useEffect(() => {
            const handleBeforeUnload = (event) => {
            if (isDirty) {
                const message = "You have unsaved changes. Are you sure you want to leave?";
                event.returnValue = message;
                return message;
            }
            };

            window.addEventListener("beforeunload", handleBeforeUnload);

            return () => {
            window.removeEventListener("beforeunload", handleBeforeUnload);
            };
        }, [isDirty]);
        }

        export default function Form() {
        const [name, setName] = React.useState("");
        const [email, setEmail] = React.useState("");
        const [note, setNote] = React.useState("");
        const navigate = useNavigate();

        const isDirty = name || email || note;

        useUnsavedChangesWarning(isDirty);

        const handleNavigation = () => {
            if (isDirty) {
            const confirmLeave = window.confirm("You have unsaved changes. Are you sure you want to leave?");
            if (!confirmLeave) return;
            }
            navigate("/some-other-route");
        };

        return (
            <form
            onSubmit={(e) => {
                e.preventDefault();
                alert("Submitted!");
                setName("");
                setEmail("");
                setNote("");
            }}
            >
            <input value={name} onChange={(e) => setName(e.target.value)} placeholder="Name" />
            <input value={email} onChange={(e) => setEmail(e.target.value)} placeholder="Email" />
            <textarea value={note} onChange={(e) => setNote(e.target.value)} placeholder="Note" />
            <button type="submit">Submit</button>
            <button type="button" onClick={handleNavigation}>Navigate Away</button>
            </form>
        );
    }
    ```

    Anyone has any other approach for this problem?


    - use Tanstack router's useBlocker hook

    - If you're using React Router 6.4+, there's a new Data Router which has a useBlocker hook.
