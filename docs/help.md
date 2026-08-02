# Help Component Accessibility

The [`Help`](../src/components/Help.tsx) component is a contextual help surface
that renders a trigger/content subtree alongside a status readout. Its async
updates are announced to assistive technology through a dedicated **polite live
region**, so screen-reader users are not left guessing whether help copy has
finished loading, is unavailable, or is ready to read.

## Status model

`Help` accepts one of four mutually-exclusive statuses via the `status` prop:

| Status    | Visible label    | Announced prefix |
| --------- | ---------------- | ---------------- |
| `loading` | Loading help…    | Loading help…    |
| `empty`   | No help available | No help available |
| `error`   | Help unavailable | Help unavailable |
| `success` | Help ready       | Help ready       |

An optional `message` string is appended to both the visible label and the
announcement (e.g. `Help unavailable: Network error`). If omitted, no trailing
segment is rendered or announced.

## ARIA contract

The component exposes two complementary regions:

1. **Visible status text** — a `<span role="status" aria-live="polite">` that
   carries the current status label. This provides a live, visually-rendered
   status for sighted users while remaining a polite live region for
   assistive tech.
2. **Screen-reader-only announcement region** — a second `<span>` with
   `aria-live="polite"`, `aria-atomic="true"`, and the `sr-only` utility class.
   This region is populated **only when the status actually changes**, so
   screen readers hear the transition (e.g. "Help ready") without the page
   re-reading the initial state on mount.

## Debounce semantics

`Help` accepts a `debounceMs` prop (default **300ms**) that controls how long a
status change waits before it is written into the announcement region:

- Rapid successive status changes within the debounce window are coalesced —
  the **last one wins**.
- A pending timer is cancelled when the status changes again, when the effect
  re-runs, or when the component unmounts (preventing stale announcements).
- Setting `debounceMs` to `0` announces immediately (synchronous state writes).
- The initial mount is never announced: the previous-status ref is seeded with
  the initial status, so the first effect run sees "no change".

### Why a debounce?

Help copy can transition quickly through `loading → success` (or
`loading → error`) as a fetch resolves. Without coalescing, a screen reader
would be interrupted by a rapid string of announcements. Debouncing keeps the
announcement queue quiet and lets the final state win.

## Example

```tsx
<Help
  status={helpState.status}
  message={helpState.error ?? undefined}
  debounceMs={300}
>
  <HelpTriggerButton />
</Help>
```

## Tests

Behaviour is covered in
[`src/components/__tests__/Help.test.tsx`](../src/components/__tests__/Help.test.tsx):

- Initial mount is **not** announced, even after the debounce window elapses.
- `loading → success`, `loading → error`, and `empty → success` transitions
  are announced with/without an optional message.
- Rapid successive updates coalesce (last one wins).
- Same-status re-renders are silent.
- Custom debounce windows and immediate (`debounceMs={0}`) announcements.
- `aria-live="polite"`, `aria-atomic="true"`, and `sr-only` are present on the
  announcement region.
- Pending debounced announcements are cancelled on unmount.

## Related documentation

- [Loading Region Accessibility](loading-regions.md)
- [Accessibility Conformance Statement](ACCESSIBILITY.md)

