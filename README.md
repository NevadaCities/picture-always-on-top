# picture-always-on-top

A single-file web tool that turns uploaded or pasted images into
always-on-top Picture-in-Picture windows using the
[Document Picture-in-Picture API](https://developer.mozilla.org/en-US/docs/Web/API/Document_Picture-in-Picture_API).

## Features

- Upload a local image file and preview it instantly
- **Paste an image directly from the clipboard** (Chromium only — see Browser Support)
- One click to pop the image into a floating, always-on-top window
- The image auto-scales to fit the PiP window without distortion
- Zero dependencies — just one HTML file
- Graceful degradation when the clipboard API or PiP API is unavailable

## Usage

1. Save the code as `index.html`.
2. Open it in a supported browser over **HTTPS** or **localhost**.
3. Load an image in one of two ways:
   - Click **Choose Image** to pick a local file, or
   - Copy an image to your clipboard, then click **Paste Image from Clipboard**.
4. Click **Pin in Picture-in-Picture** to open the floating window.
5. Drag the PiP window anywhere; it stays above other windows.

## Browser Support

### Document Picture-in-Picture

| Browser        | Status                          |
| -------------- | ------------------------------- |
| Chrome / Edge  | 116+ ✅ (Full support)          |
| Firefox        | 151+ ⚠️ (Preview support)      |
| Safari         | Not supported ❌                |

### Clipboard Image Reading

| Browser        | `navigator.clipboard.read()` | Paste Image Feature |
| -------------- | ---------------------------- | ------------------- |
| Chromium 116+  | ✅ Supported                 | ✅ Works            |
| Firefox 151+   | ❌ Not supported             | ❌ Disabled         |
| Safari         | ❌ Not supported             | ❌ Disabled         |

> **Important:** The “Paste Image from Clipboard” feature relies on
> `navigator.clipboard.read()` together with `ClipboardItem.getType()`
> for image MIME types. This API is **only available in Chromium-based
> browsers**. Firefox does not implement `clipboard.read()` for web
> content — only `readText()` is available — so the paste button is
> automatically disabled in Firefox. No error is thrown; the tool
> simply falls back to file upload only.

> **Note:** Clipboard access requires a **secure context** (HTTPS or
> `localhost`). Opening the HTML file directly via `file://` will
> disable the paste feature in all browsers.

## Limitations

- **Clipboard image reading is Chromium-only.** This is a browser API
  limitation, not a tool limitation. Firefox and Safari cannot read
  arbitrary clipboard data (including images) from web pages.
- **One PiP window per tab.** The spec limits each tab to one PiP
  window. Chrome and Edge further restrict this to **one globally
  across all tabs**; opening a new one replaces the previous, and a
  PiP window in another tab will block this page's request.
- **Always on top is by design.** The API has no option to disable it;
  the window cannot be pushed behind other windows without
  system-level tools.

## How It Works

### Picture-in-Picture

`documentPictureInPicture.requestWindow()` returns a real `Window`
object. Styles and an `<img>` element are injected into it, and the
window stays on top automatically. A mutex flag (`isOpeningPip`)
guards the async call so the API is never invoked concurrently.

### Clipboard

`navigator.clipboard.read()` returns an array of `ClipboardItem`
objects. The code iterates over each item’s `types` array looking for
a MIME type starting with `image/`, then calls `getType()` to obtain
the image `Blob`. The Blob is passed to the same `loadImageBlob()`
function used by file upload, which creates a Blob URL and updates
the preview.

Clipboard errors are handled with specific messages for the common
cases: permission denied (`NotAllowedError`), empty clipboard, no
image in the clipboard, and insecure context (`SecurityError` /
`NotFoundError`).

## Project Structure
```text
.
├── index.html # The entire tool: markup, styles, and logic
└── README.md
```
