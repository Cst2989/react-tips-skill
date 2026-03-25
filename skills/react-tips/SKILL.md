---
name: react-tips
description: 10 high-impact React patterns and anti-patterns - state management, performance, hooks, and component design. Use when writing or reviewing React components.
---

# 10 React Tips That Actually Matter

Use these patterns when writing or reviewing React code. Each one prevents real bugs or eliminates unnecessary complexity.

## 1. Use useReducer When State Is Related

When multiple `useState` values depend on each other (loading + error + data), use `useReducer` instead. Prevents impossible state combinations where your UI lies to the user.

```javascript
// BAD: Three setters you can forget to coordinate
setIsLoading(false);
setError(null);
setPost(data);

// GOOD: One dispatch, one guaranteed valid state
dispatch({ type: 'FETCH_SUCCESS', payload: data });
```

**When to apply:** Any time changing one piece of state requires changing another.

## 2. useTransition Over Debounce

For search inputs and heavy filtering, use `useTransition` instead of debounce. It marks state updates as non-urgent so React can keep the UI responsive.

```javascript
const [isPending, startTransition] = useTransition();

startTransition(() => {
  setFilteredItems(filterItems(query));
});
```

**When to apply:** Any input that triggers expensive re-renders (search, filters, large lists).

## 3. State Colocation

Move state as close as possible to where it's consumed. If only `SearchBox` and `SearchResults` need `searchTerm`, wrap them in a `SearchFeature` component that owns the state. Siblings won't re-render.

**When to apply:** Before reaching for `React.memo`, check if the state can just be moved down the tree.

## 4. useEffect Is Synchronization, Not Lifecycle

`useEffect` is NOT `componentDidMount`. It's "run this when these dependencies change." Don't use it for data fetching (use React Query/SWR). Don't put functions inside unless necessary. Extract logic out of the effect body.

**When to apply:** Any time you're writing a `useEffect`, ask: "Am I synchronizing with something external, or am I triggering logic?" If the latter, there's probably a better pattern.

## 5. Never Use Index as Key

`key={index}` causes state to stick to the wrong DOM nodes when items are added, removed, or reordered. Always use a stable, unique ID from your data (`key={item.id}`).

**When to apply:** Every list render. No exceptions.

## 6. Don't Overuse useMemo

`useMemo` has overhead: hook call + dependency comparison every render. For simple operations (string concat, basic math), the overhead exceeds the cost of just computing the value. Profile before memoizing.

```javascript
// BAD: Memoizing a string concatenation
const fullName = useMemo(() => `${first} ${last}`, [first, last]);

// GOOD: Just compute it
const fullName = `${first} ${last}`;
```

**When to apply:** Only use `useMemo` for genuinely expensive computations (large array operations, complex transformations). The React Compiler will handle the rest.

## 7. SRP = One Reason to Change

A component should have one reason to change, not "do one thing." Extract data-fetching into custom hooks. Keep presentation separate from logic. When the API changes, only the hook changes. When the layout changes, only the component changes.

**When to apply:** When a component mixes fetching, error handling, and rendering. Split into a hook + a presentational component.

## 8. Key Prop Resets Components

Use `key` on any component (not just lists) to force React to unmount and remount it. This resets all state and re-runs effects, eliminating dependency array complexity.

```javascript
<UserProfile key={userId} id={userId} />
```

**When to apply:** When a component's entire state depends on a single prop (user ID, tab ID, etc.).

## 9. useLayoutEffect for DOM Measurement

`useEffect` runs after the browser paints. `useLayoutEffect` runs before paint. Use `useLayoutEffect` when you need to measure DOM elements and immediately update styles to prevent one-frame flickers.

**When to apply:** Tooltips, popovers, dynamic positioning, any visual measurement + mutation.

## 10. Compound Components Pattern

Instead of passing config objects/arrays, build components that share state via Context. Each sub-component gets its own provider and reaches into the nearest parent's context.

```javascript
<Accordion>
  <Accordion.Item>
    <Accordion.Header>Title</Accordion.Header>
    <Accordion.Body>Content</Accordion.Body>
  </Accordion.Item>
</Accordion>
```

**When to apply:** Reusable UI components (accordions, tabs, menus, dropdowns) where the consumer needs flexibility in what goes inside.

## Quick Reference

| Problem | Reach For |
|---------|-----------|
| Related state getting out of sync | `useReducer` |
| UI lag on input | `useTransition` |
| Unnecessary re-renders | State colocation |
| Data fetching in useEffect | React Query / SWR |
| List rendering bugs | Stable `key` from data |
| Simple computation wrapped in useMemo | Remove the useMemo |
| Component with 4+ reasons to change | Custom hook + presentation split |
| Component state tied to a single ID | `key={id}` prop |
| DOM measurement flicker | `useLayoutEffect` |
| Rigid component APIs | Compound Components |
