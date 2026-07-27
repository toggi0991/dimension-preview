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
- [ ] **biome input 추가** — min/max뿐 아니라 바이옴 자체 입력(이름·derivative·layers/palette·rarity·
      generators 링크 등)을 폼에 넣어 바이옴 단위로 구성 가능하게.
- [ ] **아코디언 UI로 입력 분리** — `biome input` 섹션과 `generator input` 섹션을 접었다 펴는
      아코디언으로 나눠서 폼 정리.
- [ ] **내보내기 탭 분리** — export 모달 상단에 `biome 코드` / `generator 코드`를 클릭 전환(탭)해
      각각 복사할 수 있게. (지금은 generator JSON 하나만 나옴)
- [ ] **[보류]** 완전한 Iris 팩(dimension+region+biome 폴더 구조)으로 감싸 ZIP 다운로드
      → 게임에 바로 적용. *보류 사유(2026-07-27): 아직 필요성 못 느낌. 현재는 generator JSON 복사로 충분.*
- [ ] 실제 블록 텍스처 에셋 확보 및 라이선스 확인 (플레이스홀더 → 텍스처)
- [ ] 폼 항목 확장 (바이옴, 안개/날씨, 구조물 등 — MCStacker처럼 세분화)
- [ ] "렌더 이미지 저장/내보내기"(PNG) 기능
- [ ] CDN 대신 로컬 번들로 오프라인 동작 여부 결정
- [ ] Commercial Use 가이드라인 원문 정독 후 수익화 가능 여부 확정
- [x] Iris 지형 함수(5장) 핵심 서브셋을 FastNoiseLite로 이식 → heightAt() 대체 (완료)
- [x] export JSON을 실제 Iris 스키마·계산에 맞춤(6장) + 스케일 보정(기저 0.01) (완료)

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
### 절대 스케일 보정 (기저 주파수 확정, 2026-07-27)
- Iris 노이즈 체인: `SimplexNoise`/`FractalFBMSimplexNoise` 등 → `new FastNoiseDouble(seed)`,
  **SetFrequency 호출 없음** → `m_frequency` = **기본 0.01**.
  `GetSimplex(x,z) = SingleSimplex(seed, x*0.01, z*0.01)`. 좌표는 그 전에 `/gen.zoom`.
- 즉 **Iris 실효 주파수 = 0.01 / gen.zoom**.
- 미리보기가 `0.06`을 써서 Iris보다 **약 6배 촘촘** → Iris가 6배 평평해 보였음.
- **수정**: 미리보기 주파수를 `0.01 / zoom`으로 (Iris와 동일), 슬라이더 zoom = 그대로 `gen.zoom`으로 export.
  → 슬라이더 값이 곧 Iris gen.zoom, 스케일 일치. 기본값 zoom 0.5 / size 64로 조정.
- **스케일 배율 실측(역산, 2026-07-27)**: Iris `SingleSimplex` = 표준 Perlin 심플렉스
  = MIT `simplex-noise`와 동일 격자. 브라우저에서 두 노이즈 파장 측정(주파수 0.01):
  - OpenSimplex2(FastNoiseLite, 미리보기): 파장 **137.7**
  - 클래식 Simplex(=Iris): 파장 **155.4**
  - **정확 배율 = 137.7/155.4 = 0.886** → 미리보기 주파수를 `0.01×0.886/zoom`로 낮춰 스케일 일치.
    export gen.zoom = 슬라이더 그대로(K=1). (이전 K=2 추정은 오답이라 폐기.)
- ⚠️ **함의**: 스케일 차이는 겨우 13%(그것도 Iris가 더 완만) → 게임에서 "뾰족/높음"은
  **zoom이 아니라 진폭/프랙탈 옥타브 문제**일 가능성. 다음에 FBM 진폭 분포 비교 필요.

### 타입별 스케일 실측 (2026-07-27) — 배율은 타입마다 다름!
FastNoiseLite 각 타입 파장(주파수 0.01): OpenSimplex2 137.7 / Perlin 194.6(×1.41) /
ValueCubic 382.5(×2.78) / Value 398.7(×2.90) / Cellular 측정불가(셀값). 클래식 Simplex(Iris) 155.4.
- **한 배율(0.886)을 전 타입에 적용하면 SIMPLEX만 맞고 나머지는 틀림** → 타입별 배율로 수정:
  - `OpenSimplex2`(SIMPLEX/IRIS/NOWHERE류) → **0.886** (Iris 등가물 실측, 정확)
  - Perlin/Cellular/ValueCubic/Value → **1.0** (근사: FNL≈옛 FastNoise 같은 알고리즘 가정)
- 비-SIMPLEX 타입: 측정 전용으로 옛 FastNoise 격자(Perlin/Value/Cubic)를 브라우저에 주입해
  **직접 실측 완료**(배포엔 미포함). FNL÷Iris 파장비:
  - OpenSimplex2 **0.886** · Perlin **0.965** · ValueCubic **0.989** · Value **0.996** · Cellular **1.0**
  - 격자가 같아 대부분 ~1.0 근처(1~3.5% 차)지만 실측값으로 확정 적용. → `TYPE_SCALE` 맵.

### FBM 진폭 분포 실측 (2026-07-27)
FastNoiseLite FBm vs Iris식 클래식-심플렉스 FBM(옥타브4·gain0.5·lac2·bounding), 0..1 매핑:
- sd 0.167 vs **0.134**, p99 0.842 vs 0.782, 0.8초과 3% vs **0.6%**
- → **Iris FBM이 더 좁음(높은 봉우리 드묾)**. 미리보기를 `FBM_AMP_MATCH=0.8`로 0.5 중심 좁혀 일치.
- ⚠️ **핵심 결론**: 스케일·진폭 **둘 다 Iris ≤ 미리보기**(더 완만/낮음). 사용자가 본 "게임이 더 높게 쏫음"은
  base 노이즈가 아님.

## 7. 진짜 원인 = opacity 배율 오류 (실제 팩 파일에서 확정, 2026-07-27)
사용자 팩 `Z:\...\Iris\packs\simple` 직접 확인. 높이 파이프라인:
- 바이옴이 생성기를 `{generator, min, max}`로 연결 (plains 1~5, hills 8~24, ocean -20~-6).
- **`IrisBiomeGeneratorLink.getHeight`**: `g = generator.getHeight(...); g = clamp(g, 0, 1); return lerp(min, max, g);`
  → **생성기 출력을 [0,1]로 clamp 후** 바이옴 min/max로 lerp.
- 그런데 `IrisGenerator.getHeight = noise × opacity`. **opacity=23이면 출력 0~23 → clamp에서 noise>0.043은 전부 1로 포화**
  → `lerp(min,max,1)=max` → 지형이 거의 전부 바이옴 최대높이에 붙음 = **"높게 쏫는 지형이 자주"의 진짜 원인**.
- **우리 툴 버그**: export가 `opacity: Height슬라이더값`(23,31)을 냈음 → 포화 유발.
- **수정**: export **`opacity: 1` 고정**. 실제 높이는 바이옴 min/max 소관. (Height 슬라이더는 미리보기 전용.)
- ✅ 이게 스케일/진폭보다 상위 원인. 스케일·진폭 보정도 유효하지만 이 opacity가 진짜 범인이었음.
