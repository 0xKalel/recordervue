# Vue Video Recorder Component

A Vue.js component for recording video with customizable settings. This component allows users to record videos, handle video data, and manage recording configurations such as aspect ratio, duration, and file size.

## Features

- Record videos with configurable maximum duration, file size, and height.
- Supports aspect ratio selection for video recording.
- Handles errors and provides feedback to users.
- Allows resetting the video recorder state.
- Provides recorded video data with blob URL and mime type.

## Installation

1. **Clone the repository:**

   ```bash
   git clone https://github.com/yourusername/your-repo-name.git
   cd your-repo-name
   ```

2. **Install dependencies:**

   Make sure you have [Node.js](https://nodejs.org/) and [npm](https://www.npmjs.com/) installed. Run:

   ```bash
   npm install
   ```

3. **Build the project:**

   ```bash
   npm run build
   ```

4. **Use the component in your Vue.js application:**

   Import the component and use it as follows:

   ```vue
   <template>
     <div>
       <VideoRecorder
         :max-duration="10"
         :max-height="500"
         :max-file-size="200"
         @video-recorded="handleVideoRecorded"
         @error="handleError"
       />
     </div>
   </template>

   <script setup>
   import VideoRecorder from 'path-to-your-component/VideoRecorder.vue';

   const handleVideoRecorded = (videoData) => {
     console.log('Recorded video data:', videoData);
   };

   const handleError = (error) => {
     console.error('Error:', error);
   };
   </script>
   ```

## Component Properties

- `maxDuration` (Number, required): The maximum duration of the video recording in seconds.
- `maxHeight` (Number, required): The maximum height of the video recorder in pixels.
- `maxFileSize` (Number, required): The maximum file size of the recorded video in megabytes.

## Events

- `videoRecorded` (Event): Emitted when a video is successfully recorded. Provides the recorded video data.
- `error` (Event): Emitted when an error occurs during recording or video processing.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
