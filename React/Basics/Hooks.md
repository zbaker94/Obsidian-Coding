_Hooks_ let you use different React features from your components. You can either use the built-in Hooks or combine them to build your own. [This](https://react.dev/reference/react) page lists all built-in Hooks in React.

Hooks take the place of lifecycle methods for [[Functional Components]]. Additionally, hooks also "hook" into various react functionality such as creating [[refs]] and [[memoization]].

Developers may also create their own hooks. A custom hook must be named following the pattern "useXXXX" and must call another hook (custom or otherwise).

``` js
import { useState, useEffect } from "react";

  

function useDebounceValue(value, delay) {

    // State and setters for debounced value

    const [debouncedValue, setDebouncedValue] = useState(value);

    useEffect(

        () => {

            // Update debounced value after delay

            const handler = setTimeout(() => {

                setDebouncedValue(value);

            }, delay);

            // Cancel the timeout if value changes (also on delay change or unmount)

            // This is how we prevent debounced value from updating if value is changed ...

            // .. within the delay period. Timeout gets cleared and restarted.

            return () => {

                clearTimeout(handler);

            };

        },

        [value, delay] // Only re-call effect if value or delay changes

    );

    return debouncedValue;

}

  

export default useDebounceValue;
```

Notice the naming and that this hook calls the built-in ```usestate()``` hook.


### References: