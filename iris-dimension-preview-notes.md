# Minecraft Iris 디멘션 미리보기 — 프로젝트 노트

> 신규 아이디어: 마인크래프트 Iris(셰이더) 환경에서의 커스텀 디멘션 미리보기 제작
> 상태: 자료 조사 단계

---

## 1. 2차 창작 / 배포 정책 확인 (2026-07-27 조사)

### 결론 (요약)
- **모드/플러그인 제작·배포는 원칙적으로 허용된다.** Mojang의 정책은 팬 제작 모드·플러그인·서버·팬사이트 등을 넓게 허용한다.
- 단, **"Mod"의 정의**를 지켜야 한다: *네가 직접 만든 오리지널 결과물이며, 마인크래프트의 저작권 있는 코드·콘텐츠의 상당 부분을 포함하지 않아야 한다.* (최종 판단 권한은 Mojang에 있음)
- **마인크래프트 자체를 개조한 버전(Modded Versions)은 배포 금지.** 게임 파일/클라이언트를 통째로 재배포하면 안 됨. 순수 Mod(추가물)만 배포 가능.

### 상표/브랜드 사용
- 공식 Minecraft **로고·브랜드 에셋을 마케팅 자료에 사용 불가.**
- Minecraft 이름은 **부제/2차 명칭/설명(secondary name/title/description)에서만** 사용 가능.
  - 예: OK → "MyPreview, a tool for Minecraft" / NG → 제목·로고에 Minecraft 단독 브랜딩

### 수익화 (Commercial Use)
- 커뮤니티에 공유하는 행위(무료 포함)는 Mojang 기준상 "commercial"로 간주되어 **Commercial Use 가이드라인 적용.**
- 개인/커뮤니티 팬 프로젝트의 수익화(도네이션, 광고 등)는 조건부 허용되는 편이나, 세부 조항 확인 필요.
- 기업/브랜드/광고대행사/정치 목적 등은 Minecraft를 무관한 제품·서비스 홍보에 이용 불가.

### 확인해야 할 원문 (직접 재확인 권장)
- Usage Guidelines: https://www.minecraft.net/en-us/usage-guidelines
- Commercial Usage Guidelines (업데이트): https://www.minecraft.net/en-us/article/commercial-usage-guidelines-updated
- EULA: https://www.minecraft.net/en-us/eula

---

## 2. Iris 셰이더 라이선스 (별도 확인 필요)

- **Iris 본체: GNU LGPLv3** — 읽기/배포/수정 자유(라이선스 조건 준수 시).
- 의존성 `glsl-transformer`: **AGPLv3** → 배포 방식에 영향 줄 수 있음. **주의.**
- Iris 컴파일 버전을 자체 공개 배포(호스팅)할 경우, 법적 의무는 아니나 **개발팀에 사전 허가 요청**을 권고함.
- 저장소: https://github.com/IrisShaders/Iris  /  LICENSE 확인 필수

---

## 3. 결정된 방향 (2026-07-27)

- **산출물: 웹 뷰어.** MCStacker(https://mcstacker.net) 스타일 — 폼에서 값을 선택하면
  명령어 대신 **실시간 렌더 이미지(3D 미리보기)** 를 보여준다.
- **렌더 방식: Three.js(WebGL) 근사 렌더링.** Iris 셰이더팩을 브라우저에서 그대로 실행하는 건
  비현실적이므로, 그림자·라이팅·톤매핑으로 셰이더 "느낌"만 웹셰이더로 재현.
- **스캐폴드 파일: `dimension-preview.html`** (단일 HTML, Three.js CDN importmap).
  - 좌: 폼(블록/지형/시간대/셰이더 프리셋), 우: OrbitControls 3D 캔버스
  - 현재 블록은 **색상 플레이스홀더** — 실제 텍스처는 추후 교체
  - deepslate는 worldgen JSON 기반이라 이번 폼-드리븐 방식엔 당장 안 씀. 향후 지형 생성 고도화 시 재검토.

## 4. TODO (다음 단계)
- [ ] 실제 블록 텍스처 에셋 확보 및 라이선스 확인 (플레이스홀더 → 텍스처)
- [ ] 폼 항목 확장 (바이옴, 안개/날씨, 구조물 등 — MCStacker처럼 세분화)
- [ ] "렌더 이미지 저장/내보내기"(PNG) 기능
- [ ] CDN 대신 로컬 번들로 오프라인 동작 여부 결정
- [ ] Commercial Use 가이드라인 원문 정독 후 수익화 가능 여부 확정
