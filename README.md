# picture-always-on-top

A single-file web tool that turns uploaded images into always-on-top
Picture-in-Picture windows using the
[Document Picture-in-Picture API](https://developer.mozilla.org/en-US/docs/Web/API/Document_Picture-in-Picture_API).

## Features

- Upload any local image and preview it instantly
- One click to pop the image into a floating, always-on-top window
- The image auto-scales to fit the PiP window without distortion
- Zero dependencies — just one HTML file
- Robust against edge cases: rapid clicks, image switching during
  window creation, and stale Blob URLs

## Usage

1. Save the code as `index.html`.
2. Open it in a supported browser.
3. Click **Choose Image** to pick an image, then click
   **Pin in Picture-in-Picture**.
4. Drag the floating window anywhere; it stays above other windows.

## Browser Support

Requires the Document Picture-in-Picture API:

| Browser        | Status                          |
| -------------- | ------------------------------- |
| Chrome / Edge  | 116+ ✅ (Full support)          |
| Firefox        | 151+ ⚠️ (Preview support)      |
| Safari         | Not supported ❌                |
| Chrome Android | Not supported ❌                |

> **Note:** Firefox 151+ supports the API on desktop platforms, but MDN
> currently lists it as **preview support** rather than full support.
> Firefox for Android 151+ is also listed with preview support.

## Limitations

- **One PiP window per tab.** The spec limits each tab to one PiP
  window. Chrome and Edge further restrict this to **one globally
  across all tabs**; opening a new one replaces the previous, and a
  PiP window in another tab will block this page's request.
- **Always on top is by design.** The API has no option to disable it;
  the window cannot be pushed behind other windows without
  system-level tools.

## How It Works

The core call is `documentPictureInPicture.requestWindow()`, which
returns a real `Window` object. Styles and an `<img>` element are
injected into it, and the window stays on top automatically.

Image data is passed as a Blob URL created with
`URL.createObjectURL()`, which keeps the original page layout
untouched and is revoked when no longer needed. A mutex flag
(`isOpeningPip`) guards the async `requestWindow()` call so that
the API is never invoked concurrently.

## Project Structure
```text
.
├── index.html # The entire tool: markup, styles, and logic
└── README.md
```
