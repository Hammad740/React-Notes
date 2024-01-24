# What is state in react?

## State: A Component's Memory

- Components often need to change what’s on the screen as a result of an interaction. Typing into the form should update the input field, clicking “next” on an image carousel should change which image is displayed, clicking “buy” should put a product in the shopping cart. Components need to “remember” things: the current input value, the current image, the shopping cart. In React, this kind of component-specific memory is called state.
- [State](https://react.dev/learn/state-a-components-memory)

- React mein, "state" ek important concept hai jo components ke internal data ko represent karta hai. State ka use kisi component ke dynamic behavior ya UI ko track karne ke liye hota hai. Har React component apne internal state ko maintain kar sakta hai aur jab bhi state change hota hai, component ko re-render hone ka mauka milta hai.

- Yahan kuch mukhya baatein hain jo React state ke baare mein samajhne mein madad karengi:

1. **State Kya Hai:**

   - State ek JavaScript object hai jo component ke internal data ko store karta hai. Ye data dynamic hota hai, matlab ki jab bhi isme koi change hoti hai, React component re-render hota hai.

2. **State Ka Istemal:**

   - State ka istemal kisi component ke dynamic behavior ya UI changes ko track karne ke liye hota hai. For example, ek form component jo user ke input ko track karta hai, ya ek counter component jo apne khud ke value ko badhata hai.

   ```jsx
   import React, { useState } from 'react';

   const Counter = () => {
     const [count, setCount] = useState(0);

     const increment = () => {
       setCount(count + 1);
     };

     return (
       <div>
         <p>Count: {count}</p>
         <button onClick={increment}>Increment</button>
       </div>
     );
   };
   ```

3. **useState Hook:**

   - State ka use karne ke liye, aap `useState` hook ka istemal karte hain. `useState` hook ek array return karta hai jisme current state aur state ko update karne ke liye ek function hoti hai.

   ```jsx
   const [state, setState] = useState(initialState);
   ```

   - Yahan `state` variable current state ko represent karta hai, aur `setState` function state ko update karne ke liye istemal hoti hai.

4. **State Update:**

   - State ko update karne ke liye, aap `setState` function ka istemal karte hain. Ye function current state ko lekar new state ko calculate karta hai.

   ```jsx
   const increment = () => {
     setCount(count + 1);
   };
   ```

5. **Previous State Ka Istemal:**

   - `setState` function ko use karte waqt, aap previous state ka istemal karke bhi state ko update kar sakte hain. Isse race conditions se bacha ja sakta hai.

   ```jsx
   const increment = () => {
     setCount((prevCount) => prevCount + 1);
   };
   ```

6. **Class Components Mein State:**

   - Class components mein state ko manage karne ke liye, aap constructor aur `this.setState` ka istemal karte hain.

   ```jsx
   class Counter extends React.Component {
     constructor(props) {
       super(props);
       this.state = {
         count: 0,
       };
     }

     increment() {
       this.setState({ count: this.state.count + 1 });
     }
   }
   ```

7. **Stateless (Functional) Components:**

   - Functional components bhi state manage kar sakte hain `useState` hook ka istemal karke. Pehle, state sirf class components ke liye tha, lekin hooks ke aane ke baad functional components bhi state handle kar sakte hain.

   ```jsx
   const ExampleComponent = () => {
     const [value, setValue] = useState(initialValue);
     // ...
   };
   ```

- React state ka istemal karte waqt dhyan rakha jata hai ki direct mutations na ho, aur hamesha `setState` ka istemal current state se derive karke new state create karne ke liye kiya jaye. Isse React internal tracking mechanism ko sahi taur par kaam karne mein madad milti hai.

**Must Read Article**

- [State](https://www.freecodecamp.org/news/react-hooks-fundamentals/)

## Basics of useState

- returns an array with two elements: the current state value, and a function that we can use to update the state
- accepts default value as an argument
- state update triggers re-render
- [useState Docs](https://react.dev/reference/react/useState)

# General Rules of Hooks

- starts with "use" (both -react and custom hooks)
- component must be uppercase
- invoke inside function/component body
- don't call hooks conditionally (cover later)
- set functions don't update state immediately (cover later)

# Initial Render and Re-Renders

- In a React application, the initial render is the first time that the component tree is rendered to the DOM. It happens when the application first loads, or when the root component is first rendered. This is also known as "mounting" the components.

- Re-renders, on the other hand, happen when the component's state or props change, and the component needs to be updated in the DOM to reflect these changes. React uses a virtual DOM to optimize the process of updating the actual DOM, so that only the necessary changes are made.

- There are a few ways that you can trigger a re-render in a React component:

- By changing the component's state or props. When the component's state or props change, React will re-render the component to reflect these changes.

- When the parent element re-renders, even if the component's state or props have not changed.

## Automatic Batching

- In React, "batching" refers to the process of grouping multiple state updates into a single update. This can be useful in certain cases because it allows React to optimize the rendering of your components by minimizing the number of DOM updates that it has to perform.

- By default, React uses a technique called "auto-batching" to group state updates that occur within the same event loop into a single update. This means that if you call the state update function multiple times in a short period of time, React will only perform a single re-render for all of the updates.

- React 18 ensures that state updates invoked from any location will be batched by default. This will batch state updates, including native event handlers, asynchronous operations, timeouts, and intervals.

# Gotcha in useState

`useState` hook React mein ek bahut common aur useful tool hai state management ke liye, lekin kuch cheezein hain jo aapko dhyan mein rakhni chahiye jab aap iska istemal karte hain:

1. **Async Updates:**

   - `setState` jo `useState` ke sath aata hai, woh asynchronous hota hai. Matlab, jab aap `setState` karte hain, to woh turant state ko update nahi karta. Agar aap current state ka istemal karke next state calculate karte hain, to aapko functional update form ka istemal karna chahiye.

   ```jsx
   // Galat
   const handleClick = () => {
     setCount(count + 1);
     console.log(count); // Updated count turant nahi dikhega
   };

   // Sahi
   const handleClick = () => {
     setCount((prevCount) => prevCount + 1);
     // Ab aap yahan updated count ko log kar sakte hain
   };
   ```

2. **Object State:**

   - Agar aap state ko object ke roop mein use kar rahe hain, to dhyan rakhein ki aap previous state ko spread karen taki aap unintentional overwrites se bach sakein.

   ```jsx
   // Galat
   const setUser = (newUser) => {
     setUserObject({ user: newUser }); // Ye dusre properties ko overwrite kar dega
   };

   // Sahi
   const setUser = (newUser) => {
     setUserObject((prevUser) => ({ ...prevUser, user: newUser }));
   };
   ```

3. **Functional Updates:**

   - Jab aap functional updates ka istemal karte hain, to dhyan rakhein ki update function ke argument mein previous state hota hai aur yeh current state nahi hota.

   ```jsx
   // Galat
   const increment = () => {
     setCount(count + 1); // Yeh expected behavior nahi dega agar count stale ho
   };

   // Sahi
   const increment = () => {
     setCount((prevCount) => prevCount + 1); // Functional update ka istemal karke latest count ko ensure karein
   };
   ```

4. **Multiple State Variables:**

   - Agar aapke paas multiple state variables hain, to behtar management ke liye `useReducer` hook ka istemal consider karein, khaas kar jab aapko complex state logic ke sath kaam karna ho.

   ```jsx
   // Instead of
   const [count, setCount] = useState(0);
   const [name, setName] = useState('');

   // Consider using
   const [state, dispatch] = useReducer(reducer, initialState);
   ```

5. **Re-rendering:**

   - Yaad rakhein ki state ko update karna component ko re-render karta hai. Zyaada re-renders performance par asar daal sakte hain. Memoization ya `React.memo` ka istemal jaise techniques ko dhyan mein rakhein.

   ```jsx
   // Without memoization
   const Component = () => {
     // ...
   };

   // With memoization
   const MemoizedComponent = React.memo(() => {
     // ...
   });
   ```

- Hamesha yeh dhyan mein rakhein ki React ke official documentation mein latest guidelines aur best practices hamesha up-to-date hoti hain jab aap React hooks aur state management ke sath kaam karte hain.

# useEffect hook in react

useEffect is a hook in React that allows you to perform side effects in function components.There is no need for urban dictionary - basically any work outside of the component. Some examples of side effects are: subscriptions, fetching data, directly updating the DOM, event listeners, timers, etc.

- useEffect hook
- accepts two arguments (second optional)
- first argument - cb function
- second argument - dependency array
- by default runs on each render (initial and re-render)
- cb can't return promise (so can't make it async)
- if dependency array empty [] runs only on initial render

`useEffect` ek important React Hook hai jo functional components mein side effects ko manage karne mein madad karta hai. Side effects, jaise ki data fetching, subscriptions, aur DOM manipulations, `useEffect` ke through handle kiye ja sakte hain. Yahan kuch key points hain `useEffect` ke baare mein:

1. **Basic Syntax:**

   - `useEffect` ka basic syntax is tarah hota hai:

   ```jsx
   useEffect(() => {
     // Side effect code here
     return () => {
       // Cleanup code here (optional)
     };
   }, [dependencies]);
   ```

   - Yahan, `useEffect` ek callback function aur optional cleanup function ke sath aata hai. Dependencies array bhi hota hai jo batata hai ki kis kis tarah ke changes par `useEffect` ko run karna chahiye.

2. **Side Effects:**

   - `useEffect` ka primary purpose side effects ko manage karna hai. Side effects woh actions hote hain jo component render hone ke baad hoti hain, jaise API calls, subscriptions, aur DOM manipulations.

   ```jsx
   useEffect(() => {
     // Data fetching or other side effect
     fetchData();
   }, [dependencies]);
   ```

3. **Cleanup Function:**

   - `useEffect` ke andar diye gaye callback function ka ek optional part hota hai jo cleanup ke liye use hota hai. Yeh function tab call hota hai jab component unmount hota hai ya dependencies update hote hain.

   ```jsx
   useEffect(() => {
     // Side effect code here
     return () => {
       // Cleanup code here
     };
   }, [dependencies]);
   ```

4. **Dependencies Array:**

   - `useEffect` ke last mein ek dependencies array hota hai. Dependencies array bataata hai ki `useEffect` ko kis-kis state ya prop changes par run karna chahiye. Agar dependencies array empty hai, to `useEffect` sirf ek baar, component mount hone par hi run hoga.

   ```jsx
   useEffect(() => {
     // Code to run on mount
   }, []);
   ```

5. **Multiple `useEffect`:**

   - Aap multiple `useEffect` hooks bhi ek hi component mein use kar sakte hain. Har `useEffect` apne own side effects aur dependencies ke sath kaam karta hai.

   ```jsx
   useEffect(() => {
     // Side effect 1
   }, [dependency1]);

   useEffect(() => {
     // Side effect 2
   }, [dependency2]);
   ```

6. **Common Use Cases:**

   - Data fetching, subscriptions, DOM manipulations, timers, aur kisi bhi asynchronous operation ko `useEffect` ke andar handle kiya ja sakta hai.

   ```jsx
   useEffect(() => {
     // Data fetching
     const fetchData = async () => {
       const response = await fetch('https://api.example.com/data');
       const data = await response.json();
       setData(data);
     };

     fetchData();

     // Cleanup function
     return () => {
       // Cleanup code
     };
   }, [dependencies]);
   ```

`useEffect` ka istemal karke aap component lifecycle ke anusaar side effects ko handle kar sakte hain, jisse aapke component maintainable aur predictable rehte hain.

## infinite loop

React mein infinite loop typically tab hota hai jab aap apne component ke state ya props ko update karte hain, aur woh update component ko re-render karta hai. Agar aap is re-rendering cycle ko control mein nahi rakhte hain, to ek infinite loop create ho sakta hai. Yeh kuch common scenarios hote hain jinse infinite loop create ho sakta hai:

1. **State Change in Render Method:**

   - Agar aap apne component ke render method ke andar state ko directly update karte hain, to har render mein state update hoga, aur fir render hoga, aur yeh cycle chalta rahega.

   ```jsx
   const MyComponent = () => {
     // ...

     // Galat: State ko direct render method ke andar update karna
     setState(newState);

     return (
       // Component's JSX
     );
   };
   ```

   - Is situation ko theek karne ke liye, `useEffect` ka istemal karein aur dependencies array ka sahi taur par manage karein.

   ```jsx
   const MyComponent = () => {
     // ...

     useEffect(() => {
       // Sahi: State ko useEffect ke andar update karna
       setState(newState);
     }, [dependencies]);

     return (
       // Component's JSX
     );
   };
   ```

2. **Incorrect Dependencies Array:**

   - Dependencies array ke sahi se manage na karne se bhi infinite loop ho sakta hai. Dependencies array mein un variables ko include karein jo state ya props mein se aate hain aur jo agar update hote hain to `useEffect` chalna chahiye.

   ```jsx
   useEffect(() => {
     // ...
     // Galat: dependencies array mein sahi se variables include nahi kiye gaye hain
   }, []);
   ```

   - Dependencies array ko sahi taur par manage karein, taki `useEffect` sirf un changes par hi chale jo aap expect karte hain.

   ```jsx
   useEffect(() => {
     // ...
     // Sahi: dependencies array mein sahi se variables include kiye gaye hain
   }, [dependencies]);
   ```

3. **Infinite State Update:**

   - Ek state update ka dusre state update ko trigger karna bhi infinite loop create kar sakta hai.

   ```jsx
   const MyComponent = () => {
     const [count, setCount] = useState(0);

     useEffect(() => {
       // Galat: setCount ek infinite loop create karega
       setCount(count + 1);
     }, []);

     return (
       // Component's JSX
     );
   };
   ```

   - Is situation ko handle karne ke liye functional update form ka istemal karein.

   ```jsx
   const MyComponent = () => {
     const [count, setCount] = useState(0);

     useEffect(() => {
       // Sahi: functional update form ka istemal karein
       setCount((prevCount) => prevCount + 1);
     }, []);

     return (
       // Component's JSX
     );
   };
   ```

Infinite loop ki problem ko solve karne ke liye, aapko apne component ke lifecycle aur state changes ko sahi taur par samajhna important hai. Dependencies array ko sahi taur par manage karna aur state ko sahi taur par update karna ismein madadgar hota hai.

Bina `useEffect` ke ek example jisme infinite loop ban sakta hai, wo hai jab aap state ko seedha render method ke andar update karte hain. Yeh ek example hai:

```jsx
import React, { useState } from 'react';

const InfiniteLoopWithoutUseEffect = () => {
  const [count, setCount] = useState(0);

  // Galat: State ko seedha render method ke andar update karna
  setCount(count + 1);

  return (
    <div>
      <p>Count: {count}</p>
    </div>
  );
};

export default InfiniteLoopWithoutUseEffect;
```

Is example mein `setCount` ko render method ke andar hi call kiya gaya hai, jo har baar component re-render hota hai. Re-render hone par wapas se `setCount` call hota hai, aur yeh cycle chalta rahega.

Is problem ko theek karne ke liye, aapko state ko seedha render method ke andar update nahi karna chahiye. Aapko `setCount` ko kisi event handler ya effect ke andar use karna chahiye, jisse state update tabhi ho jab aapko chahiye. Yeh ek possible sahi tareeka ho sakta hai:

```jsx
import React, { useState } from 'react';

const InfiniteLoopWithoutUseEffectFixed = () => {
  const [count, setCount] = useState(0);

  // Sahi: Event handler mein state update karna
  const handleIncrement = () => {
    setCount(count + 1);
  };

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={handleIncrement}>Increment</button>
    </div>
  );
};

export default InfiniteLoopWithoutUseEffectFixed;
```

Is example mein `setCount` ko `handleIncrement` event handler mein use kiya gaya hai, jisse state update tabhi hoga jab button click hoga.
