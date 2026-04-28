# engineering-notes - Claude Code Context

엔지니어링 관련 이론을 이해하기 위한 노트.

## 원칙

- 모든 본문은 **유저가 직접 작성**한다. LLM은 보일러플레이트(파일 생성, 인덱스 갱신, 메타데이터, 마크다운 정리)만 돕는다
- "설명할 수 있을 때"만 글로 남긴다. 책/아티클 요약 금지
- 짧고 거칠어도 됨. 단 "왜 그런가"까지 닿아야 한다

## Claude의 역할

### Don't
- 본문을 대신 쓰거나 초안을 잡아주지 않는다
- 유저가 모호하게 말한 개념을 매끄럽게 풀어주지 않는다
- 유저가 먼저 꺼내기 전에 구조를 제안하지 않는다

### Do
- 유저가 쓴 글에서 구멍이나 추론으로 넘어간 부분을 찾아 질문한다
- "직접 확인했는가"를 묻는다
- 파일 생성, 디렉토리 정리, README 인덱스 갱신을 돕는다

## 구조

- 모든 글은 `notes/` 아래 평평하게
- 파일명: `{카테고리}-{주제}.md` (예: `java-jvm-memory-model.md`)
- 이미지는 `images/` 아래 평평하게, 글이랑 같은 prefix 사용 (예: `java-jvm-heap.excalidraw.png`)
- 새 글 추가 시 `README.md`의 인덱스 섹션 갱신

## 메타데이터 (frontmatter)

모든 글 최상단에 YAML frontmatter.

```yaml
---
date: 2026-04-28
tags: [java, jvm]
status: draft   # draft | done ("설명 가능" 통과 시 done)
sources:
  - "책/아티클/영상 출처"
---
```

- `status: done`이 졸업 게이트. 통과한 글만 README 인덱스에 노출하는 흐름도 가능
- `tags`는 교차 분류용. 한 글이 여러 영역에 걸칠 때 사용 (예: `[java, concurrency]`)
- `sources`는 옵션. 본문에서 다시 풀어쓴 입력 출처 기록

## 다이어그램

### Mermaid (텍스트 기반)
정형 다이어그램(시퀀스/플로우/ER/클래스)은 마크다운 본문에 직접 작성.

````markdown
```mermaid
sequenceDiagram
  Client->>Server: 요청
  Server-->>Client: 응답
```
````

### Excalidraw (자유 배치)
박스/화살표 자유롭게 배치할 때 사용. **편집 가능한 PNG**로 저장하는 게 핵심.

워크플로:
1. https://excalidraw.com 접속 (또는 VSCode `Excalidraw` 확장 설치)
2. 다이어그램 그리기
3. 메뉴 → Export image → PNG → **"Embed scene" 체크** → 다운로드
4. `images/{글-prefix}-{설명}.excalidraw.png` 으로 저장
5. 마크다운에서 `![설명](../images/java-jvm-heap.excalidraw.png)` 로 참조

편집할 때: 같은 `.excalidraw.png` 파일을 excalidraw.com에 드래그하면 원본 장면 그대로 다시 편집 가능. 수정 후 같은 방식으로 재저장.

### 출처 원칙
- 책/아티클 그림 그대로 캡처 금지. 이해한 걸 본인이 다시 그린다
- 정 인용해야 하면 출처 명시 + 본문에서 "내가 이해한 건…"으로 다시 풀어쓰기
- 디버깅 스크린샷, 측정 결과(그라파나 등)는 OK
