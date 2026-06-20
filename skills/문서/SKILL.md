---
name: 문서
description: 사용자가 명시적으로 호출(`/doc-style:문서 ...`)할 때만 사용한다. "회의록 만들어줘", "문서로 정리해줘", "매뉴얼 써줘" 같은 일반 자연어 요청에는 절대 자동 발동하지 말 것 — 반드시 명시적 트리거가 있을 때만. 호출되면 회의록·매뉴얼·리포트·사용설명서를 고정 사내 스타일(그라데이션 헤더·번호 섹션·콜아웃·체크리스트·표, A4 인쇄·모바일 반응형)의 자기완결 HTML로 생성한다.
---

# 일관 스타일 HTML 문서 생성

## 발동 조건 (중요)

이 스킬은 **명시적 트리거가 있을 때만** 동작한다 — `/doc-style:문서 ...` 슬래시 호출(또는 이 PC 환경의 `!문서 ...`).
접두사 없이 "회의록 만들어줘", "매뉴얼 써줘", "문서로 정리해줘" 같은 일반 요청에는 **발동하지 말 것**. 명시 트리거가 없으면 평범하게 대응한다.

## ⚠️ 정확히 이 디자인으로 — 임의 변형 금지

목적은 "매번 똑같은 모양·색"이다. 아래 **전체 CSS**와 **HTML 골격**을 그대로 사용한다. 다음을 절대 하지 말 것(과거 실수):

- 메타 정보를 **pill/알약/배지나 아이콘·이모지로 표시하지 말 것** → 반드시 `.meta` 2열 그리드(`.cell > .k + .v`)의 텍스트로.
- 상단 **네비게이션 바·목차(개요·1·2·3…)를 만들지 말 것.**
- 헤더를 밝은 파랑/보라로 그리지 말 것 → 네이비 `#2b4c8c → #16294d`.
- 새 컴포넌트·색·폰트를 발명하지 말 것. 아래 CSS·골격 밖의 요소를 추가하지 않는다.

## 우선순위

1. 가능하면 `${CLAUDE_PLUGIN_ROOT}/skills/문서/doc-style.css`·`template.html`을 읽어 그대로 사용(설치 환경).
2. **파일을 읽을 수 없으면(예: Cowork) 아래 "전체 CSS"·"HTML 골격"을 그대로 쓴다.** 기억·추측으로 다시 그리지 말 것.
3. (보조) 위가 다 안 되면 `https://raw.githubusercontent.com/fkrn75/doc-style-plugin/main/skills/문서/doc-style.css` 를 가져와 인라인.

## 절차

1. 위 우선순위로 CSS를 확보한다.
2. 사용자가 요청한 내용으로 **본문만** 채운다(아래 HTML 골격의 블록을 복제, 불필요한 블록은 제거).
3. **자기완결 단일 HTML**로 만든다 — CSS를 `<head>`의 `<style>`에 통째로 인라인(외부 링크·CDN 금지).
4. 사용자가 경로를 지정하면 거기에, 아니면 현재 폴더에 `<문서명>.html`로 저장 후 브라우저로 연다(Windows: `Start-Process <경로>`).

## 전체 CSS (그대로 `<style>`에 인라인)

