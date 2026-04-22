# MyReader 주식 리포트 템플릿 스펙 v1.0

> **이 문서는 Claude가 새 종목/섹터 분석 리포트를 생성할 때 따라야 할 구조·내용 규격서입니다.**
> 사용자가 "XX 섹터 분석 만들어줘" / "YY 종목 리포트 만들어줘" 요청 시 이 스펙을 먼저 읽고 진행합니다.

---

## 0. 대전제

- **플랫폼**: GitHub Pages 배포 → 모바일 브라우저 열람
- **위치**: `myreader/stock/{주제명}_mobile.html`
- **네이밍**: 소문자 스네이크, 영문/한자 표기 지양 → 예: `kospi_semiconductor_report_mobile.html`, `ai_theme_report_mobile.html`
- **index.html 카드 자동 추가**: 리포트 생성 후 [myreader/stock/index.html](myreader/stock/index.html) 목록에 신규 카드 1개 추가 필수
- **면책**: 모든 리포트 헤더에 "투자 참고용 — 결과 책임은 투자자 본인에게 있음" 고정
- **기준일 명시**: "실적 YYYY년 연간 공시 기준 / 주가 YYYY.MM.DD 장마감 기준" 필수

---

## 1. 기술 스택 / 외부 의존

| 항목 | 사용 | 비고 |
|---|---|---|
| HTML/CSS/Vanilla JS | ✅ | 단일 파일, 빌드 툴 없음 |
| Google Fonts | ✅ | `Noto Sans KR`, `IBM Plex Mono` |
| Chart.js (CDN) | ✅ | 주가 스파크라인·4분면 섹터지도용 — `https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js` |
| 이미지·외부 리소스 | ❌ | 차트는 Chart.js로 그리고, 아이콘은 이모지 |
| 프레임워크 | ❌ | React/Vue 등 사용 금지 |

모바일 최적화 필수:
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
```

---

## 2. 디자인 토큰 (CSS 변수 — 모든 리포트 공통)

```css
:root {
  --bg: #e9ecef;
  --surface: #ffffff;
  --surface2: #f1f3f5;
  --border: #ced4da;
  --accent: #1a73e8;   /* 기본 파랑 */
  --accent2: #3b82f6;
  --accent3: #e67e00;
  --red: #d93025;
  --green: #188038;
  --text: #101828;
  --muted: #667085;
  --label: #344054;
  --nav-h: 60px;
  --safe-bottom: env(safe-area-inset-bottom, 0px);
}
```

**헤더 그라데이션은 섹터별로 변화**:
- 플랫폼/IT 계열: `#0f9d58 → #0b7a3e` (그린) 또는 `#1a73e8 → #1557b0` (블루)
- 바이오: `#1a73e8 → #1557b0`
- 반도체: `#7c3aed → #5b21b6` (퍼플)
- 금융/은행: `#0891b2 → #0e7490` (틸)
- 자동차/2차전지: `#dc2626 → #991b1b` (레드)
- AI/테마: `#ec4899 → #be185d` (핑크)

---

## 3. 리포트 전체 구조 (순서 고정)

```
1. 헤더 (.header) — 섹터 그라데이션 + 태그 + 제목 + 기준일 배지 + 메타 + 면책
2. 기준일 경과 배지 (.freshness-badge) — JS 자동 계산 "N일 경과"
3. 🆕 섹터 진단 (.sector-overview) — 3줄 요약 또는 4분면 지도
4. 🆕 이벤트 캘린더 (.event-calendar) — 향후 6~12개월
5. 목차 (.toc-wrap) — sticky 칩 네비
6. 태그 범례 (.tag-legend) — 4색 점 + 라벨
7. 종목 카드 × N (.company-card) — 아코디언
   ├ 헤더 (번호/이름/티커/뱃지/chevron)
   └ 바디
     ├ 스탯 2×2 (.stats-row)
     ├ 52주 가격 바 (.price-bar-wrap)
     ├ 🆕 주가 스파크라인 (.price-chart) — Chart.js, 1년
     ├ 탭 네비 (.tab-nav)
     └ 탭 패널 × 4 (.tab-pane)
       ├ 사업분석 (section-block + highlight-box + probability)
       ├ 주가동인 (section-block ×2)
       ├ 강점약점 (sw-box strength/weakness)
       └ 뉴스키워드 (tag-group ×4, 클릭 복사)
     ├ 🆕 경쟁사 매핑 (.competitor-matrix)
     ├ 🆕 시나리오 트리 (.scenario-tree) — Bull/Base/Bear
     └ 🆕 액션존 (.action-zone) — 진입·손절·차익실현 가격대
8. 딥다이브 (.deep-section) — 섹터 전체 분석, 필수
   ├ 구조적 원인 (reason)
   ├ 비교 (compare)
   ├ 반등 탈출구 (hope)
   ├ 실전 시그널 (watch, signal-grid)
   └ 결론
9. 🆕 수급 테이블 (.flow-table) — 외국인/기관/개인 순매수
10. 🆕 오너·경영진 리스크 (.owner-risk) — 해당 종목만
11. 종합 비교 (.summary-card) — 시총/PER/ROE/영업이익/위험도/주가동인
12. 인사이트 박스 (.insight-box) + PER 주의 (.per-note)
13. 🆕 체크리스트 위젯 (.checklist) — localStorage 저장
14. 🆕 용어집 (.glossary) — 바이오/반도체 등 필요 시
15. 🆕 참고자료 푸터 (.references)
16. 🆕 관련 리포트 연결 (.related-reports)
17. 하단 내비게이션 (.bottom-nav) — 고정, 앵커 링크
18. 토스트 (.toast)
```

