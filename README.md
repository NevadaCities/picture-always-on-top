# picture-always-on-top

A single-file web tool that turns uploaded images into always-on-top Picture-in-Picture windows using the Document Picture-in-Picture API.

## Features

- Upload any local image and preview it instantly
- One click to pop the image into a floating, always-on-top window
- The image auto-scales to fit the PiP window without distortion
- Zero dependencies — just one HTML file
- Handles edge cases: concurrent clicks, image switching during window creation, and stale Blob URLs

## Usage

1. Save the code as `index.html`.
2. Open it in a supported browser.
3. Click **选择图片** to pick an image, then click **在画中画窗口中置顶**.
4. Drag the floating window anywhere; it stays above other windows.

## Browser Support

Requires the [Document Picture-in-Picture API](https://developer.mozilla.org/en-US/docs/Web/API/Document_Picture-in-Picture_API):

| Browser | Status |
| --- | --- |
| Chrome / Edge | 116+ ✅ |
| Firefox | Not supported ❌ |
| Safari | Not supported ❌ |

## Limitations

- **One PiP window globally.** Chrome and Edge allow only one PiP window across all tabs. Opening a new one replaces the previous, and a PiP window in another tab will block this page's request.
- **Always on top is by design.** The API has no option to disable it; the window cannot be pushed behind other windows without system-level tools.

## How It Works

The core call is `documentPictureInPicture.requestWindow()`, which returns a real `Window` object. Styles and an `<img>` element are injected into it, and the window stays on top automatically. Image data is passed as a Blob URL created with `URL.createObjectURL()`, which keeps the original page layout untouched and is revoked when no longer needed.