```css
:root{
  --accent:#2b4c8c; --accent-soft:#eef2fb; --accent-line:#c7d3ee;
  --head-from:#2b4c8c; --head-to:#16294d;
  --ink:#1c2230; --ink-soft:#4a5568; --muted:#6b7280; --line:#e3e7ef;
  --bg:#f4f6fa; --card:#ffffff;
  --ok:#1f7a4d; --ok-soft:#e8f5ee; --warn:#b25b1b; --warn-soft:#fdf0e6;
  --radius:14px; --maxw:920px;
}
*{box-sizing:border-box;}
html{ -webkit-text-size-adjust:100%; }
body{
  margin:0; background:var(--bg); color:var(--ink);
  font-family:"Pretendard","Pretendard Variable",-apple-system,BlinkMacSystemFont,"Apple SD Gothic Neo","Malgun Gothic","맑은 고딕","Noto Sans KR",sans-serif;
  line-height:1.7; font-size:clamp(15px,1.6vw,16.5px); -webkit-font-smoothing:antialiased; word-break:keep-all;
}
.wrap{ max-width:var(--maxw); margin:0 auto; padding:28px 20px 64px; }
.toolbar{ max-width:var(--maxw); margin:0 auto; padding:14px 20px 0; display:flex; justify-content:flex-end; gap:8px; }
.btn{ appearance:none; border:1px solid var(--accent-line); background:var(--card); color:var(--accent); font:inherit; font-weight:600; font-size:.9rem; padding:9px 16px; border-radius:10px; cursor:pointer; display:inline-flex; align-items:center; gap:7px; }
.btn:hover{ background:var(--accent-soft); }
header.doc{ background:linear-gradient(135deg,var(--head-from) 0%,var(--head-to) 100%); color:#fff; border-radius:var(--radius); padding:32px 34px 28px; box-shadow:0 6px 22px rgba(20,40,90,.20); }
header.doc .eyebrow{ color:rgba(255,255,255,.82); font-weight:700; font-size:.8rem; letter-spacing:.14em; text-transform:uppercase; margin:0 0 8px; }
header.doc h1{ margin:0 0 6px; color:#fff; font-size:clamp(1.55rem,3.6vw,2.05rem); font-weight:800; letter-spacing:-.01em; line-height:1.3; }
header.doc .subtitle{ margin:0 0 22px; color:rgba(255,255,255,.86); font-size:1rem; }
.meta{ display:grid; grid-template-columns:repeat(2,minmax(0,1fr)); gap:10px 28px; font-size:.92rem; }
.meta .cell{ display:flex; gap:10px; align-items:baseline; }
.meta .k{ color:rgba(255,255,255,.7); font-size:.82rem; font-weight:700; flex:0 0 auto; min-width:58px; letter-spacing:.02em; }
.meta .v{ color:#fff; font-weight:500; }
main{ margin-top:22px; }
section.card{ background:var(--card); border:1px solid var(--line); border-radius:var(--radius); padding:24px 28px; margin:0 0 18px; box-shadow:0 1px 3px rgba(20,30,60,.04); }
section.card > h2{ margin:0 0 14px; font-size:1.18rem; font-weight:800; letter-spacing:-.01em; display:flex; align-items:center; gap:10px; }
section.card > h2 .num{ flex:0 0 auto; width:26px; height:26px; background:var(--accent-soft); color:var(--accent); border-radius:8px; font-size:.85rem; font-weight:800; display:inline-flex; align-items:center; justify-content:center; }
h3{ font-size:1rem; font-weight:700; margin:18px 0 6px; color:var(--accent); }
p{ margin:0 0 10px; color:var(--ink-soft); }
ul,ol{ margin:0 0 4px; padding-left:1.15em; }
li{ margin:0 0 8px; color:var(--ink-soft); }
li::marker{ color:var(--accent-line); }
strong{ color:var(--ink); font-weight:700; }
.summary{ border-left:4px solid var(--accent); }
.summary p{ color:var(--ink); font-size:1.02rem; margin-bottom:0; }
code{ font-family:ui-monospace,"Cascadia Code","D2Coding",Consolas,monospace; font-size:.88em; background:var(--accent-soft); color:var(--accent); padding:.12em .44em; border-radius:6px; }
pre{ background:#f7f9fd; border:1px solid var(--line); border-radius:10px; padding:15px 18px; margin:0 0 12px; overflow:auto; line-height:1.55; }
pre code{ background:none; color:var(--ink); padding:0; font-size:.86rem; }
.callout{ border-radius:var(--radius); padding:13px 16px 13px 18px; margin:0 0 12px; border:1px solid; font-size:.96rem; }
.callout strong{ display:block; margin-bottom:3px; }
.callout.info{ background:var(--accent-soft); border-color:var(--accent-line); }
.callout.info, .callout.info p{ color:#26406f; }
.callout.warn{ background:var(--warn-soft); border-color:#f3d9c0; }
.callout.warn, .callout.warn p{ color:#7a3f12; }
.callout p{ margin:0; }
.checklist{ background:var(--ok-soft); border:1px solid #cfe8d9; border-radius:var(--radius); }
.checklist > h2 .num{ background:#d3ecdf; color:var(--ok); }
.checklist ul{ list-style:none; padding-left:0; margin:0; }
.checklist li{ position:relative; padding-left:30px; margin-bottom:10px; color:var(--ink); }
.checklist li::before{ content:"✓"; position:absolute; left:4px; top:0; color:var(--ok); font-weight:800; }
.checklist li:last-child{ margin-bottom:0; }
.table-wrap{ overflow-x:auto; -webkit-overflow-scrolling:touch; }
table.spec{ width:100%; border-collapse:collapse; font-size:.95rem; }
table.spec th,table.spec td{ text-align:left; padding:11px 12px; border-bottom:1px solid var(--line); vertical-align:top; }
table.spec thead th{ background:var(--accent-soft); color:var(--accent); font-size:.82rem; font-weight:700; letter-spacing:.02em; border-bottom:1px solid var(--accent-line); }
table.spec tbody tr:last-child td{ border-bottom:none; }
table.spec .key{ font-weight:700; color:var(--ink); white-space:nowrap; }
.tag{ display:inline-block; font-size:.78rem; font-weight:700; padding:2px 9px; border-radius:999px; background:var(--accent-soft); color:var(--accent); white-space:nowrap; }
.tag.req{ background:#fdf0e6; color:#b25b1b; }
.tag.opt{ background:#eef1f4; color:#5a6675; }
footer.doc{ margin-top:24px; color:var(--muted); font-size:.84rem; text-align:center; line-height:1.6; }
footer.doc a{ color:var(--accent); }
.note{ margin-top:8px; font-size:.8rem; color:var(--muted); }
@media screen and (max-width:640px){
  .wrap{ padding:18px 14px 48px; }
  header.doc{ padding:24px 20px 22px; }
  section.card{ padding:18px 18px; }
  .meta{ grid-template-columns:1fr; gap:8px; }
  table.spec{ min-width:460px; }
}
@media print{
  @page{ size:A4; margin:14mm; }
  html,body{ background:#fff; color:#000; font-size:10.5pt; line-height:1.55; }
  .wrap{ max-width:none; margin:0; padding:0; }
  .toolbar,.no-print{ display:none !important; }
  header.doc,section.card{ box-shadow:none; }
  section.card{ border:1px solid #c9cfdb; border-radius:8px; padding:14px 16px; margin-bottom:10px; }
  header.doc{ border-radius:8px; padding:18px 20px; }
  header.doc, section.card > h2 .num, .checklist, .tag, table.spec thead th, .summary, .callout, code, pre{ -webkit-print-color-adjust:exact; print-color-adjust:exact; }
  p, li, pre, .callout, .meta, .meta .cell, h3, blockquote{ break-inside:avoid; page-break-inside:avoid; }
  table.spec tr{ break-inside:avoid; page-break-inside:avoid; }
  header.doc, .summary, .checklist, .callout{ break-inside:avoid; page-break-inside:avoid; }
  h2,h3{ break-after:avoid; page-break-after:avoid; }
  a{ color:#000; text-decoration:none; }
}
```