**최소 필수 (종목 1~2개짜리 라이트 리포트)**: 1, 2, 5, 6, 7(카드 간소화), 11, 12, 15, 17
**권장 (섹터 리포트)**: 전부

---

## 4. 섹션별 상세 규격

### 4.1 헤더 + 기준일 경과 배지
- `.header` 높이 대략 200px, 그라데이션 배경
- `.header h1` 폰트 26px / weight 900
- `.header-date` 배지: 작성 기준일
- **경과 배지 JS** (필수):
  ```js
  const BASE_DATE = '2026-04-22'; // 작성 시점
  const days = Math.floor((Date.now() - new Date(BASE_DATE)) / 86400000);
  if (days > 7) {
    document.querySelector('.freshness-badge').textContent =
      `⚠ 기준일로부터 ${days}일 경과 — 최신 주가 확인 권장`;
    document.querySelector('.freshness-badge').classList.add('stale');
  }
  ```

### 4.2 🆕 섹터 진단 (.sector-overview)
두 가지 버전 중 선택:

**(A) 3줄 섹터 진단** (모든 리포트 기본):
```html
<div class="sector-overview">
  <div class="sector-label">SECTOR DIAGNOSIS</div>
  <ul class="diagnosis-list">
    <li class="good">✅ 긍정 — 한 줄</li>
    <li class="neutral">⚖ 중립 — 한 줄</li>
    <li class="bad">⚠ 리스크 — 한 줄</li>
  </ul>
</div>
```

**(B) 4분면 지도** (종목 4개 이상일 때 권장):
Chart.js scatter로 (x: 실적 기반, y: 성장 기대감) 2차원 매핑. 각 점은 종목.
```js
new Chart(ctx, {
  type: 'scatter',
  data: { datasets: [{
    data: [{x: 90, y: 30, label: '케어젠'}, ...],
    pointRadius: 8
  }]},
  options: { /* 축: -실적+, -기대감+ */ }
});
```

### 4.3 🆕 이벤트 캘린더 (.event-calendar)
```html
<div class="event-calendar">
  <div class="cal-label">📅 EVENT CALENDAR — 향후 12개월</div>
  <div class="cal-item high">
    <div class="cal-date">2026.07</div>
    <div class="cal-title">김범수 항소심 선고</div>
    <div class="cal-tag">카카오 · ★최대 이벤트</div>
  </div>
  <!-- 중요도: high (빨강) / mid (주황) / low (회색) -->
</div>
```
정렬: 날짜 오름차순. 미정 이벤트는 "2026 2H" 등 분기 단위 허용.

### 4.4 종목 카드 내부 - 🆕 주가 스파크라인 (.price-chart)
52주 가격 바 **바로 아래**에 배치. 높이 60px 정도의 미니 라인 차트.
```html
<canvas class="price-chart" data-ticker="035420"
        data-prices="280000,275000,...,201500"
        height="60"></canvas>
```
JS가 `data-prices`를 파싱해서 Chart.js line으로 렌더. 축·레이블 없이 순수 라인만. 색상: 시작값 < 끝값 이면 녹색, 반대는 빨강.

### 4.5 종목 카드 내부 - 🆕 경쟁사 매핑 (.competitor-matrix)
탭 4개 뒤에 추가.
```html
<div class="competitor-matrix">
  <div class="matrix-label">경쟁 지형</div>
  <table class="comp-table">
    <tr><th></th><th>당사</th><th>경쟁사A</th><th>경쟁사B</th></tr>
    <tr><td>시장 점유율</td><td class="us">30%</td><td>50%</td><td>15%</td></tr>
    <tr><td>핵심 차별점</td><td class="us">저가·속도</td><td>물류망</td><td>해외</td></tr>
    <tr><td>리스크</td><td class="us">AI 대응</td><td>가격 경쟁</td><td>규제</td></tr>
  </table>
</div>
```

