# 🩸 Advanced Task: Video Transcoding (FFmpeg + Jobs)

### 📌 Problem Description
**"User uploads MP4. Convert to HLS with progress."**
Like YouTube.

**Requirements**:
1.  **Library**: `php-ffmpeg` or `protone-media/laravel-ffmpeg`.
2.  **Job**: `ProcessVideo`.
3.  **Storage**: S3 (Private upload, Public processed).
4.  **Queue**: `redis` (timeout: 10 mins).
5.  **Bonus**: Real-time progress bar (Pusher).

---

### 💻 Solution (Job)

```php
// FFMpeg::fromDisk('s3')
//     ->open($this->video->path)
//     ->exportForHLS()
//     ->onProgress(function ($percentage) {
//         broadcast(new VideoEncodingProgress($this->video, $percentage));
//     })
//     ->toDisk('s3')
//     ->save("videos/{$this->video->id}/playlist.m3u8");

// $this->video->update(['status' => 'ready']);
```

**Why this is hard?**
1.  FFmpeg takes memory/CPU.
2.  Timeout handling (`$timeout = 3600`).
3.  Cleaning up temp files.

---

### 🧪 Test (Mocking)
`FFMpeg::fake()` -> assertExported().
