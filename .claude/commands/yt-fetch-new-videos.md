# YouTube 새 영상 목록 가져오기

모니터링 대상 YouTube 채널들에서 아직 처리하지 않은 새로운 long-form 영상 목록을 가져온다.

## 실행 절차

1. `data/channels.json` 파일을 읽어서 채널 목록을 확인한다.
2. 각 채널의 RSS 피드를 WebFetch로 가져온다:
   - URL 형식: `https://www.youtube.com/feeds/videos.xml?channel_id={채널ID}`
   - WebFetch prompt: "Extract all video entries as JSON array with fields: videoId, title, published (ISO date), channelName. Return ONLY the JSON array, no other text."
3. `data/state.json` 파일을 읽어서 이미 처리된 video ID 목록을 확인한다.
4. 이미 처리된 영상은 제외하고 새 영상만 필터링한다.
5. 새 영상 각각에 대해 `mmk youtube videotype https://youtube.com/watch?v={videoId}`를 실행하여 long-form인지 확인한다.
6. Short는 제외하고 long-form 영상만 남긴다.
7. 각 영상에 대해 썸네일 URL을 생성한다:
   - 형식: `https://i.ytimg.com/vi/{videoId}/hqdefault.jpg`
   - hqdefault (480x360)는 모든 영상에서 항상 사용 가능하다.
8. 최종 결과를 아래 형식으로 출력한다:

```json
{
  "new_videos": [
    {
      "videoId": "...",
      "title": "...",
      "channelName": "...",
      "channelId": "...",
      "published": "...",
      "url": "https://youtube.com/watch?v=...",
      "thumbnailUrl": "https://i.ytimg.com/vi/.../hqdefault.jpg"
    }
  ],
  "total_checked": 0,
  "new_count": 0,
  "skipped_shorts": 0,
  "skipped_processed": 0
}
```