### 4.6 종목 카드 내부 - 🆕 시나리오 트리 (.scenario-tree)
```html
<div class="scenario-tree">
  <div class="sc-label">12개월 시나리오</div>
  <div class="sc-row bull">
    <div class="sc-tag">🟢 BULL 25%</div>
    <div class="sc-cond">무죄 + 실적 + AI 성과</div>
    <div class="sc-price">70,000~85,000 (+50%)</div>
  </div>
  <div class="sc-row base">
    <div class="sc-tag">🟡 BASE 50%</div>
    <div class="sc-cond">재판 지연 + 실적 개선</div>
    <div class="sc-price">50,000~60,000 (±10%)</div>
  </div>
  <div class="sc-row bear">
    <div class="sc-tag">🔴 BEAR 25%</div>
    <div class="sc-cond">유죄 or AI 실패</div>
    <div class="sc-price">30,000~40,000 (-30%)</div>
  </div>
  <div class="sc-expected">
    <strong>기대값(E):</strong> 55,600원 — 현재가 대비 <span class="positive">+18%</span>
  </div>
</div>
```
세 시나리오 확률 합 = 100% 검증 필수.

### 4.7 종목 카드 내부 - 🆕 액션존 (.action-zone)
```html
<div class="action-zone">
  <div class="az-label">🎯 Action Zone</div>
  <div class="az-row buy-strong">💚 적극매수 < 190,000 <span class="az-note">박스권 하단</span></div>
  <div class="az-row buy">💛 분할매수 190,000 ~ 220,000</div>
  <div class="az-row hold">⚪ 관망 220,000 ~ 280,000</div>
  <div class="az-row sell">🔴 차익실현 > 280,000</div>
  <div class="az-row stop">✂ 손절선 < 175,000 <span class="az-note">지지선 이탈</span></div>
</div>
```

### 4.8 🆕 수급 테이블 (.flow-table)
딥다이브 뒤 섹터 단위 한 번.
```html
<table class="flow-table">
  <caption>최근 1개월 순매수 (억원)</caption>
  <tr><th>종목</th><th>외국인</th><th>기관</th><th>개인</th><th>판정</th></tr>
  <tr><td>네이버</td><td class="neg">-450</td><td class="neg">-220</td><td class="pos">+670</td><td class="bad">수급 악화</td></tr>
</table>
```

### 4.9 🆕 오너 리스크 (.owner-risk)
해당하는 종목만. 없으면 섹션 생략.
```html
<div class="owner-risk">
  <div class="or-label">오너·경영진 리스크</div>
  <div class="or-item">
    <div class="or-name">김범수 <span class="or-role">카카오 창업자</span></div>
    <div class="or-issue">SM엔터 시세조종 항소심 — 2026.07 선고 예정</div>
    <div class="or-severity">🔴 HIGH</div>
  </div>
</div>
```

### 4.10 🆕 체크리스트 위젯 (.checklist)
localStorage 키: `checklist_{파일명}`
```html
<div class="checklist">
  <div class="cl-label">📋 매수 체크리스트</div>
  <label><input type="checkbox" data-id="cl1"> 알테오젠 Q2 로열티 매출 인식 확인</label>
  <label><input type="checkbox" data-id="cl2"> 키트루다 SC PGR 심결 나옴</label>
  <div class="cl-progress">0 / 3 체크됨</div>
</div>
```
JS:
```js
const key = 'checklist_' + location.pathname.split('/').pop();
// 체크 이벤트 시 localStorage 저장, 로드 시 복원
```

### 4.11 🆕 용어집 (.glossary)
바이오·반도체·금융 등 약어 많은 섹터만. 툴팁 버전과 목록 버전 둘 다 제공:
```html
<!-- 본문 -->
<abbr class="term" data-term="ADC">ADC</abbr>

<!-- 페이지 하단 -->
<div class="glossary">
  <div class="gl-label">📖 용어집</div>
  <dl>
    <dt>ADC</dt><dd>항체약물접합체 (Antibody-Drug Conjugate)</dd>
    <dt>BBB</dt><dd>혈액뇌장벽 (Blood-Brain Barrier)</dd>
  </dl>
</div>
```
JS로 `.term` 호버/탭 시 툴팁.

