# Kawa 🏞️

Kawa (川) is a simple livestream application that consists of [`kawa-ingest 🛤`](https://github.com/sanstzu/kawa-ingest) and [`kawa-transcoder ⚙️`](https://github.com/sanstzu/kawa-transcoder), fully written in Rust 🦀.

# Kawa-Ingest 🛤

The ingest service is responsible for receiving RTMP streams. It is made using `rml-rtmp` and `tokio` for async handling of incoming streams.

The raw video and audio bytes is being used to extract NAL units and ADTS frames, which are then sent to the transcoder service through gRPC stream.

> The ingest service is designed to be used with [OBS Studio](https://obsproject.com/), a popular open-source software for video recording and live streaming. As of now, it only supports `h264` and `aac` codec for video and audio respectively.

# Kawa-Transcoder ⚙️

The transcoder service uses `ffmpeg` to transcode the incoming gRPC stream to HLS format.

It also utilizes `aws-cli` to upload the transcoded media files to an S3 bucket that serves as a CDN for the HLS stream.

## Contact 📧

Since this is a personal project (first time using Rust in this large scale project), I'm open to any suggestions or feedback.

Feel free to reach out to me at my email 📧: [claytonfernalo@gmail.com](mailto:claytonfernalo@gmail.com) or Discord 💬: san.s
