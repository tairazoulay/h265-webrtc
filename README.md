# Stream H265 video using WebRTC

This documentation provides an overview and explanation of the Node.js server code used to implement a WebRTC streaming server with real-time video transmission and signaling. 

## Installation

Make sure you have Node.js and npm installed on your system. You also need to install the required packages by running the following command in the terminal:

```bash
npm install
```

## Code Explanation

The provided code implements a WebRTC streaming server that establishes a WebRTC connection between peers for video streaming. It uses the Express framework for handling HTTP requests, WebRTC functionality, and GStreamer for video streaming.

1. Initialize a WebRTC peer connection:
```javascript
const peerConnection = new WebRTC.RTCPeerConnection();
```

2. Set up the GStreamer pipeline for video streaming:
```javascript
const pipeline = new gstreamer.Pipeline(`uridecodebin uri=rtsp://localhost:8554/mystream name=source ! \
      videoconvert  ! jpegenc ! appsink name=sink max-buffers=3 drop=true emit-signals=true sync=false`);
```

3. Start the video streaming pipeline:
```javascript
var appsink = pipeline.findChild('sink');
pipeline.play();
```

4. Define the `onPull` function to send video data over the data channel:
```javascript
function onPull(buf) {
    dcChannel.send(buf);
    setTimeout(() => appsink.pull(onPull), 1);
}
```

5. Set up data channel event listeners to start sending video data:
```javascript
peerConnection.ondatachannel = ((dc) => {
    dcChannel = dc.channel;
    appsink.pull(onPull);
});
```

6. Implement endpoints for SDP exchange and ICE candidate exchange:
- `/sdp`: Handles SDP exchange between peers.
- `/ice`: Handles ICE candidate exchange between peers.
- `/send`: A test endpoint to send a message over the data channel.

12. Start the HTTP server on a specified port:
```javascript
const PORT = 5000;
server.listen(port, () => {
    console.log(`Server is running on port ${PORT}`);
});
```

## Usage

1. Start the server using the following command:
```bash
node server.js
```

2. Access the server's API endpoints to establish a WebRTC connection and start streaming video.

3. The server uses the GStreamer pipeline to receive video data and sends it over the established WebRTC connection.

## Note

This documentation provides an overview of the provided code. You may need to modify and adapt the code to suit your specific use case and requirements.

```

Please make sure to adjust any paths, URLs, and configurations according to your specific use case. Additionally, ensure that you have the required dependencies and prerequisites installed to run the server successfully.
