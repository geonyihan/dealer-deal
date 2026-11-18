# 무료응모 헌터 (deal-hunter)

구매 없이 참여하는 **무료 응모·경품** 중 현금 가치 5,000원 이상만 모아 보여주는 개인용 정적 웹앱.

- `index.html` — 앱 전체(HTML/CSS/JS 한 파일, 빌드 없음)
- `data/events.json` — 응모 데이터. 모든 항목은 공식 페이지를 직접 열어 마감 전임을 확인한 것만 수록
- GitHub Pages 서브패스(`/dealer-deal/`)와 로컬 루트에서 모두 동작하도록 데이터 경로는 `./data/events.json` 상대경로 사용

## 기능

- 상단: 진행 중 건수, 3일 내 마감 건수, 마지막 업데이트, 응모 완료 건수
- 검색: 브랜드·경품·요약·조건 키워드(여러 단어 AND)
- 필터: 전체 / 인스타 / 공식몰·폼 / 사전예약
- 정렬: 마감임박순 / 최신순
- 카드: 응모 방식 뱃지, D-Day 자동 계산(KST), 경품 가치, 참여 요약, 접이식 '참여 조건', 공식 페이지 링크
- 응모 완료 체크 → localStorage 저장(흐리게 표시) + '응모 완료 숨기기' 토글
- 다크/라이트 모드 (저장 + 시스템 설정 기본값)

## 데이터 갱신

`data/events.json`의 `events` 배열에 항목을 추가/삭제하고 `updated`, `updated_label`을 고치면 됩니다.

```json
{
  "id": "고유-id",
  "brand": "브랜드",
  "title": "이벤트명",
  "value_note": "경품과 대략적 가치",
  "category": "insta | official | form | prereg",
  "deadline": "YYYY-MM-DD 또는 상시",
  "start": "YYYY-MM-DD",
  "summary": "3줄 이내 참여 요약",
  "conditions": "세부 조건·발표일",
  "url": "공식 이벤트 URL",
  "verified_at": "YYYY-MM-DD"
}
```

개인정보(이름·전화번호·주소 등)는 코드와 데이터에 넣지 않습니다.
