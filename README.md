# React Tips - Claude Code Plugin

10 high-impact React patterns and anti-patterns for writing better components. Extracted from the [Daily React](https://neciudan.dev/programs/daily-react) newsletter by [Dan Neciu](https://neciudan.dev).

## What's Inside

A Claude Code skill that guides you toward better React patterns while you code:

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

## Install

Add the marketplace:

```
/plugin marketplace add neciudan/react-tips-skill
```

Install the plugin:

```
/plugin install react-tips@neciudan-react-tips
```

## Usage

Once installed, the skill is available in Claude Code. It activates when you're writing or reviewing React components. You can also invoke it directly:

```
/react-tips:react-tips
```

## Read More

Full article: [10 React tips I wish someone had told me before I mass-produced bugs](https://neciudan.dev/10-react-tips-that-actually-matter)

## License

MIT
