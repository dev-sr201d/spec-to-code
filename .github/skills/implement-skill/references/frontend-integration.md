# Frontend Integration Checklist

When implementing frontend features, verify:

## Navigation & Layout

- [ ] Feature is reachable through navigation (if not, add navigation links)
- [ ] Page uses the project's shared layout components
- [ ] Users can discover this feature without typing a URL manually
- [ ] Authentication flow works from this page
- [ ] Page matches the design patterns of other pages
- [ ] No dead-ends or orphaned pages

## Accessibility (a11y)

- [ ] Meets the WCAG conformance target defined in `AGENTS.md` (or PRD if AGENTS.md defers to it)
- [ ] Keyboard-only navigation works for every interactive element
- [ ] Focus order is logical and visible focus indicators are present
- [ ] Semantic HTML / ARIA roles used correctly (no `div` buttons)
- [ ] Color contrast meets the configured WCAG level
- [ ] Forms have programmatic labels and accessible error messages
- [ ] Automated a11y check passes (axe / Lighthouse a11y / equivalent) with no new violations

## Internationalization (i18n)

- [ ] All user-facing strings go through the i18n layer — no hardcoded copy
- [ ] Dates, times, numbers, and currencies use locale-aware formatting
- [ ] Layout works with longer translations and RTL languages if the PRD lists them

## Performance & Bundle

- [ ] Bundle size budget in `AGENTS.md` is not exceeded (or exception is documented)
- [ ] Above-the-fold content meets Core Web Vitals targets defined in `AGENTS.md` / PRD
- [ ] Heavy components are code-split / lazy-loaded where appropriate
- [ ] Images use modern formats and responsive sizing

## Resilience & UX

- [ ] Loading, empty, error, and offline states are designed and implemented
- [ ] Network calls have timeouts and surface user-friendly error messages
- [ ] Forms validate on the client *and* the server
- [ ] No console errors or unhandled promise rejections in normal flows

## Security

- [ ] No `dangerouslySetInnerHTML` / `innerHTML` with untrusted input
- [ ] User-generated content escaped/sanitized before rendering
- [ ] Tokens stored according to `AGENTS.md` (no long-lived secrets in `localStorage` unless explicitly approved)
- [ ] Third-party scripts pinned and integrity-checked where supported
