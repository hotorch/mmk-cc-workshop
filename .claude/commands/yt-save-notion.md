# YouTube 영상 요약 Notion DB 저장

YouTube 영상 요약 결과를 Notion "YouTube Channel Archive" 데이터베이스에 저장한다.

## 인자

$ARGUMENTS = 아래 형식의 영상 정보 (JSON 또는 자유 텍스트)

## 실행 절차

1. $ARGUMENTS에서 영상 정보를 파싱한다:
   - title: 영상 제목
   - channelName: 채널명
   - url: 영상 URL
   - published: 게시일 (ISO 날짜)
   - summary: 요약 핵심 포인트들
   - oneliner: 한줄 요약
   - category: 카테고리 (AI / Automation / Tutorial / Other)
   - thumbnailUrl: 썸네일 이미지 URL (https://i.ytimg.com/vi/{videoId}/hqdefault.jpg)

2. Notion MCP 도구 `notion-create-pages`를 사용하여 페이지를 생성한다:
   - parent: `{ "database_id": "450e80f6d54c41c8a04a374390957e0f" }`
   - properties:
     - 제목: {title}
     - 채널: {channelName}
     - 영상 URL: {url}
     - 게시일: {published}
     - 카테고리: AI, Automation, Tutorial, Other 중 적절한 값
     - 요약: {oneliner}
     - 상태: "New"
   - content (페이지 본문):
     ```
     ![썸네일]({thumbnailUrl})

     ## 영상 정보
     - 채널: {channelName}
     - 게시일: {published}
     - URL: {url}

     ## 핵심 요약
     • {포인트 1}
     • {포인트 2}
     • {포인트 3}

     ## 한줄 요약
     {oneliner}
     ```
     > **중요**: 썸네일 이미지를 페이지 본문 최상단에 배치해야 Notion 갤러리 뷰에서 미리보기로 표시된다.

## Notion DB 정보
- Database ID: `450e80f6d54c41c8a04a374390957e0f`
- Data Source ID: `dc25d0a7-a199-48e1-82a8-d7640792c2ce`
- Parent Page: "🎍 타 채널 아카이브"

## 카테고리 분류 기준
- **AI**: AI 모델, LLM, GPT, Claude, Gemini 등 AI 관련 영상
- **Automation**: 자동화, 워크플로우, n8n, Make 등 자동화 관련 영상
- **Tutorial**: 튜토리얼, 가이드, 사용법 등 교육 관련 영상
- **Other**: 위 카테고리에 해당하지 않는 영상
