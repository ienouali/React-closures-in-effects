![image](https://github.com/ienouali/react-closures-in-effects/assets/54776723/a58d009b-c993-429b-946b-e1b6311a94a9)

# React: Closures in effects


## Overview
Why useEffect actually need a dependency array in order to know when it should execute the effect? why can’t this effect rerun automatically whenever it should? To analyze this issue, we need to talk about closures.
Closures in Javascript
the fact that a function captures all the variables from its lexical scope. so from the place that it was defined at the time that the function was created.
whenever a function is created, it closes over the effect of that lexical ENV at the time. and it’ll always have access to the variables from the place where it was defined.

## React hooks: useEffect
in react hooks rely heavily on this concept of javascript closures, and this is the case for useEffect.

Example

```javascript
import React, { useState, useEffect } from 'react';

const YourComponent = () => {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `count value is: ${count}`;
  }, []);

  return (
    // Your component JSX here
    <div>
      <p>Count: {count}</p>
      {/* Other JSX elements */}
    </div>
  );
};

export default YourComponent;
```

now, this function was first created, it closed over the variable environment that was present at the time that this function was created. that was on the initial render.
So its closure was created here at the time that this first render was created. and it closed over the props and the state in the case of react.

![image](https://github.com/ienouali/react-closures-in-effects/assets/54776723/56ad7812-47b4-47d4-a266-bfb0c8c492b0)

and in react, we can actually also call this current state and the current props a snapshot.
And so any function that was created at the initial render and then not recreated still has access to that initial snapshot of state and props.
And that’s exactly what we have right here. (useEffect runs only on the first render and then will not rerun. our function in the useEffect was created at the first render but then never again (still remember the initial snapshot).
And so therefore here we now have a closure with that initial snapshot and with the dependency array that we have right now, which is basically here the empty dep array as this default, this function will actually never be recreated again. and will never get access to the new current snapshot,

```javascript
const [count, setCount] = useState(0)

useEffect(function() {
	document.title = `count value is : ${count}`
}, [])


function increment() {
	setState(c => c + 1)
}
```

For example, as we change the count state, as a click handler using the ‘increment’ function
the setState triggered a new render and the count value did not change and the document title value was still the same as before in the document title.

![image](https://github.com/ienouali/react-closures-in-effects/assets/54776723/25adc9bf-f6f1-43a1-916b-79d9a3ee4b18)


## How to avoid it
Essentially The effect function cannot be recreated on every render unless we explicitly list the required states in the dependency array.
let’s do that in this example

![image](https://github.com/ienouali/react-closures-in-effects/assets/54776723/cbf32bff-23e7-4e6d-84b4-0b45394b3e99)

After the re-render, React compares the references passed on in the dependency array between renders. If the reference has changed React assumes that the data may have changed and reruns the effect, which makes the function capture a new snapshot
At this point, our function in the effect will always get access to the latest snapshot that it is interested in.

## Note
if any of the other new state variables within the function effect out here change and not in the dependency array, then this function will still not get access to them. The stale closure only happens if this function is still referencing some old values that are outdated by the time that this function is running.


 #
 ##### Imad Eddine NOUALI
 ###### Frontend Engineer | React Specialist.
