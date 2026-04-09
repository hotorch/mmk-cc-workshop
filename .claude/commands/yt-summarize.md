# YouTube 영상 자막 추출 및 요약

개별 YouTube 영상의 자막을 추출하고 핵심 내용을 한국어로 요약한다.

## 인자

$ARGUMENTS = YouTube 영상 URL (예: https://youtube.com/watch?v=VIDEO_ID)

## 실행 절차

1. `mmk youtube metadata $ARGUMENTS -o json` 을 실행하여 영상 메타데이터를 조회한다.
   - 제목, 채널명, 게시일, 영상 길이 등을 확인한다.
2. `mmk youtube transcript $ARGUMENTS --format text` 를 실행하여 자막을 추출한다.
3. 추출된 자막을 바탕으로 아래 형식의 한국어 요약을 생성한다:

## 요약 형식

```
## 영상 정보
- 제목: {title}
- 채널: {channel}
- 게시일: {published_date}
- 길이: {duration}
- URL: {url}

## 핵심 요약 (3~5개 포인트)
• [핵심 포인트 1]
• [핵심 포인트 2]
• [핵심 포인트 3]

## 한줄 요약
[영상 전체를 한 문장으로 요약]
```

## 요약 규칙
- 반드시 한국어로 요약한다
- 핵심 포인트는 3~5개로 제한한다
- 기술적 용어는 원문(영어)을 괄호로 병기한다 (예: 프롬프트 엔지니어링(Prompt Engineering))
- 한줄 요약은 50자 이내로 작성한다
