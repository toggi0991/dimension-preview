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
- [ ] Iris 지형 함수(아래 5장) 중 핵심 서브셋을 JS 노이즈로 이식 → heightAt() 대체

> ⚠️ **용어 정정**: 위 3장에서 "Iris 셰이더팩"이라 쓴 부분은 착오. 이 프로젝트가 재현하려는 대상은
> **Volmit Software의 Iris = Paper/Spigot용 월드 생성(worldgen) 플러그인**이다(셰이더 아님).
> 셰이더는 지형 모양을 바꾸지 못함. 지형 변형 로직은 아래 Iris worldgen 함수 체계를 따른다.

## 5. Iris(Volmit) 지형 변형 함수 조사 (2026-07-27)

리포: https://github.com/VolmitSoftware/Iris · 문서: https://docs.volmit.com/iris
저수준 노이즈 수학은 Auburn의 **FastNoise/FastNoiseLite**를 Iris의 **CNG(Coherent Noise Generator)** 로 감싼 것.
지형 변형은 아래 **4계층**으로 구성된다.

### (A) NoiseStyle — 노이즈 "모양" 함수 (총 140개, `NoiseStyle.java`)
- **기본 생성기**: `SIMPLEX`, `PERLIN`, `CELLULAR`(=Voronoi/Worley), `CUBIC`, `GLOB`, `CLOVER`,
  `VASCULAR`, `IRIS`(커스텀), `FLAT`, `STATIC`(=white noise), `NOWHERE`(계열)
- **프랙탈 변형**: `FRACTAL_FBM_*`(층상 디테일), `FRACTAL_BILLOW_*`(둥근 구름형),
  `FRACTAL_RM_*`(=Rigid Multi, 날카로운 능선 — 산맥용) → SIMPLEX/PERLIN/IRIS/CUBIC 위에 적용
- **옥타브 사전스택**: `BI/TRI/QUAD/QUINT/SEX/SEPT/OCT/NON/VIGOCTAVE_*` (2~20 옥타브 미리 합성)
- **밀도 변형**: `_HALF`, `_DOUBLE`, `_THICK` 접미사
- **보간 내장 스타일**: 이름에 `BILINEAR` / `BICUBIC` / `HERMITE` / `STARCAST_3|6|9|12` 포함

### (B) InterpolationMethod — 샘플 사이 스무딩 (총 27개, `util/interpolation/InterpolationMethod.java`)
`NONE`, `BILINEAR`, `BICUBIC`, `HERMITE`(+`_TENSE`/`_LOOSE`+bias 변형),
`CATMULL_ROM_SPLINE`, `STARCAST_3|6|9|12`(+`BILINEAR_`/`HERMITE_` 접두),
`BILINEAR_BEZIER`, `BILINEAR_PARAMETRIC_1_5|2|4`

### (C) IrisNoiseGenerator — 단일 노이즈에 거는 변형 파라미터 (`IrisNoiseGenerator.java`)
- `zoom`(주파수/스케일), `opacity`(출력 배수), `offsetX/Y/Z`(offsetY는 지형생성엔 지양)
- `exponent`(noise^exp; >1 평탄화, <1 봉우리 강조)
- `octaves`(줌이 다른 여러 생성기 합산)
- **`fracture`(도메인 워핑)**: 자식 노이즈로 입력 좌표를 뒤틀어 흐르는/유기적 패턴 생성 ← 핵심 변형기법
- 출력 곡선: `negative`, `parametric`, `bezier`, `sinCentered` / `seed`, `enabled`

### (D) IrisGenerator — 여러 노이즈를 합성해 최종 높이 산출 (`IrisGenerator.java`)
- `composite`(=IrisNoiseGenerator 리스트; 기본 합산, `multiplicitive`면 곱셈)
- `interpolator`(=InterpolationMethod + 스케일), `zoom`, `opacity`, `offsetX/Z`, `seed`
- **절벽/계단화**: `cliffHeightMin/Max` + `cliffHeightGenerator`
- **셀 프랙처(Voronoi 균열)**: `cellFractureZoom`, `cellFractureShuffle`, `cellFractureHeight`,
  `cellPercentSize`(0~1; 0.1이면 두꺼운 정맥+작은 셀) → 판상/고원형 지형