### 4.12 🆕 참고자료 푸터 (.references)
```html
<div class="references">
  <div class="ref-label">📚 참고 자료</div>
  <ul>
    <li><strong>실적</strong> DART 전자공시시스템 (2025 사업보고서)</li>
    <li><strong>컨센서스</strong> 에프앤가이드 / 하나·SK 증권 리포트</li>
    <li><strong>임상</strong> ClinicalTrials.gov / 각 사 IR</li>
    <li><strong>주가·시총</strong> KRX, 네이버금융 (2026.MM.DD 장마감)</li>
  </ul>
</div>
```

### 4.13 🆕 관련 리포트 연결 (.related-reports)
```html
<div class="related-reports">
  <div class="rel-label">🔗 관련 리포트</div>
  <a class="rel-card" href="naver_kakao_report_mobile.html">
    <div class="rel-tag">플랫폼</div>
    <div class="rel-title">네이버 vs 카카오 박스권 탈출</div>
  </a>
</div>
```

---

## 5. 내용 작성 규칙 (Contents Rules)

### 5.1 확률 숫자 제시 원칙
- `probability` 바의 %는 **근거가 되는 문장을 본문에 반드시 포함**
- 높을수록 녹색(high, 60%+), 중간(mid, 30~60%), 낮음(low, 30%-)
- "느낌"으로 찍지 말고: **임상 단계 성공률 통계, 경쟁사 선례, 애널리스트 컨센서스** 중 하나 근거

### 5.2 숫자 출처 표기
- 구체 수치(매출·영업이익·시총) 옆에 출처 미니 표기: `1,069억<sup>DART</sup>`, `컨센서스 4,712억<sup>하나證</sup>`
- 추정치는 "추정" 명시: `영업이익 3,082억 (추정)`
- 애매한 범위는 단일값으로 통일하지 말고 "~" 허용: `시총 5~6조`

### 5.3 시나리오 트리 작성 가이드
- **확률 합 = 100%**
- Bull은 "모든 호재 발생 + 타이밍 맞음", Bear는 "핵심 리스크 현실화"
- **기대값 계산 공식 표시**: `E = 0.25×77,500 + 0.5×55,000 + 0.25×35,000`
- 기대값이 현재가 대비 ±5% 이내면 "관망", +15%↑면 "긍정", -15%↓면 "부정"

### 5.4 액션존 가격대 산출 기준
- **진입가**: 52주 저점·지지선·배당수익률 2% 수준 중 가장 보수적
- **손절가**: 주요 지지선 -5~10%
- **차익실현가**: 52주 고점 또는 컨센서스 목표가 중앙값

### 5.5 섹터 진단 3줄 템플릿
- ✅ 긍정: "OO가 YY로 인해 ZZ 중"
- ⚖ 중립: "AA는 진행 중이나 BB 불확실"
- ⚠ 리스크: "CC가 DD하면 EE 타격"

### 5.6 이벤트 캘린더 선별 기준
포함해야 할 이벤트:
- 실적 발표 (분기별)
- 제품 런칭·출시
- 임상 결과 공개 (바이오)
- 규제 심사 결과 (FDA, 공정위 등)
- 재판·판결
- 자사주 매입·소각, 배당 공시
- 주총, IPO 일정

### 5.7 뉴스키워드 태그 원칙
- **4그룹 고정**: 핵심기술 / 제품·파트너(사업·서비스) / 이벤트 신호 / 적신호
- 각 그룹 3~5개
- 네이버·구글 뉴스에서 그대로 검색 가능한 형태 (영문 병기 권장)
- 너무 일반적인 단어 금지 (예: "매출", "실적" 단독 ❌)

### 5.8 위험도 ⭐ 산정
| 별 | 조건 |
|---|---|
| ⭐ | 실적 탄탄 + 배당 + 저변동성 |
| ⭐⭐ | 실적 기반 + 일부 기대감 섞임 |
| ⭐⭐⭐ | 실적+기대감 이중 구조 or 중간 변동성 |
| ⭐⭐⭐⭐ | 기대감 주도 + 적자 or 재판·규제 리스크 |
| ⭐⭐⭐⭐⭐ | 100% 기대감, 임상·허가 1개에 모든 것 걸림 |

### 5.9 뱃지 스타일
- `badge-profit` (녹): 흑자·22년 연속 흑자 등
- `badge-loss` (빨): 적자, 구조적 적자
- `badge-mixed` (황): 이익 미약, 흑전 중, 회복 진행

---

## 6. 스크립트 필수 함수 (공통 JS 블록)

