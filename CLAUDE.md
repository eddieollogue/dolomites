# Dolomites Itinerary — Contributor Notes

단일 파일 프로젝트 (`index.html`). 일정 타임라인 + 관광지 카드로 구성. GitHub Pages로 `main` 브랜치에서 직접 서빙된다.

## 새 관광지(destination) 카드 추가 포맷

기존 카드와 동일한 구조를 사용한다. 시간대는 `time-badge`, 카드는 `destination-card` (전체가 Google Maps로 가는 링크).

```html
<div class="timeline-item destination">
    <div class="timeline-dot"></div>
    <div class="time-badge">10:00 - 12:00</div>
    <a class="destination-card" href="https://www.google.com/maps/search/?api=1&query=PLACE+NAME" target="_blank" rel="noopener">
        <img class="card-thumb" src="IMAGE_URL" alt="한국어 이름" loading="lazy">
        <div class="destination-info">
            <div class="destination-title">한국어 이름 (Original Name)</div>
            <div class="destination-desc">2~3문장 한국어 설명. 무엇을 하는지/어떤 점이 인상적인지.</div>
            <span class="map-link">구글맵에서 보기</span>
        </div>
    </a>
</div>
```

### 필수 컨벤션

- **제목**: `한국어 이름 (Original Name)` 형식 병기.
- **링크 1순위**: `https://maps.app.goo.gl/...` 짧은 링크가 있으면 이걸 쓰고, 없으면 `https://www.google.com/maps/search/?api=1&query=...` 폴백.
- **target / rel**: 항상 `target="_blank" rel="noopener"`.
- **이미지**: `<img class="card-thumb" ... loading="lazy">` — `loading="lazy"` 빼먹지 말 것.
- **CTA**: 마지막에 `<span class="map-link">구글맵에서 보기</span>` 고정.

## 이미지 URL 규칙

### Wikimedia (1순위)

- 반드시 **`/1280px-...`** thumbnail 사이즈를 사용한다.
- Wikimedia가 2025년부터 thumb 사이즈를 화이트리스트(960/1280/1920 등)로 제한했다. `800px-`, `640px-`, `1024px-` 등은 **HTTP 400** 반환.
- 사용 전 반드시 검증:
  ```bash
  curl -s -o /dev/null -w "%{http_code}\n" -A "Mozilla/5.0 Chrome/120" "<URL>"
  ```
  → `200`이 떠야 한다.
- 원본 파일 페이지에서 파일명을 받아오는 패턴: `https://upload.wikimedia.org/wikipedia/commons/thumb/<a>/<ab>/<Filename>/1280px-<Filename>`.

### Pexels (2순위, 특정 랜드마크가 Wikimedia에 없을 때)

```
https://images.pexels.com/photos/<ID>/pexels-photo-<ID>.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=800
```
- `&w=800`은 Pexels 쿼리 파라미터라 안전 (Wikimedia 규칙과 무관).
- 일반 풍경/도시 사진엔 잘 맞지만 specific 랜드마크는 매칭이 어려울 수 있음 — 그럴 땐 Wikimedia를 먼저 시도.

### 깨졌을 때

전체 Wikimedia thumb URL을 일괄 점검하는 스니펫:
```bash
grep -oE 'https://upload\.wikimedia\.org[^"]+' index.html | while read url; do
  echo "$(curl -s -o /dev/null -w '%{http_code}' -A 'Mozilla/5.0 Chrome/120' "$url") $url"
done
```

## 일정 작성 규칙

- **이동 수단**: 모든 도시 간 / 관광지 간 이동은 **차량(렌터카)** 기준으로 짠다. 기차/대중교통은 사용하지 않음. 단, 한 지역 내에서 도보로 충분히 닿는 거리(예: 베네치아 산 마르코 광장 내 도보 구역, 도시 구도심)는 도보로 표기.
- **여유 있는 페이스**:
  - 하루에 메인 관광지 2~3곳을 넘기지 않는다. 욕심내서 4~5곳 꽉꽉 채우지 말 것.
  - 각 관광지에 충분한 체류 시간(보통 1.5~2시간 이상)을 확보.
  - 이동 사이에는 휴식/사진/즉흥 정차 여지를 위한 버퍼(30~60분)를 둔다.
  - 점심/저녁 시간은 1.5시간 이상 잡는다 — 이탈리아 식사는 빠르게 끝내지 않는다는 전제.
  - 새벽 출발이나 늦은 밤 도착으로 강행하지 않는다. 가능하면 09~10시 출발, 19~20시 이전 숙소 도착.

## 일반 작업 노트

- 새 일자/섹션을 추가할 때는 기존 day 섹션의 timeline 구조를 그대로 따른다 (`timeline-item destination` 외에 식사/이동/숙소용 클래스가 있는지 인접 day 확인).
- 변경 후 GitHub Pages 반영은 보통 30~60초. 캐시 때문에 안 보이면 강력 새로고침(Cmd+Shift+R).
- 배포 URL: https://eddieollogue.github.io/dolomites/
