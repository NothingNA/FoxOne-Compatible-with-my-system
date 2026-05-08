# Installation

### 1. Enable custom stylesheets

In Firefox, go to `about:config` and set:

```
toolkit.legacyUserProfileCustomizations.stylesheets = true
```

### 2. Find your profile folder

In Firefox, go to `about:support` and click **Open Profile Folder**.

### 3. Copy the files

Create a `chrome` folder inside your profile folder if it doesn't exist, then copy these files into it:

- [`userChrome.css`](../userChrome.css) — browser UI styling
- [`userContent.css`](../userContent.css) — new tab / home page colors

### 4. Restart Firefox

The theme applies on restart.

### Color Theme

FoxOne includes a built-in Gruvbox Dark color theme that activates automatically in dark mode. No separate extension needed.

If you use a different system theme or want light mode, the color theme section in `userChrome.css` only applies inside `@media (prefers-color-scheme: dark)` and won't interfere.
