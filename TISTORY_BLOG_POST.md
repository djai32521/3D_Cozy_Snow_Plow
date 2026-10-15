# [Three.js & AI Studio] 3D 구체 행성 제설 & 보스 배틀러 게임 제작기 (Cozy Plow)

> **"눈 내리는 작은 행성을 청소하며 힐링하는 아늑한 3D 제설 시뮬레이션 & 보스 배틀러!"**  
> Google AI Studio와 Three.js, 그리고 절차적 Web Audio API를 활용해 웹 브라우저에서 바로 플레이 가능한 3D 스노우 플로우 게임 **Cozy Plow(아늑한 제설차 · 雪かき惑星)**를 개발하고 GitHub Pages에 배포한 전 과정을 상세히 공유합니다.

---

## 📌 목차
1. [프로젝트 개요 (Project Overview)](#1-프로젝트-개요)
2. [핵심 게임 플레이 및 특징](#2-핵심-게임-플레이-및-특징)
3. [Google AI Studio 개발 경험 및 프롬프트 엔지니어링](#3-google-ai-studio-개발-경험)
4. [핵심 기술 심층 분석 & 코드 예시](#4-핵심-기술-심층-분석--코드-예시)
   - 4.1 구체 행성 표면 이동 수학 & 직교화(Orthogonalization)
   - 4.2 방향 뒤집힘 없는 병렬 운송(Parallel-Transported) 카메라 시스템
   - 4.3 Three.js 커스텀 쉐이더: 실시간 눈 파임(Displacement) 효과
   - 4.4 Web Audio API 무에셋 절차적 사운드트랙 & 촉각적 SFX
   - 4.5 런타임 한/영 실시간 다국어(i18n) 전환 아키텍처
5. [GitHub Pages 배포 및 자동화 파이프라인](#5-github-pages-배포-및-자동화)
6. [배포 링크 및 리포지토리](#6-배포-링크-및-리포지토리)
7. [마무리 및 개발 후기](#7-마무리-및-개발-후기)

---

## 1. 프로젝트 개요

- **게임명**: Cozy Plow · 아늑한 제설차 (雪かき惑星)
- **장르**: 3D 구체 캐주얼 제설 시뮬레이션 + 보스 배틀러 (Spherical Snow-Plowing & Boss Brawler)
- **개발 도구**: Google AI Studio, TypeScript, Three.js (r128), Web Audio API, Vite, Tailwind CSS, WebSocket, Express
- **지원 언어**: 한국어 (기본) / 영어 (실시간 토글 지원)
- **플레이 환경**: 데스크톱(키보드/마우스) 및 모바일(가상 조이스틱) 완벽 대응 반응형 웹

눈이 소복이 쌓인 작은 행성에 제설차를 몰고 나가 눈을 치우면, 치운 눈들이 차량 뒤편에 굴러가며 거대한 눈덩이로 뭉쳐집니다. 눈덩이가 커질수록 파괴력이 강해지며, 맵 곳곳을 순찰하는 거대 요괴 보스(도깨비 불도저, 서리 골렘, 검은 텐구, 대장군 쇼군)의 공격을 회피하고 눈덩이를 날려(스매시) 물리치는 재미를 결합한 게임입니다.

---

## 2. 핵심 게임 플레이 및 특징

### 🚜 1) 눈을 치우며 눈덩이를 키우는 카타마리 메커니즘
- 행성 위를 달리면 제설기 날(Plow)에 닿은 눈들이 실시간으로 깎여나가고, 수집된 눈은 차량 뒤에 눈덩이로 견인됩니다.
- 눈덩이 반경이 커질수록 시각적으로 웅장해질 뿐 아니라, 공격력 배율(DMG Multiplier)이 기하급수적으로 상승합니다.

### 🦊 2) 개성 넘치는 4명의 애니메이션 파일럿
- **콘찬 (🦊 Kon-Chan)**: `여우불 소용돌이` — 360도 회전하며 넓은 눈을 순식간에 녹이고 주변 보스에게 지속 화염 대미지를 입힙니다.
- **쿠마테츠 (🐻 Kuma-Tetsu)**: `기가 스톰프` — 유압식 충격파로 지면을 강타하여 주변 눈을 부수고 보스를 기절시킵니다.
- **우사기 노바 (🐰 Usagi-Nova)**: `오로라 제트 대시` — 2배 속도로 초음속 빙결 활주를 펼쳐 넓은 길을 개척합니다.
- **가마선인 (🐸 Gama-Sennin)**: `두꺼비 소용돌이` — 인술 회오리를 일으켜 주변 눈을 거대한 눈덩이로 한 번에 빨아들입니다.

### 👹 3) 전조 공격 & 카운터 스매시 전투 시스템
- 보스가 돌진하거나 공격하기 직전 붉은색 텔레그래프(전조)를 띄울 때, 타이밍에 맞춰 **[Space] 키(스매시)**를 적중시키면 **185% 카운터 대미지**와 함께 슬로모션 히트스톱(Hitstop) 및 화면 진동 이펙트가 발동합니다.

### 🎵 4) 외부 음원 파일(MP3) 없는 100% 절차적 사운드
- 게임 용량을 0MB 수준으로 유지하기 위해 외부 오디오 에셋 대신 **브라우저 Web Audio API의 오실레이터, 필터, 노이즈 버퍼**를 조합하여 감성적인 펠트 피아노 멜로디와 사각사각 눈 밟는 소리를 실시간으로 합성합니다.

### 🌐 5) 한국어 / 영어 실시간 원클릭 전환
- 기본 언어는 **한국어**로 시작하며, 화면 상단 또는 게임 중 언제든지 `[L]` 키나 언어 버튼을 누르면 모든 UI, 설명, 보스 상태, 업적 텍스트가 즉시 전환됩니다.

---

## 3. Google AI Studio 개발 경험

Google AI Studio는 아이디어를 실제 프로덕션 수준의 웹 3D 애플리케이션으로 구체화하는 데 믿기지 않는 속도를 제공했습니다.

### 💡 1) 수학적 복잡도의 신속한 해결
구체 행성 게임 개발에서 가장 까다로운 부분은 **구면 좌표계(Spherical Coordinates)에서의 이동 벡터 직교화(Gram-Schmidt Orthogonalization)**와 **카메라 병렬 운송(Parallel Transport)**입니다. 일반적인 평면 게임과 달리, 구체에서는 북극이나 남극을 지나갈 때 카메라 업(Up) 벡터가 뒤집히거나 짐벌락(Gimbal Lock)이 발생하기 쉽습니다.  
AI Studio 프롬프트에 *"구체의 노멀 벡터를 기준으로 카메라 탄젠트 베이시스를 안정적으로 유지하는 오버헤드 3/4 팔로우 뷰를 구현해줘"*라고 지시하자, 안정적인 탄젠트 직교화 알고리즘이 단번에 도출되었습니다.

### ⚡ 2) 무에셋(Zero-Asset) 절차적 오디오 생성
보통 배경음악과 효과음을 위해 수십 MB의 오디오 에셋을 다운로드해야 하지만, AI Studio에 Web Audio API 신시사이저 구조를 프롬프트로 요청하여 **사인파 + 3각파 하모닉스 + 로우패스 필터 엔벨로프**를 활용한 고품질 로우파이 피아노 아르페지에이터를 자바스크립트 클래스로 완전히 내재화했습니다.

### 🔄 3) 실시간 피드백 루프와 빌드 안정성
코드 수정과 동시에 Vite 번들러 및 TypeScript 검증(`compile_applet`)이 백그라운드에서 동작하여, 오타나 런타임 에러를 사전에 차단하며 안전하게 기능을 확장할 수 있었습니다.

---

## 4. 핵심 기술 심층 분석 & 코드 예시

### 4.1 구체 행성 표면 이동 수학 & 직교화

플레이어가 구체 위에서 회전하고 전진할 때, 방향 벡터(`forward`)는 항상 행성 표면의 법선 벡터(`normal`)와 90도 직교를 유지해야 모델이 행성 안으로 파묻히거나 공중에 뜨지 않습니다.

```javascript
// 구체 위 방향 벡터 직교화 함수 (Gram-Schmidt Orthogonalization)
function orthogonalizeForward(normal, forward) {
  // forward 벡터에서 normal 방향의 성분을 투영하여 제거
  forward.addScaledVector(normal, -forward.dot(normal));
  
  // 만약 벡터 길이가 너무 작아지면 기본 축으로 보정
  if (forward.lengthSq() < 1e-6) {
    forward.set(1, 0, 0);
    forward.addScaledVector(normal, -forward.dot(normal));
  }
  forward.normalize();
}

// 행성 표면 위 3D 엔티티 트랜스폼 동기화
function syncEntityTransformOnSphere(group, normal, forward, heightOffset = 0.0) {
  const right = new THREE.Vector3().crossVectors(normal, forward).normalize();
  
  // 3x3 회전 행렬 구성 [Right, Normal, Forward]
  const rotMat = new THREE.Matrix4().makeBasis(right, normal, forward);
  group.quaternion.setFromRotationMatrix(rotMat);
  
  // 행성 중심에서 반지름 + 오프셋 위치로 이동
  group.position.copy(normal).multiplyScalar(PLANET_RADIUS + heightOffset);
}
```

---

### 4.2 방향 뒤집힘 없는 병렬 운송(Parallel-Transported) 카메라

플레이어가 남극이나 북극 쪽으로 달려갈 때 일반 카메라를 사용하면 상하가 뒤집히는 멀미 현상이 생깁니다. 이를 방지하기 위해 플레이어 위치의 탄젠트 평면(Tangent Plane)을 기준으로 카메라를 평행 운송합니다.

```javascript
// 카메라의 탄젠트 로컬 베이시스 동기화
function syncCameraTangentBasis(playerNormal) {
  // 전역 Y축(0, 1, 0)을 플레이어 표면에 투영하여 카메라 '위(Up)' 방향 설정
  _camTangentUp.set(0, 1, 0).addScaledVector(playerNormal, -playerNormal.y);
  if (_camTangentUp.lengthSq() < 1e-5) {
    _camTangentUp.set(0, 0, -1);
  }
  _camTangentUp.normalize();
  
  // 카메라 '오른쪽(Right)' 방향 산출
  _camTangentRight.crossVectors(_camTangentUp, playerNormal).normalize();
}

function updateCamera(dt, elapsed) {
  const player = racersList[activeRacerIdx];
  if (!player) return;

  syncCameraTangentBasis(player.normal);

  // 플레이어 후방 elevated 위치에 안정적인 카메라 위치 지정
  const pSurf = player.normal.clone().multiplyScalar(PLANET_RADIUS);
  _camDesiredPos.copy(pSurf)
    .addScaledVector(player.normal, 14.0)          // 고도
    .addScaledVector(_camTangentUp, -18.5);        // 후방 오프셋

  // 부드러운 카메라 보간 (Damping)
  camera.position.lerp(_camDesiredPos, Math.min(1.0, dt * 6.0));
  _camCurrentLook.lerp(pSurf, Math.min(1.0, dt * 8.0));
  camera.up.copy(_camTangentUp);
  camera.lookAt(_camCurrentLook);
}
```

---

### 4.3 Three.js 커스텀 쉐이더: 실시간 눈 파임 효과

제설차가 지나간 자리는 눈이 깎이고 원래 행성의 지표면 텍스처와 코어 색상이 드러나야 합니다. 텍스처 캔버스 대신 **GLSL 버텍스 & 프래그먼트 쉐이더**에 눈 파임 반경 유니폼을 전달하여 실시간으로 음영과 변형을 처리했습니다.

```glsl
// Custom Snow Vertex Shader (GLSL)
uniform float uTime;
uniform vec3 uCarvedPoints[32]; // 제설차가 깎아낸 위치 배열
varying vec3 vNormal;
varying vec3 vWorldPosition;
varying float vSnowDepth;

void main() {
  vNormal = normalize(normalMatrix * normal);
  vec4 worldPos = modelMatrix * vec4(position, 1.0);
  vWorldPosition = worldPos.xyz;

  // 기본 눈 두께 계산 (노이즈 기반)
  float depth = 1.0;
  for(int i = 0; i < 32; i++) {
    float dist = distance(worldPos.xyz, uCarvedPoints[i]);
    if (dist < 2.5) {
      depth *= smoothstep(0.5, 2.5, dist); // 제설된 영역의 눈을 깎아냄
    }
  }
  vSnowDepth = depth;

  // 표면 노멀 방향으로 눈 두께만큼 버텍스 돌출
  vec3 displacedPosition = position + normal * (vSnowDepth * 0.35);
  gl_Position = projectionMatrix * modelViewMatrix * vec4(displacedPosition, 1.0);
}
```

---

### 4.4 Web Audio API 무에셋 절차적 사운드트랙 & SFX

단 한 장의 MP3/WAV 파일도 없이 브라우저 기본 내장 Web Audio API로 따뜻한 로우파이 피아노와 타격 사운드를 구현했습니다.

```javascript
class YukiAudioEngine {
  constructor() {
    this.ctx = null;
    this.isMuted = false;
  }

  init() {
    if (!this.ctx) {
      this.ctx = new (window.AudioContext || window.webkitAudioContext)();
    }
    if (this.ctx.state === 'suspended') {
      this.ctx.resume();
    }
  }

  // 따뜻한 펠트 피아노 음 합성 (Warm Felt-Piano Voice)
  playPianoNote(freq, velocity = 0.5, duration = 1.2) {
    if (!this.ctx || this.isMuted) return;
    const now = this.ctx.currentTime;
    
    const osc = this.ctx.createOscillator();
    const gain = this.ctx.createGain();
    const filter = this.ctx.createBiquadFilter();

    // 삼각파로 부드러운 어쿠스틱 피아노 톤 생성
    osc.type = 'triangle';
    osc.frequency.setValueAtTime(freq, now);

    // 고주파 차단 필터로 아늑한 로우파이(Lo-Fi) 질감 부여
    filter.type = 'lowpass';
    filter.frequency.setValueAtTime(1400, now);
    filter.frequency.exponentialRampToValueAtTime(320, now + duration);

    // 자연스러운 피아노 타건 엔벨로프 (ADSR)
    gain.gain.setValueAtTime(0.001, now);
    gain.gain.linearRampToValueAtTime(velocity * 0.45, now + 0.02);
    gain.gain.exponentialRampToValueAtTime(0.0001, now + duration);

    osc.connect(filter);
    filter.connect(gain);
    gain.connect(this.ctx.destination);

    osc.start(now);
    osc.stop(now + duration);
  }

  // 눈덩이 타격 및 카운터 성공 충격음
  playSmashImpact(isCounter = false) {
    if (!this.ctx || this.isMuted) return;
    const now = this.ctx.currentTime;
    const osc = this.ctx.createOscillator();
    const gain = this.ctx.createGain();

    osc.type = isCounter ? 'sawtooth' : 'sine';
    osc.frequency.setValueAtTime(isCounter ? 320 : 180, now);
    osc.frequency.exponentialRampToValueAtTime(40, now + 0.28);

    gain.gain.setValueAtTime(isCounter ? 0.6 : 0.4, now);
    gain.gain.exponentialRampToValueAtTime(0.001, now + 0.28);

    osc.connect(gain);
    gain.connect(this.ctx.destination);
    osc.start(now);
    osc.stop(now + 0.28);
  }
}
```

---

### 4.5 런타임 한/영 실시간 다국어(i18n) 시스템

새로고침 없이 모든 UI와 텍스트를 원클릭으로 전환할 수 있도록 중앙 집중형 `I18N` 딕셔너리와 `applyLanguage()` 함수를 설계했습니다.

```javascript
let currentLang = localStorage.getItem('cozy_plow_lang') || 'ko'; // 기본값 한국어

const I18N = {
  ko: {
    langToggleBtn: '🇰🇷 한국어',
    hudLangLabel: '🇰🇷 한국어',
    selectKicker: '제설 파일럿을 선택하세요 · [← / →] 키 또는 클릭하여 미리보기',
    selectTitle: '코지 플로우 · Cozy Plow',
    beginMission: '제설 작전 시작',
    pointsLeaderboard: '포인트 순위표',
    // ... 모든 UI 텍스트 수록
  },
  en: {
    langToggleBtn: '🇬🇧 English',
    hudLangLabel: '🇬🇧 EN',
    selectKicker: 'Choose Your Snow-Plow Pilot · [← / →] or Click to Preview',
    selectTitle: 'Cozy Plow',
    beginMission: 'Begin Snow Clearing',
    pointsLeaderboard: 'Points Leaderboard',
    // ...
  }
};

function applyLanguage(lang) {
  currentLang = (lang === 'en' ? 'en' : 'ko');
  localStorage.setItem('cozy_plow_lang', currentLang);
  document.documentElement.lang = currentLang;
  const t = I18N[currentLang];

  // DOM 텍스트 실시간 반영
  document.getElementById('select-kicker-text').textContent = t.selectKicker;
  document.getElementById('start-mission-btn').innerHTML = `${t.beginMission} <span class="desktop-enter-hint">[Enter]</span>`;
  // ...
}
```

---

## 5. GitHub Pages 배포 및 자동화

### 5.1 Vite Base Path 설정 (`vite.config.ts`)
GitHub Pages의 서브디렉토리 경로(`https://<user>.github.io/<repo>/`)에서도 에셋이 깨지지 않고 정상 로드되도록 상대 경로(`base: './'`)를 설정했습니다.

```typescript
import tailwindcss from '@tailwindcss/vite';
import react from '@vitejs/plugin-react';
import path from 'path';
import { defineConfig } from 'vite';

export default defineConfig(() => {
  return {
    base: './', // GitHub Pages 서브디렉토리 호환
    plugins: [react(), tailwindcss()],
    resolve: {
      alias: {
        '@': path.resolve(__dirname, '.'),
      },
    },
  };
});
```

### 5.2 GitHub Actions 워크플로우 (`.github/workflows/deploy.yml`)
`main` 브랜치에 코드가 푸시되면 자동으로 빌드를 수행하고 최신 빌드 결과를 GitHub Pages로 자동 배포하는 CI/CD 파이프라인을 구축했습니다.

---

## 6. 배포 링크 및 리포지토리

- 🌐 **GitHub Pages 플레이 링크**: [https://djai32521.github.io/3D_Cozy_Snow_Plow/](https://djai32521.github.io/3D_Cozy_Snow_Plow/)
- 💻 **GitHub 소스코드 리포지토리**: [https://github.com/djai32521/3D_Cozy_Snow_Plow](https://github.com/djai32521/3D_Cozy_Snow_Plow)

---

## 7. 마무리 및 개발 후기

Google AI Studio와 Three.js의 결합은 상상했던 인터랙티브 3D 아이디어를 현실로 만드는 데 최고의 시너지를 발휘했습니다. 
- 복잡한 3D 구면 좌표계 연산과 수학 공식을 자연어로 설명하고 검증받을 수 있었으며,
- 웹 오디오 신시사이저를 통해 번들 용량 걱정 없이 감성적인 사운드를 채워 넣을 수 있었습니다.
- 무엇보다 한국어와 영어를 자유롭게 넘나드는 다국어 지원까지 갖추어 전 세계 게이머가 편안하게 즐길 수 있는 완성도 높은 인디 웹 게임을 완성했습니다.

지금 바로 브라우저를 열고 작은 행성에서 눈을 굴리며 힐링 타임을 가져보세요! ❄️🦊⛄
