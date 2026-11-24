# 무료응모 헌터 (deal-hunter)

구매 없이 참여하는 **무료 응모·경품**, 접수 중인 **AI 콘테스트·경진대회**, 받을 수 있는 **지원금·AI 혜택**을 한곳에 모은 개인용 정적 웹앱.

- `index.html` — 앱 전체(HTML/CSS/JS 한 파일, 빌드 없음)
- `data/events.json` — 무료응모 데이터(58건). 모든 항목은 공식 페이지를 직접 열어 마감 전임을 확인
- `data/contests.json` — AI 콘테스트·경진대회(12건). 공식 대회 페이지에서 접수 기간·상금·자격 확인
- `data/benefits.json` — 지원금·세금·AI 혜택(13건). 국세청·고용24·국토부·각 사 공식 안내 확인
- `images/` — 카드 썸네일(`{id}.jpg`, `contest-*.jpg`, `benefit-*.png`), 브랜드 아이콘(`brands/`), 앱 캐릭터(`avatar.png`)
- GitHub Pages 서브패스(`/dealer-deal/`)와 로컬 루트에서 모두 동작하도록 모든 경로는 `./` 상대경로 사용

## 화면 구성

에디토리얼 1열 리스트(본문 최대 560px, 얇은 구분선). 박스 카드 대신 신문 지면 같은 목록형.

- **헤더**: 원형 캐릭터 아이콘 + 타이틀 + 부제, 우측 다크모드 토글
- **상위 탭 3개**(헤더 아래, 각 탭에 현재 건수 배지): `🎯 무료응모` / `🤖 AI 콘테스트` / `💰 지원금·AI혜택`. 탭을 누르면 `#events`·`#contests`·`#benefits` 해시가 바뀌고, 마지막 탭은 `localStorage`에 기억됩니다. 검색어·필터는 탭별로 독립적입니다.
- **툴바(무료응모)**: 검색 → **경품별 분류 칩**(전체 경품 / ☕ 커피·음료 / 🎫 상품권·페이 / 🍗 치킨·식품 / 🎧 디지털·가전 / 🎁 굿즈·기타, 각 칩에 현재 건수) → 참여 필터 칩(**✅ 내가 응모한 것 (N)** / 미응모만 / 방식 전체 / 인스타 / 공식몰·폼 / 사전예약 / 누구나 가능) + 정렬 칩(마감임박순 / 최신순)
- **툴바(AI 콘테스트)**: 검색 → **✅ 내가 지원한 것 (N)** / 미지원만 / 분야 전체 / 💻 개발·데이터 / 🎨 콘텐츠·창작 / 💡 아이디어·해커톤 / 누구나 가능 + 정렬
- **툴바(지원금·AI혜택)**: 검색 → **✓ 확인한 것 (N)** / 미확인만 / 분야 전체 / 🏛️ 지원금·주거 / 🧾 세금·환급 / 🤖 AI 혜택·교육 / 🚇 교통·생활 / 누구나 가능 + 정렬
- 좁은 화면에서는 칩 줄이 가로 스크롤되고, 툴바가 상단에 고정됩니다.
- **섹션 헤더**: `접수 중인 AI 콘테스트 (12)` + 우측 `11. 24. 확인` + 2px 검정 하단선
- **행**: 주최·제공처 + 참여처 뱃지(`공식 홈페이지`, `데이커(DAKER)`, `Kaggle`, `고용24`, `회사·홈택스` 등) → 굵은 제목(`총상금 2,600만원 · 월렛 AI 서비스 챌린지`처럼 규모+이름) → 테라코타 메타(`개발·데이터 · 12월 21일 마감 · D-27`) → 대상 태그 → 84×84 썸네일 / 요약 1~2줄 / `대회 상세 +`·`참여 조건 +`·`신청 조건 +` 토글 / 공식 페이지 링크 + 체크 토글
- 카드마다 **체크 토글**: 무료응모는 `응모 체크`(`localStorage['dealer_deal_entered_v1']`), AI 콘테스트는 `지원 체크`(`dealer_deal_contests_checked_v1`), 지원금은 `확인 체크`(`dealer_deal_benefits_checked_v1`)에 `{id: 체크 시각(ISO)}` 저장. 다시 누르면 해제되고, 탭마다 기록이 분리됩니다.
- `✅ 내가 응모한 것`·`✅ 내가 지원한 것`·`✓ 확인한 것` 칩으로 체크한 항목만 모아 보고, `미응모만`·`미지원만`·`미확인만`으로 아직 안 한 것만 볼 수 있음(둘은 서로 배타)
- `누구나 가능` 필터를 켜면 지역·나이·대상 제한(`restriction`)이 있는 항목을 한 번에 감춤
- 데이터에 없는(만료·삭제) id가 localStorage에 남아 있어도 개수·목록에서 무시
- 다크/라이트 모드(저장 + 시스템 설정 기본값)

## 데이터 갱신

`data/events.json`의 `events` 배열에 항목을 추가/삭제하고 `updated`, `updated_label`을 고치면 됩니다.
새 항목에는 `images/{id}.jpg`(320×320, 84px 썸네일용)와 `icon`(브랜드 아이콘 경로, 없으면 null)을 함께 넣습니다.

