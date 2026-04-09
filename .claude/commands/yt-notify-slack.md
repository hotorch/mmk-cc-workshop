# YouTube 영상 요약 Slack 알림

YouTube 영상 요약 결과를 Slack #91-ai-trends 채널에 전송한다.

## 인자

$ARGUMENTS = 아래 형식의 영상 정보 (JSON 또는 자유 텍스트)

## 실행 절차

1. $ARGUMENTS에서 영상 정보를 파싱한다:
   - title: 영상 제목
   - channelName: 채널명
   - url: 영상 URL
   - summary: 요약 내용 (핵심 포인트들)
   - oneliner: 한줄 요약

2. Slack MCP 도구 `slack_send_message`를 사용하여 메시지를 전송한다:
   - channel_id: `C0AS5FP9N0Z`
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

## 주의사항
- Slack 채널 ID는 `C0AS5FP9N0Z` (#91-ai-trends, private channel)
- 메시지는 Slack mrkdwn 포맷을 사용한다 (*bold*, _italic_)
- 너무 긴 요약은 Slack에서 읽기 어려우므로 간결하게 작성한다
