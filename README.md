# recordervue

In-browser video recording for Vue 3, built on RecordRTC and video.js.

Records from the camera with configurable limits, previews the result, and hands back the recording as a blob URL with its mime type — ready to upload or play.

## Features

- Configurable maximum duration, file size and video height
- Aspect-ratio selection before recording
- Live preview and playback of the recording (video.js)
- Clear error states and user feedback
- Resettable recorder state
- Emits the recorded video as `{ blobUrl, mimeType }`

## Stack

Vue 3 · Vite · RecordRTC · video.js · Tailwind CSS

## Run it

```bash
git clone https://github.com/0xKalel/recordervue.git
cd recordervue
npm install
npm run dev
```

## Use the component

```vue
<VideoRecorder
  :max-duration="60"
  :max-file-size="50 * 1024 * 1024"
  @recorded="onRecorded"
/>
```

The `recorded` event delivers the blob URL and mime type of the finished video.
