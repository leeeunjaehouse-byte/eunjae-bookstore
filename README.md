# 이은재서점 — Web

좋아하는 출판사·작가·독립서점의 신간을 매일 자동 수집하는 큐레이션 사이트.

- 🎬 만화: 알라딘 · 예스24 신간 (매일 갱신)
- 📚 독립서점: 유어마인드 · 소심한책방 · 인디펍 · 러브앤프리 · 땡스북스 외 (매일 자동 HTTP + 주1회 인스타 시각 수집)

## 구조

```
_deploy/
├── index.html       ← 메인 페이지 (다크모드 · 모바일 우선)
├── data/
│   ├── manga.json / manga.js     ← 만화 데이터
│   └── indie.json / indie.js     ← 독립서점 데이터
├── vercel.json      ← Vercel 캐시 설정
└── .gitignore
```

## 배포

Vercel 자동 배포. main 브랜치에 push 하면 60초 안에 새 버전 라이브.

캐시 정책:
- `data/*` → 5분 캐시 (자주 갱신되니까)
- `index.html` → 캐시 0 + revalidate 60초

## 데이터 갱신 흐름

데이터 크롤러는 **상위 폴더 (`03_산출물_v2/`)** 에 있음.

```
03_산출물_v2/run_daily.py      ← 매일 08:00 자동 (Cowork 스케줄)
  ↓ 결과 (manga.json, indie.json)
03_산출물_v2/data/
  ↓ 복사
_deploy/data/                  ← 이 폴더
  ↓ git commit + push (GitHub Desktop 한 번 클릭)
GitHub
  ↓ webhook
Vercel 자동 재배포
```

## 변경 사항 라이브 반영 단계

1. 크롤러 자동 또는 수동 실행 (Cowork 채팅에 "크롤링 돌려줘")
2. 상위 폴더 `data/` → 이 폴더의 `data/` 동기화 (자동, run_daily.py 가 처리)
3. GitHub Desktop 열고 → 이 repo 선택 → Commit → Push
4. 1~2분 뒤 Vercel 자동 배포 끝, 라이브 URL 새로고침

## 데이터 스키마

각 책 객체:

```json
{
  "title": "책 제목",
  "author": "저자",
  "publisher": "출판사",
  "cover": "https://image.aladin.co.kr/.../cover200/...jpg",
  "source": "알라딘 | 예스24 | 유어마인드 | ...",
  "source_label": "유어마인드 · BOOKS",
  "aladin_link": "https://www.aladin.co.kr/...",
  "yes24_link": "https://www.yes24.com/...",
  "shop_link": "원본 서점 URL",
  "first_seen": "2026-05-17",
  "last_seen": "2026-05-17",
  "is_favorite": false,
  "favorite_match": [],
  "is_resolved": true
}
```

루트 메타:

```json
{
  "updated_at": "2026-05-17T10:57:51.500553+09:00",
  "today": "2026-05-17",
  "total": 166,
  "books": [...],
  "monitored_sources": [...],          // 만화만
  "monitored_stores_http": [...],      // 독립서점만
  "monitored_stores_instagram": [...]  // 독립서점만
}
```
