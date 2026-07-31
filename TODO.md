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
- [ ] 4. Run `npm test` to verify all tests pass (Node.js/npm not available on this system)
- [ ] 5. Run `npm run lint` to check linting (Node.js/npm not available on this system)
- [ ] 6. Run `npm run build` to verify build (Node.js/npm not available on this system)
