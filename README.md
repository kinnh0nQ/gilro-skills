# GILRO Skills

길로(GILRO)에서 쓰는 Claude 스킬 모음입니다.

## 설치

```bash
npx skills add https://github.com/kinnh0nQ/gilro-skills --skill gilroro-instagram
```

같은 명령어를 다시 실행하면 최신 버전으로 덮어씁니다.
수동 설치는 스킬 폴더를 `~/.claude/skills/` 아래에 복사하면 됩니다.

## 스킬

| 스킬 | 설명 |
|---|---|
| [`gilroro-instagram`](./gilroro-instagram) | 원고를 받아 길로로(@gilroro.mag) 인스타그램 카드뉴스 1080×1350을 Figma에 만듭니다. 커버·피드·엔딩 실측 스펙과 이미지 처리 규칙이 들어 있습니다. |

## 요구 사항

- Figma MCP 연결 (Figma 편집 권한이 있는 계정)
- 커버·피드는 별도 가이드 파일 없이 만들어집니다.
- 마지막장(엔딩)만 길로로 프로필이 이미지라서, 작업할 Figma 파일 안에 기존 엔딩 프레임(1080×1350)이 하나 있어야 복제해서 씁니다.

## 라이선스

[MIT](./LICENSE)
