# Kawa 🏞️

Kawa (川) is a simple livestream application that consists of [`kawa-ingest 🛤`](https://github.com/sanstzu/kawa-ingest), [`kawa-transcoder ⚙️`](https://github.com/sanstzu/kawa-transcoder), and [`kawa-session-manager 📓`](https://github.com/sanstzu/kawa-session-manager) fully written in Rust 🦀.

# Kawa-Ingest 🛤

The ingest service is responsible for receiving RTMP streams. It is made using `rml-rtmp` and `tokio` for async handling of incoming streams.

The raw video and audio bytes are used to extract NAL units and ADTS frames, which are then sent to the transcoder service through gRPC stream.

> The ingest service is designed to be used with [OBS Studio](https://obsproject.com/), a popular open-source software for video recording and live streaming. Currently, it only supports `h264` and `aac` codec for video and audio respectively.

# Kawa-Transcoder ⚙️

The transcoder service employs `ffmpeg` to transcode the incoming gRPC stream to HLS format (MPEG-TS container).

It also utilizes `aws-cli` to upload the transcoded media files to an S3 bucket that serves as a CDN for the HLS stream. As for now, the stream will be published to `[AWS_S3_OUTPUT_URI]/[stream_key]`. For better practice, it would be better to have a key-value store to map a stream key to a particular publish path (e.g. Redis).

To function properly, make sure the IAM credentials used possess the ability to write/delete files into the AWS S3 bucket.

# Kawa-Session-Manager 📓

The session manager services can be interacted with through REST API to generate stream keys that can be used for the ingest server and also act as a gRPC server to handle requests from the ingest service (verifying stream keys). It uses Redis to store the list of valid stream keys and its corresponding output path.

Here are the supported list of commands:
|Method|Path|Query|Request Body|Description|
|---|---|---|---|---|
|GET|`/stream`|`stream_key`|-|Returns the path (relative to RTMP) for a corresponding `stream_key`|
|DELETE|`/stream`|`stream_key`|-|Deletes the corresponding `stream_key`|
|POST|`/stream`|-|`publish_path`|Returns a stream key for a following path|

# Deployment

For convenience, you can use Docker for deployment. After filling up the necessary environment variables (refer to [`.env.example`](https://github.com/sanstzu/kawa/blob/main/.env.example)), run the following command to build the images:
```bash
docker compose build
```

Once it is built, simply run the following command to start the server:

```bash
docker compose up --detach
```

## Contact 📧

Since this is a personal project (and my first experience using Rust on such a scale), I'm very much open to any suggestions or feedback.

Please feel free to reach out to me at my email 📧: [claytonfernalo@gmail.com](mailto:claytonfernalo@gmail.com) or Discord 💬: san.s

## To-Do 📝

- [ ] README.md for both services.
- [X] Create Dockerfile for both services (currently for `kawa-ingest` only).
- [X] Create docker-compose file for both services.
- [ ] Add tests.
- [ ] Improve from 30 seconds latency.
- [ ] Create full-stack application.

## References 📚

- [rust-media-libs](https://github.com/KallDrexx/rust-media-libs)
- https://yumichan.net/video-processing/video-compression/introduction-to-h264-nal-unit/
- https://zhuanlan.zhihu.com/p/83454344
- https://zhuanlan.zhihu.com/p/83346973
- https://doc-kurento.readthedocs.io/en/latest/knowledge/h264.html
