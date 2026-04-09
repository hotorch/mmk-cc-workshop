# YouTube 채널 모니터링 - 메인 오케스트레이터

4개 YouTube 채널의 새 long-form 영상을 감지하고, 자막을 요약하여 Slack 알림 + Notion DB 저장을 수행하는 메인 파이프라인이다.

## 모니터링 채널
- Nate Herk | AI Automation (UC2ojq-nuP8ceeHqiroeKhBA)
- Nick Saraev (UCbo-KbSjJDG6JWQ_MTZ_rNA)
- AICodeKing (UC0m81bQuthaQZmFbXEY9QSw)
- Nick Puru | AI Automation (UC4FK5DEcMLB3CyJcbJfZEJA)

## 실행 절차

### Phase 1: 새 영상 수집

1. `data/channels.json` 파일을 읽어서 채널 목록을 로드한다.
2. `data/state.json` 파일을 읽어서 이미 처리된 video ID 목록을 로드한다.
3. 각 채널의 YouTube RSS 피드를 WebFetch로 가져온다:
   - URL: `https://www.youtube.com/feeds/videos.xml?channel_id={채널ID}`
   - prompt: "Extract all video entries. Return a JSON array with objects having: videoId, title, published (ISO date string). Return ONLY the raw JSON array."
4. RSS 결과에서 이미 처리된 video ID를 제외한다 (state.json의 processed_videos 확인).
5. 새 영상 각각에 대해 `mmk youtube videotype https://youtube.com/watch?v={videoId}` 실행한다.
6. videotype이 "short"인 영상은 제외하고 long-form만 남긴다.

### Phase 2: 각 새 영상 처리

새로 발견된 long-form 영상 각각에 대해 순서대로:

1. **자막 추출**: `mmk youtube transcript https://youtube.com/watch?v={videoId} --format text` 실행
2. **메타데이터 조회**: `mmk youtube metadata https://youtube.com/watch?v={videoId} -o json` 실행
3. **요약 생성**: 추출된 자막을 바탕으로 한국어 요약을 생성한다:
   - 핵심 포인트 3~5개
   - 한줄 요약 (50자 이내)
   - 카테고리 분류 (AI / Automation / Tutorial / Other)
4. **Slack 알림**: Slack MCP `slack_send_message` 도구로 알림을 보낸다:
   - channel_id: `C0AS5FP9N0Z` (#91-ai-trends)
   - 메시지 포맷:
     ```
     🎬 *새 YouTube 영상 알림*
     
     *채널:* {channelName}
     *제목:* {title}
     🔗 {url}
     
     📝 *핵심 요약:*
     • {포인트 1}
     • {포인트 2}
     • {포인트 3}
     
     💡 *한줄 요약:* {oneliner}
     ```
5. **Notion 저장**: Notion MCP `notion-create-pages` 도구로 DB에 저장한다:
   - parent: `{ "database_id": "450e80f6d54c41c8a04a374390957e0f" }`
   - properties:
     - 제목: {title}
     - 채널: {channelName}
     - 영상 URL: https://youtube.com/watch?v={videoId}
     - 게시일: {published} (ISO 날짜)
     - 카테고리: 분류된 카테고리
     - 요약: {한줄 요약}
     - 상태: "New"
   - content (페이지 본문): **썸네일 이미지를 본문 최상단에 배치**한 후 전체 요약 내용 마크다운
     ```
     ![썸네일](https://i.ytimg.com/vi/{videoId}/hqdefault.jpg)

     ## 영상 정보
     ...
     ```
     > 썸네일을 본문 첫 번째 이미지로 넣어야 Notion 갤러리 뷰에서 미리보기로 노출된다.

### Phase 3: 상태 업데이트

1. 처리 완료된 video ID들을 `data/state.json`의 `processed_videos` 배열에 추가한다.
2. `last_check`를 현재 시간(ISO 8601)으로 업데이트한다.
3. 수정된 state.json을 파일에 저장한다.

### Phase 4: 결과 보고

처리 결과를 아래 형식으로 출력한다:

```
## ✅ YouTube 채널 모니터링 완료

- 체크 시간: {현재 시간}
- 총 확인 영상: {N}개
- 새 long-form 영상: {N}개
- Shorts 스킵: {N}개
- 이미 처리됨 스킵: {N}개

### 처리된 영상:
1. [{title}]({url}) - {channelName} → Slack ✅ Notion ✅
2. ...

새 영상이 없으면: "새로운 long-form 영상이 없습니다."
```

## 오류 처리
- 자막 추출 실패 시: 메타데이터만으로 간단한 알림을 보내고, Notion에는 "자막 없음"으로 기록한다
- RSS 피드 실패 시: 해당 채널을 건너뛰고 나머지 채널을 계속 처리한다
- Slack/Notion 실패 시: 에러를 로그로 남기고 다음 영상으로 진행한다

## Notion DB 정보
- Database ID: `450e80f6d54c41c8a04a374390957e0f`
- Data Source: `dc25d0a7-a199-48e1-82a8-d7640792c2ce`

## 스케줄 실행
이 스킬을 `/loop 1h /check-youtube` 명령으로 1시간마다 자동 실행할 수 있다.
