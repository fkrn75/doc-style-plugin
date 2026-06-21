# doc-style

일관된 사내 스타일로 **읽는 HTML 문서**(회의록·매뉴얼·리포트·사용설명서)를 생성하는 Claude Code 플러그인.

매번 스타일을 새로 디자인하지 않고 **고정된 디자인 시스템**을 그대로 재사용한다. 결과물은 외부 의존이 없는 **자기완결 단일 HTML**이며, A4 인쇄 시 단락이 페이지 경계에서 잘리지 않고 모바일에서 반응형으로 변한다.

> **다른 PC·다른 계정·Cowork에서 쓰려면** → [docs/setup-guide.html](docs/setup-guide.html) 참고

## 호출

```
/doc-style:문서 <요청>
```

예) `/doc-style:문서 이번 주 회의록 정리해줘`

## 설치

### 로컬 (모든 프로젝트에서 영구)

```
/plugin marketplace add https://github.com/fkrn75/doc-style-plugin
/plugin install doc-style@doc-style
```

### 임시 테스트 (그 세션만)

```
claude --plugin-dir <이 폴더 경로>
```

### Cowork / 팀 공유

프로젝트의 `.claude/settings.json`에 등록:

```json
{
  "extraKnownMarketplaces": {
    "doc-style": {
      "source": { "source": "github", "repo": "fkrn75/doc-style-plugin" }
    }
  }
}
```

## 구조

```
.claude-plugin/
  plugin.json          매니페스트
  marketplace.json     설치용 마켓플레이스 정의
skills/문서/
  SKILL.md             생성 지침 (스타일 재발명 금지 규약)
  doc-style.css        디자인 시스템 (단일 출처)
  template.html        블록 마크업 골격
```

## 디자인 시스템

- 포인트 네이비 `#2b4c8c`, 회청 배경 `#f4f6fa`, Pretendard + `word-break:keep-all`
- 블록: 그라데이션 헤더(`header.doc`) · 요약(`.summary`) · 번호 섹션(`section.card` + `.num`) · 콜아웃(`.callout.info`/`.warn`) · 체크리스트(`.checklist`) · 표(`table.spec`)
- A4 인쇄 단락 보존(`break-inside:avoid`) · 모바일 `@media (max-width:640px)` 반응형
- **색만 바꾸려면** `skills/문서/doc-style.css`의 `:root`에서 `--accent` / `--head-from` / `--head-to`만 교체

## 라이선스

자유롭게 복사·수정.
