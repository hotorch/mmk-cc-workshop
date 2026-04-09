# Claude Code 워크샵 스타터

강의 실습용 Claude Code 워크샵 스타터 프로젝트입니다.

## 프로젝트 목적

이 프로젝트는 [pureugong/mmk-cc-workshop](https://github.com/pureugong/mmk-cc-workshop)에서 fork된 저장소입니다.
원본 저장소를 fork하여 Claude Code 웹에서 바로 자동화 바이브 코딩을 시작할 수 있습니다.

## mmk CLI

mmk (Magic Meal Kits)는 YouTube 자막 추출, 메타데이터 조회 등을 지원하는 CLI 도구입니다.

**중요: 현재 토큰은 YouTube 전용입니다. `mmk youtube` 명령어만 사용하세요.**
`mmk notion`, `mmk paymint` 등 다른 명령어는 권한이 없어 실패합니다 (403 insufficient_scope).

### 설정

```bash
export MMK_SERVER="https://magic-meal-kits-r7fpfharja-uw.a.run.app"
export MMK_TOKEN="<강사가 제공한 토큰>"
```

### 사용 가능한 명령어

```bash
# YouTube 자막 추출
mmk youtube transcript <youtube-url>
mmk youtube transcript <youtube-url> --format json
mmk youtube transcript <youtube-url> --format srt

# YouTube 메타데이터 조회
mmk youtube metadata <youtube-url>

# YouTube 영상 타입 확인 (일반 영상 vs Short)
mmk youtube videotype <youtube-url>
```

### 사용 불가 명령어 (토큰 권한 없음)

- `mmk notion ...` — 사용 불가
- `mmk paymint ...` — 사용 불가
- `mmk threads ...` — 사용 불가

## YouTube 채널 모니터링 스킬

4개 YouTube 채널의 새 영상을 감지하고, 자막 요약 → Slack 알림 → Notion 저장을 수행하는 자동화 스킬입니다.

### 모니터링 대상 채널
- Nate Herk | AI Automation
- Nick Saraev
- AICodeKing
- Nick Puru | AI Automation

### 사용 가능한 스킬

| 스킬 | 설명 |
|------|------|
| `/check-youtube` | 전체 파이프라인 실행 (감지 → 요약 → 알림 → 저장) |
| `/yt-summarize <url>` | 개별 영상 자막 추출 및 한국어 요약 |
| `/yt-fetch-new-videos` | 새 long-form 영상 목록만 조회 |
| `/yt-notify-slack` | 요약 결과를 Slack으로 전송 |
| `/yt-save-notion` | 요약 결과를 Notion DB에 저장 |

### 스케줄 실행

```bash
# 1시간마다 자동 모니터링
/loop 1h /check-youtube
```

## 세션 시작 시

세션이 시작되면 `.claude/scripts/check-env.sh` 스크립트가 자동 실행되어 환경 정보를 출력합니다:
- 호스트명, OS, CPU, 메모리, 디스크
- Git, Python, Node, mmk 버전
- 원격 환경 여부
