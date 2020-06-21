---
title: "React: Subscribe to events and debounce with RxJS"
draft: false
date: 2020-03-27T13:01:00Z
slug: "js-react-event-rxjs-debounce"
tags: ["js", "react", "rxjs"]
---

Today I would like to show you an easy way to subscribe to a window event in React and how you can debounce it, so that your callback is not called too often.

One scenario when this could prove useful, is if you want to make some kind of update on your page whenever the size of the window has changed.

So how do we know when the window has been resized? We can use the browser built-in method addEventListener, like so:

```js
window.addEventListener('resize', handleResize);
```

And we unsubscribe with:

```js
window.removeEventListener('resize', handleResize);
```

Only we are not going to do any of that. Instead we'll be using RxJS and its fromEvent method:

```js
const listener = fromEvent(window, 'resize');

const subscription = listener.subscribe(handleResize);

// and unsubscribing with
subscription.unsubscribe();
```

Now let's add this to our React component function and use the useEffect hook to make a custom hook that is going to handle the subscription and unsubscription. We'll call this custom hook useWindowResize.

```js
import { fromEvent, timer } from 'rxjs';
import { debounce } from 'rxjs/operators';

function useWindowResize(people) {
  useEffect(() => {
    const handleResize = () => {
      // Do stuff
    }

    const event = fromEvent(window, 'resize').pipe(debounce(() => timer(1000)));

    const subscription = event.subscribe(handleResize);

    return () => subscription.unsubscribe();
  })
}
```

Now every time the window is being resizes, we are going to be calling the handleResize method. But, it will never be called more often than once per second (1000ms).

Have a nice day!