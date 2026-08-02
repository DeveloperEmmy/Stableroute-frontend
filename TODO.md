# Task: Announce help updates through an ARIA live region

## Steps

- [x] 1. Create TODO.md
- [x] 2. Modify `src/components/Help.tsx`:
  - Add `debounceMs` prop (default 300ms)
  - Add `useRef` to track previous status
  - Add separate `sr-only` live region for announcements
  - Skip announcement on initial mount
  - Debounce rapid status updates
- [x] 3. Update `src/components/__tests__/Help.test.tsx`:
  - Test initial mount does not announce
  - Test status change announcements (loading → success, loading → error)
  - Test optional message included in announcement
  - Test debounce coalesces rapid updates
  - Test visual status text still renders
  - Test `aria-live="polite"` and `aria-atomic="true"` attributes
- [x] 4. Fix initial-mount announcement bug in `Help.tsx` (prev-status ref seeded with initial status; remove redundant mount-marker effect)
- [x] 5. Update `Help.test.tsx` assertions for jest-dom v6 (`toBeEmptyDOMElement()` instead of `toHaveTextContent('')`); strengthen initial-mount test to advance past debounce; add unmount timer-cleanup test
- [x] 6. Create `docs/help.md` documenting the live-region behaviour
- [x] 7. Create `.github/PULL_REQUEST_BODY.md` with full test output
- [x] 8. Run `npm run lint`
- [x] 9. Run `npm test`
- [x] 10. Run `npm run build`