### 웹 이식 우선순위 (heightAt 대체용 최소 서브셋)
1. 기본: `SIMPLEX` + `PERLIN` (JS simplex-noise 등)
2. 프랙탈: `FBM` / `BILLOW` / `RIGID_MULTI` + `octaves`
3. 파라미터: `zoom`, `exponent`, `opacity`, `offsetX/Z`
4. `CELLULAR`(Voronoi) — 셀/고원형
5. `fracture`(도메인 워핑) — 자연스러운 뒤틀림

## 6. Export JSON 정확 스키마 (소스 확정, 2026-07-27)

export 시 이 구조를 따라야 Iris가 받아들임 (Gson: Java 필드명 = JSON 키).

- **`IrisNoiseGenerator.style`은 문자열이 아니라 `IrisGeneratorStyle` 객체** (초기 버그: 문자열로 넣었음 → 수정 완료)
- **IrisGeneratorStyle** (`IrisGeneratorStyle.java`): `style`(NoiseStyle enum, 기본 FLAT),
  `zoom`, `exponent`, `cellularFrequency`, `cellularZoom`, `multiplier`("parent가 fracture일 때만"),
  `axialFracturing`, `expression`/`script`/`imageMap`, **`fracture`(중첩 IrisGeneratorStyle)** ← 도메인 워핑
- **IrisInterpolator** (`util/interpolation` 참조 아니라 `engine/object/IrisInterpolator.java`):
  `function`(InterpolationMethod, 기본 `BILINEAR_STARCAST_6`), `horizontalScale`(기본 7)
- **IrisNoiseGenerator**: `zoom`, `opacity`, `offsetX/Y/Z`, `seed`, `negative`, `parametric`,
  `bezier`, `sinCentered`, `exponent`, `enabled`, `octaves`, `style`(IrisGeneratorStyle), `fracture`(IrisNoiseGenerator 리스트)
- **IrisGenerator**: `zoom`, `opacity`, `interpolator`, `composite`(IrisNoiseGenerator 리스트),
  `cliffHeightMin/Max`, `cliffHeightGenerator`, `cellFractureZoom/Shuffle/Height`, `cellPercentSize`, `multiplicitive`, `offsetX/Z`, `seed`

### 계산 규칙 (getHeight/getNoise, 소스 확정) — "게임에서 더 평평" 원인
- `IrisNoiseGenerator.getNoise`: `n = CNG.fitDouble(0, opacity_noisegen=1, x/zoom, z/zoom)` → 0..1,
  이어서 `n = pow(n, exponent)`
- `IrisGenerator.getHeight`: `v = (n / tp) * generator.opacity`  (tp = noisegen.opacity, 기본 1)
  → 최종 높이 = `pow(noise01, exponent) * opacity`
- **좌표는 zoom으로 3번 나뉜다**: `generator.zoom × noisegen.zoom × style.zoom` (모두 곱해짐)

⚠️ **초기 버그**: export가 zoom을 세 레벨 전부에 넣어 실효 zoom이 2³=8배 → 파장 8배 → 게임에서
훨씬 평평. exponent도 noisegen+style 이중 적용. **수정**: zoom은 `generator.zoom` 한 곳,
exponent는 `composite[].exponent` 한 곳만. style은 이름만(zoom/exponent=기본 1).

올바른 최소 export 예 (수정본):
```json
{
  "zoom": 2, "opacity": 24,
  "interpolator": { "function": "BILINEAR_STARCAST_6", "horizontalScale": 7 },
  "composite": [
    { "octaves": 4, "exponent": 1,
      "style": { "style": "FRACTAL_RM_SIMPLEX",
                 "fracture": { "style": "SIMPLEX", "multiplier": 12 } } }
  ]
}
```
(잔여 보정: CNG FBM의 옥타브 gain/기본 주파수가 FastNoiseLite와 미세하게 달라 절대 스케일은
2차적으로 다를 수 있음. 필요 시 CNG 기본 주파수 확인해 미리보기 `0.06` 상수 보정.)