## HTML 골격 (이 구조 그대로, 내용만 교체 — 블록 추가·변형 금지)

```html
<!DOCTYPE html>
<html lang="ko"><head>
<meta charset="UTF-8"><meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>문서 제목</title>
<style>/* ↑ 위 "전체 CSS"를 여기 통째로 인라인 */</style>
</head><body>
  <div class="toolbar no-print">
    <button class="btn" onclick="window.print()" type="button">인쇄 / PDF 저장</button>
  </div>
  <div class="wrap">
    <header class="doc">
      <p class="eyebrow">분류 라벨 · Category</p>
      <h1>문서 제목</h1>
      <p class="subtitle">한 줄 부제</p>
      <div class="meta">
        <div class="cell"><span class="k">항목1</span><span class="v">값</span></div>
        <div class="cell"><span class="k">항목2</span><span class="v">값</span></div>
        <div class="cell"><span class="k">항목3</span><span class="v">값</span></div>
        <div class="cell"><span class="k">항목4</span><span class="v">값</span></div>
      </div>
    </header>
    <main>
      <section class="card summary">
        <h2><span class="num">·</span>한눈에 보기</h2>
        <p>요약 1~3문장. <strong>강조</strong>는 strong.</p>
      </section>
      <section class="card">
        <h2><span class="num">1</span>섹션 제목</h2>
        <p>본문.</p>
        <ul><li>목록.</li></ul>
        <h3>소제목</h3>
        <pre><code>예시 코드</code></pre>
        <div class="callout info"><strong>참고</strong><p>부가 설명.</p></div>
        <div class="callout warn"><strong>주의</strong><p>경고.</p></div>
      </section>
      <section class="card">
        <h2><span class="num">2</span>표 섹션</h2>
        <div class="table-wrap"><table class="spec">
          <thead><tr><th>열1</th><th>열2</th><th>열3</th></tr></thead>
          <tbody>
            <tr><td class="key">키</td><td>설명 <span class="tag req">필수</span></td><td>값</td></tr>
            <tr><td class="key">키</td><td>설명 <span class="tag opt">선택</span></td><td>값</td></tr>
          </tbody>
        </table></div>
      </section>
      <section class="card checklist">
        <h2><span class="num">✓</span>체크리스트</h2>
        <ul><li><strong>항목</strong> — 설명.</li></ul>
      </section>
    </main>
    <footer class="doc">
      <div>푸터 — 출처·링크.</div>
      <div class="note">부가 주석.</div>
    </footer>
  </div>
</body></html>
```

## 블록 의미

- **헤더** `header.doc` — eyebrow + h1 + subtitle + `.meta`(2열 k/v 텍스트). 그라데이션 네이비. **pill·아이콘·네비바 금지.**
- **요약** `.summary` — 좌측 네이비 바.
- **번호 섹션** `section.card` + `h2 > .num`(1,2,3… 또는 ·,→,✓).
- **콜아웃** `.callout.info`(참고)/`.warn`(주의).
- **표** `.table-wrap > table.spec` (+ `.tag.req`/`.opt`).
- **체크리스트** `.checklist`(초록 ✓).
- **푸터** `footer.doc`.

## 금지

- **산출물은 HTML 단일 파일.** docx(Word)·PDF 등으로 변환·추가 생성하지 말 것(사용자 명시 요청 시에만).
- 위 CSS·골격 밖의 컴포넌트(네비바, pill 메타, 아이콘 등)를 추가하거나, 색·폰트·여백을 임의 변경하지 말 것. **일관성이 목적이다.**
- 외부 스타일시트·웹폰트 CDN 의존 금지(자기완결).

## 색만 바꿀 때 (요청 시에만)

`--accent`/`--head-from`/`--head-to`만 교체. 구조·레이아웃·폰트는 유지.
