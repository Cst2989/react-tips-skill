# React Tips - Claude Code Plugin

High-impact React patterns and anti-patterns for writing better components. Extracted from the [Daily React](https://neciudan.dev/programs/daily-react) newsletter by [Dan Neciu](https://neciudan.dev).

## What's Inside

### react-tips

10 React patterns and anti-patterns for state management, performance, hooks, and component design:

1. **useReducer for related state** - Prevent impossible state combinations
2. **useTransition over debounce** - Keep UIs responsive during heavy work
3. **State colocation** - Free performance by moving state closer to consumers
4. **useEffect mental model** - It's synchronization, not a lifecycle hook
5. **Stable keys** - Never use index as key
6. **useMemo anti-patterns** - Stop memoizing cheap operations
7. **Single Responsibility** - One reason to change, not "one thing"
8. **Key prop resets** - Delete useEffect by changing the key
9. **useLayoutEffect** - Kill tooltip flicker with synchronous DOM measurement
10. **Compound Components** - Build composable APIs with Context

### no-unnecessary-effects

A decision tree that stops the AI from reaching for `useEffect` when a better alternative exists. Covers every common misuse: derived state, event handling, state resets, data fetching, parent notifications, effect chains, and external store subscriptions.

Full article: [You really, really, really don't need an effect! I swear!](https://neciudan.dev/you-really-really-dont-need-an-effect)

## Install

Add the marketplace:

```
/plugin marketplace add Cst2989/react-tips-skill
```

Install the plugin:

```
/plugin install react-tips@neciudan.dev
```

## Usage

Once installed, the skills are available in Claude Code. They activate automatically when you're writing or reviewing React components. You can also invoke them directly:

```
/react-tips:react-tips
/react-tips:no-unnecessary-effects
```

## Read More

- [10 React tips I wish someone had told me before I mass-produced bugs](https://neciudan.dev/10-react-tips-that-actually-matter)
- [You really, really, really don't need an effect! I swear!](https://neciudan.dev/you-really-really-dont-need-an-effect)

## License

MIT
