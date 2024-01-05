This repo provides a minimal reproduction of what I believe is incorrect functionality from the new "Third-party Storage Partitioning" flag.

Context:
Assuming I'm reading the spec correctly, cross-origin iframes should be able to access localStorage assuming `document.hasStorageAccess()` is `true`. If it is not, a developer should be able to prompt a user for access to this functionality via `document.requestStorageAccess()`. However, it appears that right now `document.hasStorageAccess()` returns `true` even in cases where a given iframe does not have storage access. This renders the iframe unable to share local storage context with a top level window of the same domain.

The repro setup to prove this to be the case requires 2 different domains - This example uses `4storia.github.io` and `blakelee.github.io`.

`third-party-cookie-repro.html` - This is the top level file, loaded from `4storia.github.io`. It contains an iframe that loads against `blakelee.github.io`. It also has a link to open a new window against `blakelee.github.io`.
`third-party-cookie-frame.html` - This is the contents of the frame rendered on `4storia.github.io`. Every second it refreshes the result of `document.hasStorageAccess()`, as well as the contents of it's localStorage. It also has a button that will write a test value to localStorage
`third-party-cookie-popup.html` - This is a standalone window that opens against `blakelee.github.io`. Like the above, it also refreshes it's `localStorage` contents every second, and has a button that will write a test value to localStorage

Repro:
- Enable "Third-party Storage Partitioning" via chrome://flags/
- Navigate to https://4storia.github.io/third-party-cookie-repro.html
- Click the link to open a standalone window to https://blakelee-pendo.github.io/third-party-cookie-popup.html
- Observe that `document.hasStorageAccess()` returns `true` in the iframe
- Click the test localStorage write button in either the iframe or standalone window on `blakelee.github.io`

Expected:
- localStorage access is shared between the iframe and top level window, and it's contents are identical

Actual:
- localStorage access is NOT shared, and the iframe's localStorage is entirely separate from the top level window