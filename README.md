# recordervue

In-browser video recording for Vue 3, built on RecordRTC and video.js.

Records from the camera with configurable limits, previews the result, and hands back the recording as a blob URL with its mime type — ready to upload or play.

## Features

- Configurable maximum duration, file size and video height
- Aspect-ratio selection (9:16, 16:9, 1:1) before recording
- Camera and microphone device selection
- Live preview and playback of the recording (video.js)
- Clear error states and a resettable recorder
- Emits the recorded video as a blob URL with its mime type

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
<Player
  :maxDuration="60"
  :maxFileSize="200"
  :maxHeight="720"
  @videoRecorded="onVideoRecorded"
  @error="onError"
/>
```

`videoRecorded` delivers the finished video's blob URL and mime type; `error` reports permission and device problems.
