# Help

The `Help` component provides a contextual help surface that reflects one of four mutually-exclusive states: `loading`, `empty`, `error`, and `success`. The trigger/content children always render; the status text (exposed via `role="status"` for assistive technology) changes to reflect the current fetch/availability state of the help copy.

A separate `sr-only` live region (`aria-live="polite"`, `aria-atomic="true"`) announces status changes as they occur, with a configurable debounce window (default 300ms) to coalesce rapid updates. The initial mount is skipped so the empty region is never announced on first render.

The component is memoized (`React.memo`) so stable props prevent needless re-renders of the trigger subtree.

## Props

| Prop | Type | Required | Default | Notes |
|---|---|---|---|---|
| `status` | `'loading' \| 'empty' \| 'error' \| 'success'` | Yes | — | Mutually-exclusive render state. Drives which status message is shown. |
| `message` | `string` | No | — | Extra detail appended to the status label (e.g. an error description, empty hint, or success confirmation). |
| `children` | `ReactNode` | Yes | — | The element the help affordance is attached to (trigger / content). |
| `debounceMs` | `number` | No | `300` | Debounce window (ms) for the live-region announcement. Rapid status changes are coalesced so the last one wins. Set to `0` for immediate announcement. |

## States

| Status | Visible Label | Live Region Announcement |
|---|---|---|
| `loading` | "Loading help…" | `"Loading help…"` (+ optional `message`) |
| `empty` | "No help available" | `"No help available"` (+ optional `message`) |
| `error` | "Help unavailable" | `"Help unavailable"` (+ optional `message`) |
| `success` | "Help ready" | `"Help ready"` (+ optional `message`) |

Only one status label is present at a time; the previous label is replaced, never stacked.

### Status Semantics

- **`loading`** — Indicates that help content is currently being fetched. The visible status text reads "Loading help…".
- **`empty`** — Indicates that no help content is available. The visible status text reads "No help available". The optional `message` can provide a hint (e.g. "No results matched your query").
- **`error`** — Indicates that help content failed to load. The visible status text reads "Help unavailable". The `message` should describe the error (e.g. "Failed to load help content").
- **`success`** — Indicates that help content is available and ready. The visible status text reads "Help ready". The optional `message` can provide additional context (e.g. "3 articles found").

## Debounce Behavior

- Status changes are debounced by `debounceMs` (default 300ms) before being announced in the live region.
- If the status changes multiple times within the debounce window, only the last status is announced (rapid coalescing).
- The visible status text updates immediately (not debounced) — only the `sr-only` live region announcement is debounced.
- Setting `debounceMs` to `0` disables debouncing, making announcements immediate.
- The initial mount is always skipped — the live region is empty on first render regardless of the initial `status`.

## Accessibility

- **Visible Status**: The visible status text has `role="status"` and `aria-live="polite"`, so screen readers announce state changes without interrupting the user.
- **Live Region**: A separate `sr-only` element (`aria-live="polite"`, `aria-atomic="true"`) announces status changes after the debounce window, ensuring the full announcement (including any `message`) is read as a single atomic unit.
- **Memoization**: The component is wrapped in `React.memo` so stable props prevent needless re-renders of the trigger subtree.

## Usage Examples

### Basic Usage

```tsx
import { Help } from '@/components/Help';

<Help status="success">
  <button>Get help</button>
</Help>
```

### With Error State and Message

```tsx
import { Help } from '@/components/Help';

<Help status="error" message="Failed to load help content">
  <button>Get help</button>
</Help>
```

### With Custom Debounce

```tsx
import { Help } from '@/components/Help';

<Help status="loading" debounceMs={500}>
  <button>Get help</button>
</Help>
```

### Immediate Announcement (No Debounce)

```tsx
import { Help } from '@/components/Help';

<Help status="success" message="3 articles found" debounceMs={0}>
  <button>Get help</button>
</Help>
```

### State Transitions (Loading → Success)

```tsx
import { useState, useEffect } from 'react';
import { Help, type HelpStatus } from '@/components/Help';

export function AsyncHelp() {
  const [status, setStatus] = useState<HelpStatus>('loading');

  useEffect(() => {
    fetchHelpContent()
      .then(() => setStatus('success'))
      .catch(() => setStatus('error'));
  }, []);

  return (
    <Help status={status}>
      <button>Get help</button>
    </Help>
  );
}