모든 리포트 하단에 포함:
```js
// 1. 아코디언
function toggleCard(header) { /* 기존 */ }

// 2. 탭 전환
function switchTab(btn, cardId) { /* 기존 */ }

// 3. 태그 복사 + 토스트
function copyTag(el) { /* 기존 */ }
function showToast(msg) { /* 기존, msg 파라미터 추가 */ }

// 4. 앵커 스크롤 오프셋 보정
document.querySelectorAll('a[href^="#"]').forEach(...);

// 🆕 5. 기준일 경과 배지
function updateFreshness(baseDate) { /* 4.1 참조 */ }

// 🆕 6. 주가 스파크라인 렌더
function renderPriceCharts() {
  document.querySelectorAll('.price-chart').forEach(canvas => {
    const prices = canvas.dataset.prices.split(',').map(Number);
    const isUp = prices[prices.length-1] > prices[0];
    new Chart(canvas, {
      type: 'line',
      data: { labels: prices.map((_,i) => i),
              datasets: [{ data: prices, borderColor: isUp ? '#188038' : '#d93025',
                           borderWidth: 2, pointRadius: 0, tension: 0.3 }]},
      options: { plugins: {legend:{display:false}}, scales: {x:{display:false}, y:{display:false}} }
    });
  });
}

// 🆕 7. 4분면 섹터 지도 (있을 때만)
function renderSectorMap(data) { /* Chart.js scatter */ }

// 🆕 8. 체크리스트 localStorage
function initChecklist() {
  const key = 'checklist_' + location.pathname.split('/').pop();
  const saved = JSON.parse(localStorage.getItem(key) || '{}');
  document.querySelectorAll('.checklist input').forEach(cb => {
    if (saved[cb.dataset.id]) cb.checked = true;
    cb.addEventListener('change', () => {
      saved[cb.dataset.id] = cb.checked;
      localStorage.setItem(key, JSON.stringify(saved));
      updateChecklistProgress();
    });
  });
  updateChecklistProgress();
}

// 🆕 9. 용어집 툴팁
function initGlossary() { /* .term 호버/탭 시 dt/dd 찾아 툴팁 */ }

// 초기화
document.addEventListener('DOMContentLoaded', () => {
  updateFreshness(BASE_DATE);
  renderPriceCharts();
  initChecklist();
  initGlossary();
});
```

---

## 7. index.html 등록

리포트 생성 후 [myreader/stock/index.html](myreader/stock/index.html) 의 `.list` 안에 카드 1개 **최상단 추가**:
```html
<a href="{파일명}.html" class="card">
  <div class="card-top">
    <div class="tag">{섹터 한글 2어절}</div>
    <div class="date">YYYY.MM.DD</div>
  </div>
  <div class="title">{리포트 제목}</div>
  <div class="meta">{부제 한 줄}</div>
</a>
```

---

## 8. 생성 워크플로우 (Claude 체크리스트)

새 리포트 요청 받으면 이 순서로:

1. **[확인]** 종목/섹터 범위 확정, 기준일(오늘)
2. **[자료]** 시가총액·매출·영업이익·주가 (웹서치 우선, 추정치는 표시)
3. **[구조]** 섹션 1~17 중 해당 리포트에 넣을 섹션 선별
4. **[작성]** 스펙 그대로 HTML 생성
5. **[검증]**
   - 시나리오 확률 합 = 100%
   - 경과 배지 JS 포함
   - Chart.js CDN 링크 포함 (차트 쓸 때만)
   - 면책·기준일 헤더에 있음
   - 하단 네비 앵커 5개 이내
6. **[등록]** index.html에 카드 추가
7. **[보고]** 사용자에게 생성 경로와 포함 섹션 요약 전달

---

## 9. 제약 조건

- **절대 금지**
  - 과장된 목표가 ("300% 상승 가능!") 없이 시나리오 트리로만 표현
  - 출처 없는 컨센서스 수치
  - "내일 사라" 류의 단정적 매매 지시

- **권장**
  - 확률·범위·시나리오로 불확실성 명시
  - 약점·리스크 섹션 반드시 비어있지 않게
  - 독자가 **다음에 뭘 읽고 뭘 체크할지** 알 수 있게 끝내기

---

## 10. 레퍼런스 구현

- [naver_kakao_report_mobile.html](naver_kakao_report_mobile.html) — 플랫폼 2종목, 딥다이브 포함 버전
- [kosdaq_bio_report_mobile.html](kosdaq_bio_report_mobile.html) — 바이오 8종목, 섹터 지도 버전

이 두 파일이 v2로 보강되면서 위 스펙의 실제 구현 예시가 됩니다.
