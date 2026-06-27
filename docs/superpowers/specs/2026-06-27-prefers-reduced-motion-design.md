# Design: `prefers-reduced-motion` Support

Date: 2026-06-27
Status: Approved
Target version: stays in 3.1.x (the cleanup/version-jump round is separate)

## Goal

Honour the OS-level "reduce motion" preference. When a user disables
animation effects at the system level (Windows: *Show animations in
Windows* off), FoxOne's own decorative motion goes quiet automatically.
No manual toggle, no configuration.

## Decisions

1. **Trigger: native `@media (prefers-reduced-motion: reduce)`.**
   Not a `--uc-` toggle. This is the accessibility convention and is
   driven by the OS, which already knows the user's intent. A toggle
   would duplicate a setting Windows owns.

2. **Scope: only FoxOne's own motion.** Firefox's native chrome
   animations (panel slides, tab open) are left untouched. FoxOne is a
   theme, not an OS patch. No blanket `*` selector.

3. **Loading bar: static, not removed.** Reduced motion means "no
   motion", not "no information". The bar shows as a resting full
   stroke rather than a growing one.

## Mechanics

A single consolidated `@media (prefers-reduced-motion: reduce){ … }`
block at the **end of `userChrome.css`**.

FoxOne's transitions are all set with `!important`. A media query does
not raise specificity, so the override wins only through **identical
selectors + `!important` + later source position**. Placing the block
at end-of-file satisfies the source-order requirement for every target.

Placing everything in one block also makes it trivial to locate and
relocate during the upcoming cleanup round.

## Targets

Each of these gets `transition: none !important` (appears/disappears
instantly instead of animating):

| # | Selector | Current motion |
|---|----------|----------------|
| 1 | `#nav-bar` | urlbar width 0.25s |
| 2 | `#titlebar` | counter-width 0.25s |
| 3 | `#reload-button, #stop-reload-button` (autohide block) | reveal 0.2s |
| 4 | `#identity-permission-box` reveal group | reveal 0.2s |
| 5 | `#userContext-label, #userContext-indicator` | reveal 0.2s |
| 6 | page-action icons (`#reader-mode-button`, `#taskbar-tabs-button`, `#translations-button`, `.urlbar-addon-page-action`) | reveal 0.2s |
| 7 | `#TabsToolbar-customization-target > toolbaritem` (extension icons) | reveal 0.2s |
| 8 | `findbar`, `findbar[hidden]` | slide + scaleY 100ms |

No information is lost; only the transition to the end state is removed.

### Special case: loading progress bar

Selectors: `.tabbrowser-tab[busy] .tab-background::before` and `::after`
(plus the base `.tabbrowser-tab .tab-background::before`).

Override:

```css
animation: none !important;
transform: scaleX(1) !important;
```

Result: a resting full stroke instead of the `uc-progress` scaleX
keyframe growth. Visibility stays bound to `--uc-show-loading-progress`
(default `0`), so the feature remains opt-in exactly as before. Users
who enabled it get a calm static stroke under reduced motion; everyone
else sees nothing, unchanged.

The base `::before` transition (`opacity 0.3s`) is also nulled so the
opt-in state appears instantly.

## Out of scope

- Firefox native chrome animations.
- Any `--uc-` toggle variable.
- The broader cleanup of legacy code (e.g. whether the loading bar
  should exist at all). That is the next round, with a version jump.
