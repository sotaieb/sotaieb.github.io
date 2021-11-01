---
title: "Getting started React JS"
categories:
  - Javascript
  - React Js
tags:
  - React Js
---

React is a JavaScript library for building user interfaces.

- Declarative
- Component-Based

- Stateful component(maintain an internal state)
- Reusable Components (immutable props , mutable state, output ui)
- Reactive updates
- Virtual Tree/ Tree reconciliation

Principles:

- Lifting: Move something from a lower to a higher component.
- Higher Order Component
- Render Props
- Reconciliation

Scripts:
react.js / react
react-dom.js / react-dom

Client:
create-react-app

ToolChain:

- Package manager : npm/yarn
- Bundler: webpack
- Compiler: babel

## useCallback vs useMemo

useCallback gives you referential equality between renders for functions.
useMemo gives you referential equality between renders for values.

## useState vs useReducer

useState is a Basic Hook for managing simple state transformation and useReducer is an Additional Hook for managing more complex state logic.

## useMemo vs React.memo()

While class components already allowed you to control re-renders with the use of PureComponent or shouldComponentUpdate, React introduced the ability to do the same with functional components.
React.memo() is a higher-order component (HOC).

While React.memo() is a HOC, useMemo() is a React Hook. With useMemo(), we can return memoized values and avoid re-rendering if the dependencies to a function have not changed.
