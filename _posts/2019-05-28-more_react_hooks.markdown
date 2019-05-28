---
layout: post
title:      "More React Hooks!"
date:       2019-05-28 19:21:44 +0000
permalink:  more_react_hooks
---


This week, I'm digging deeper into React Hooks. I'll be giving a talk about React Hooks tomorrow at a [React Ladies](https://www.meetup.com/React-Ladies/) Lightning Talks event! I'm super excited, and this will be my first tech talk ever. Here's a [link](https://docs.google.com/presentation/d/1aJ5W9DTYlD1ctVFMSEO8wz_c2mOeHESevOMa8sJ2sxA/edit?usp=sharing) to some slides I created for the occasion, and I'll embed the slides below:

<figure class="video_container">
<iframe src="https://docs.google.com/presentation/d/e/2PACX-1vQN5c52WvMU3knYbf1r4Huz8ptfe9_s_Rnm5xbkCUZcqOC5NF_fN_1I8xBBV8rlmgbAJWMHqLgYBHMt/embed?start=false&loop=false&delayms=5000" frameborder="0" width="100%" height="350" allowfullscreen="true" mozallowfullscreen="true" webkitallowfullscreen="true"></iframe>
</figure>

A few weeks ago, I posted about [React Hooks](http://malloryfeuer.net/react_hooks) on this blog- mostly about `useState` and `useEffect`. I just wanted to add a few more points I plan on bringing up at my talk tomorrow.

In addition to the State Hook and the Effect Hook,  there are many other [built-in Hooks](https://reactjs.org/docs/hooks-reference.html) for React, and we're even encouraged to create our own [custom Hooks](https://reactjs.org/docs/hooks-custom.html).

## **Built-In Hooks**
* Basic Hooks
   * useState
   * useEffect
   * useContext
* Additional Hooks
   * useReducer
   * useCallback
   * useMemo
   * useRef
   * useImperativeHandle
   * useLayoutEffect
   * useDebugValue

  
## **Custom Hooks**
We can build our own hooks! A custom hook can be any JavaScript function whose name starts with "use" and that may call other hooks. We can decide what it takes as arguments, and what it returns, just like any other function. This makes it very easy for us to pass information and reuse common stateful logic between different components.

## **Rules**
It's important to only call Hooks at the top level, not inside loops, conditions, or nested functions, because Hooks need to be called in the same order each time a component renders. React depends on the order in which hooks are called in order for it to know which state corresponds to which `useState` call.