```json
{
  "id": "고유-id",
  "brand": "브랜드",
  "channel": "참여처 짧은 라벨 (예: 구글폼, 네이버폼, 공식 홈페이지, 인스타그램, 사전예약, MY S-OIL 앱)",
  "title": "경품명 — 카드의 굵은 제목. 상품이 1초에 보이게 (예: 피자헛 피자·다이소 2만원 등)",
  "event_name": "원래 이벤트명 — 제목에서 빠지므로 conditions 맨 앞에 함께 적는다",
  "value_note": "경품과 대략적 가치(검색용)",
  "prize_category": "coffee | voucher | food | tech | etc",
  "category": "insta | official | form | prereg",
  "deadline": "YYYY-MM-DD 또는 상시",
  "start": "YYYY-MM-DD",
  "summary": "1~2줄 참여 방법",
  "conditions": "이벤트: {event_name} / 경품: {경품 구성·당첨 인원} / {세부 조건·발표일}",
  "restriction": "제한이 있을 때만 짧은 라벨 (예: 만 19~39세 청년, 부산시민·학생·학부모, W브릿지 신규 가입자), 없으면 null",
  "restriction_type": "region | age | target | null",
  "url": "공식 이벤트 URL",
  "image": "./images/고유-id.jpg",
  "icon": "./images/brands/도메인.png",
  "verified_at": "YYYY-MM-DD"
}
```

`prize_category`는 상단 경품 칩 기준값입니다. 경품이 여러 개 섞인 이벤트는 **가장 대표/고액 경품**으로 정합니다
(`coffee` 커피·음료 / `voucher` 상품권·페이 / `food` 치킨·식품 / `tech` 디지털·가전 / `etc` 굿즈·기타).
값이 없거나 모르는 값이면 화면에서 `title` → `value_note` → `conditions` 순으로 경품 종류를 추론하므로, 일일 업데이트로 새 항목이 들어와도 빈 분류 없이 표시됩니다.

`restriction`은 **성인 이용자 기준으로 실제 참여를 가르는 제한**(거주 지역, 특정 연령대, 특정 대상)만 적습니다.
`만 14세 이상`처럼 거의 모든 이벤트에 붙는 표준 최소연령은 태깅하지 않고 `conditions`에만 남깁니다.

썸네일은 ① 공식 페이지 `og:image` → ② 공용 경품(커피·상품권·포인트 등)은 깔끔한 상품 이미지를 재사용하는 순서로 채웠습니다. 브랜드 아이콘은 Google Favicon(64px) 또는 이니셜 원형 아이콘입니다.

### AI 콘테스트 (`data/contests.json`)

```json
{
  "id": "고유-id",
  "brand": "주최·플랫폼",
  "channel": "참여처 짧은 라벨 (예: 공식 홈페이지, 데이커(DAKER), Kaggle)",
  "title": "총상금 1,000만원 · 대회명 — 규모가 먼저 보이게",
  "event_name": "공식 대회명",
  "prize_note": "상금 구성(대상·최우수·우수 등)",
  "field": "dev | creative | idea",
  "deadline": "YYYY-MM-DD",
  "start": "YYYY-MM-DD",
  "summary": "1~2줄 참가 방법",
  "conditions": "대회명 / 주최·운영 / 접수 기간 / 제출물 / 상금 / 참가비 / 문의",
  "restriction": "참가 자격 제한 라벨, 없으면 null",
  "restriction_type": "age | target | null",
  "url": "공식 대회 페이지 URL",
  "image": "./images/contest-고유-id.jpg",
  "verified_at": "YYYY-MM-DD"
}
```

`field`는 `dev` 💻 개발·데이터 / `creative` 🎨 콘텐츠·창작 / `idea` 💡 아이디어·해커톤.
공식 페이지를 직접 열어 **마감 전 접수 중**임을 확인한 대회만 넣고, 마감이 지난 대회는 지웁니다.

### 지원금·AI혜택 (`data/benefits.json`)

```json
{
  "id": "고유-id",
  "brand": "주관 기관·제공처",
  "channel": "채널 짧은 라벨 (예: 정부24, 고용24, 회사·홈택스)",
  "title": "최대 40만원 · 전세보증료 지원 — 혜택 규모가 먼저 보이게",
  "benefit_category": "subsidy | tax | ai | life",
  "deadline": "YYYY-MM-DD 또는 상시",
  "start": "YYYY-MM-DD",
  "summary": "1~2줄 혜택·신청 방법",
  "conditions": "제도명 / 대상·소득 기준 / 금액·한도 / 신청 방법 / 유의사항",
  "restriction": "대상 조건 라벨, 없으면 null",
  "restriction_type": "age | target | null",
  "url": "공식 안내·신청 URL",
  "image": "./images/benefit-고유-id.png",
  "verified_at": "YYYY-MM-DD"
}
```

`benefit_category`는 `subsidy` 🏛️ 지원금·주거 / `tax` 🧾 세금·환급 / `ai` 🤖 AI 혜택·교육 / `life` 🚇 교통·생활.

개인정보(이름·전화번호·주소·나이·소득 등)는 코드와 데이터에 넣지 않습니다. 이 저장소는 공개되어 있으므로 혜택 목록도 일반 큐레이션으로만 적습니다.
