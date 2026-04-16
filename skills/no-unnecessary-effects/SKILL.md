---
name: no-unnecessary-effects
description: Before writing useEffect, run through a decision tree to verify it's actually needed. Prevents the most common React anti-pattern in AI-generated code.
---

# Before Writing useEffect

Every time you are about to write a `useEffect`, stop and answer this question:

**Is this syncing with an external system?**

External systems: WebSocket, browser APIs (`IntersectionObserver`, `navigator.onLine`), third-party libraries (map SDKs, chart widgets), DOM measurements, `setInterval` timers.

NOT external systems: props, state, values derived from props or state, user events (clicks, form submissions).

If the answer is no, do NOT write the effect. Use the decision tree below to find the right alternative.

## Decision Tree

Before writing the effect, check each case in order:

### 1. Am I transforming or deriving data?

Compute it inline. No state, no effect.

```javascript
// NEVER do this
const [filtered, setFiltered] = useState([]);
useEffect(() => {
  setFiltered(data.filter(item => item.active));
}, [data]);

// DO this
const filtered = data.filter(item => item.active);

// If genuinely expensive and not using React Compiler:
const filtered = useMemo(() => data.filter(item => item.active), [data]);
```

### 2. Am I responding to a user event?

Put the logic in the event handler. Effects respond to renders, not to user actions.

```javascript
// NEVER do this
useEffect(() => {
  if (submitted) {
    performSearch(query);
    setSubmitted(false);
  }
}, [submitted, query]);

// DO this
function handleSubmit(e: FormEvent) {
  e.preventDefault();
  performSearch(query);
}
```

### 3. Am I resetting state when a prop changes?

Use the `key` prop to let React unmount and remount the component with fresh state.

```javascript
// NEVER do this
useEffect(() => {
  setComment('');
}, [userId]);

// DO this — in the parent
<UserProfile key={userId} userId={userId} />
```

### 4. Am I fetching data?

Use TanStack Query (React Query) or a similar library. If you must use `useEffect`, always add cleanup to ignore stale responses.

```javascript
// Preferred
const { data, isLoading, error } = useQuery({
  queryKey: ['user', userId],
  queryFn: () => fetchUser(userId),
});

// If useEffect is unavoidable, always handle race conditions
useEffect(() => {
  let ignore = false;
  fetchUser(userId).then(data => {
    if (!ignore) setUser(data);
  });
  return () => { ignore = true; };
}, [userId]);
```

### 5. Am I notifying a parent component?

Call the parent's callback directly in the event handler, alongside `setState`. React batches both updates into one render.

```javascript
// NEVER do this
useEffect(() => {
  onChange(isOn);
}, [isOn, onChange]);

// DO this
function handleClick() {
  const next = !isOn;
  setIsOn(next);
  onChange(next);
}
```

### 6. Am I chaining multiple effects?

Move the cascade into a single event handler. Derive what you can during render.

```javascript
// NEVER do this — three effects, three render passes
useEffect(() => { setCity(''); }, [country]);
useEffect(() => { setDistrict(''); }, [city]);
useEffect(() => { setShippingCost(calculate(country, city, district)); }, [country, city, district]);

// DO this
function handleCountryChange(newCountry: string) {
  setCountry(newCountry);
  setCity('');
  setDistrict('');
}

const shippingCost = country && city && district
  ? calculateShipping(country, city, district)
  : 0;
```

### 7. Am I subscribing to an external store?

Use `useSyncExternalStore` instead of manually wiring up listeners with `useEffect`.

```javascript
// NEVER do this
useEffect(() => {
  const handler = () => setIsOnline(navigator.onLine);
  window.addEventListener('online', handler);
  window.addEventListener('offline', handler);
  return () => {
    window.removeEventListener('online', handler);
    window.removeEventListener('offline', handler);
  };
}, []);

// DO this
function subscribe(callback: () => void) {
  window.addEventListener('online', callback);
  window.addEventListener('offline', callback);
  return () => {
    window.removeEventListener('online', callback);
    window.removeEventListener('offline', callback);
  };
}

const isOnline = useSyncExternalStore(subscribe, () => navigator.onLine, () => true);
```

## When useEffect IS correct

If none of the above cases apply and the answer to "Is this an external system?" is genuinely **yes**, then `useEffect` is the right tool. Examples:

- WebSocket connections (open on mount, close on unmount)
- Third-party widget initialization (map SDKs, rich text editors)
- DOM measurements (`useLayoutEffect` for pre-paint, `useEffect` for post-paint)
- Browser API subscriptions with cleanup (`IntersectionObserver`, `ResizeObserver`)

When writing a valid effect:

- **Name the function** for readability: `useEffect(function connectToChat() { ... })`
- **Always return a cleanup function** when subscribing or connecting
- **List all dependencies** the effect reads from
- **Use `useLayoutEffect`** when measuring the DOM to avoid visual flicker

## Rules

- NEVER write `useEffect(() => setSomething(derivedValue), [dep])` — compute it inline
- NEVER use `useEffect` to respond to click/submit/change events
- NEVER use `useEffect` to reset state on prop change without first considering the `key` prop
- NEVER chain effects where one effect sets state that triggers another effect
- ALWAYS add cleanup functions when subscribing to external systems
- ALWAYS name your effect functions for readability
