# 기술 스택 정리 (Tech Stack)

서경대학교 소프트웨어학과 홍보 사이트 — **어떤 부분에 어떤 기술이 쓰였는지** 정리한 문서.

> 한 줄 요약: **React 19 + Vite 8** 기반 SPA로, 스크롤 따라 전개되는 인터랙티브 비주얼 랜딩 페이지.
> 3D는 **Three.js(R3F)**, 스크롤 연출은 **GSAP ScrollTrigger**, UI 모션은 **Framer Motion**,
> 스타일은 **Tailwind v4**, 배포는 **Netlify**.

---

## 1. 토대 (Foundation)

| 부분 | 기술 | 비고 |
|------|------|------|
| UI 라이브러리 | **React** 19 | 함수형 컴포넌트 + Hooks |
| 진입점 렌더링 | `react-dom/client`의 `createRoot` | `src/main.jsx` |
| 빌드 / 개발 서버 | **Vite** 8 | `npm run dev` / `npm run build` |
| React 통합 | `@vitejs/plugin-react` | JSX 변환·HMR |
| 언어 | **JavaScript (JSX)** | TypeScript 미사용 (`@types/*`는 에디터 자동완성용) |
| 린트 | **ESLint** 9 flat config | `eslint.config.js`, react-hooks / react-refresh 플러그인 |
| 배포 | **Netlify** | `netlify.toml` — `dist` 퍼블리시 + SPA 리다이렉트 |

---

## 2. 스타일링 (Styling)

| 부분 | 기술 | 위치 |
|------|------|------|
| 유틸리티 CSS | **Tailwind CSS v4** (`@tailwindcss/vite`) | `className="..."` 전역 |
| 글로벌 CSS / 토큰 | 커스텀 CSS | `src/index.css`, `src/App.css` |
| 디자인 토큰 (색상·글로우) | 일반 JS 상수 | `src/constants/colors.js` (`C`, `GLOW`) |
| 타이밍 상수 | 일반 JS 상수 | `src/constants/timing.js` |
| 웹폰트 | Google Fonts | `JetBrains Mono`, `Inter` (`index.html`) |
| SEO / 공유 미리보기 | Open Graph · Twitter Card 메타태그 | `index.html` |

---

## 3. 핵심 라이브러리가 **실제로 쓰인 부분**

아래는 import 기준으로 추적한 실제 사용처다.

### 🟦 Three.js + React Three Fiber — *WebGL 3D 씬*
`import * as THREE from 'three'` + `import { Canvas, useFrame } from '@react-three/fiber'`

| 파일 | 역할 |
|------|------|
| `src/scenes/Page1/CoreChipScene.jsx` | 코어 → 칩 3D 연출 (Page 1 핵심) |
| `src/scenes/Page1/CoreScene.jsx` | 코어 3D 씬 |

> `@react-three/drei`는 `package.json`에 포함돼 있으나 현재 코드에서 import되는 곳은 없음(향후 사용 여지).

### 🟩 GSAP + ScrollTrigger — *스크롤 기반 시네마틱 연출*
`import gsap from 'gsap'` + `import { ScrollTrigger } from 'gsap/ScrollTrigger'`

| 파일 | 역할 |
|------|------|
| `src/scenes/Page1/TunnelScene.jsx` | 스크롤에 묶인 "터널" 진입 타임라인 연출 |

### 🟪 Framer Motion — *UI 모션 / 스크롤 트랜스폼 / 등장 애니메이션*
프로젝트 전반에서 가장 광범위하게 사용. (`motion`, `useScroll`, `useTransform`, `AnimatePresence`, `useInView`, `useMotionValue`, `useSpring` 등)

| 파일 | 사용 API | 역할 |
|------|----------|------|
| `src/components/GlowCursor.jsx` | `useMotionValue`, `useSpring` | 마우스 따라오는 글로우 커서 |
| `src/components/chrome/NavBar.jsx` | `motion`, `AnimatePresence` | 내비게이션 바 |
| `src/components/ElectricCard.jsx` | `motion` | 카드 호버/모션 |
| `src/components/ScanText.jsx` | `motion` | 스캔 텍스트 효과 |
| `src/scenes/Page1/HeroScene.jsx` | `motion` | 히어로 등장 |
| `src/scenes/Page1/CoreChipScene.jsx` | `framer-motion` | 3D 씬 위 UI 오버레이 |
| `src/scenes/Page1/ComputerScene.jsx` | `useScroll`, `useTransform` | 스크롤 연동 모니터 연출 |
| `src/scenes/Page2/CircuitScene.jsx` | `useScroll`, `useTransform` | PCB/회로 스크롤 연출 |
| `src/scenes/Page2/ChipScene.jsx` | `motion` | 칩 씬 |
| `src/scenes/Page2/CourseNetwork.jsx` | `motion`, `AnimatePresence` | 과목 네트워크 인터랙션 |
| `src/scenes/Page3/SparkScene.jsx` | `motion`, `useInView` | 스파크 등장 연출 |
| `src/scenes/Page3/CareerNetwork.jsx` | `motion`, `AnimatePresence` | 커리어 네트워크 인터랙션 |
| `src/scenes/Page3/FutureScene.jsx` | `motion` | 미래 비전 씬 |

### ⬛ Canvas 2D API (라이브러리 X, 브라우저 네이티브)
`getContext` / `requestAnimationFrame` 기반 직접 구현.

| 파일 | 역할 |
|------|------|
| `src/components/BinaryRain.jsx` | 전역 배경 "이진수 비" 효과 (App 전체 뒤 `-z-10`) |
| `src/scenes/Page1/TunnelScene.jsx` | 터널 캔버스 렌더 |
| `src/scenes/Page3/SparkScene.jsx` | 스파크 파티클 |
| `src/scenes/Page3/FutureScene.jsx` | 미래 씬 캔버스 |

---

## 4. 페이지(섹션)별 스택 매핑

`src/App.jsx`의 구성 순서를 기준으로 정리.

| 섹션 | 컴포넌트 | 주요 스택 |
|------|----------|-----------|
| 전역 배경 | `BinaryRain` | Canvas 2D |
| 전역 | `GlowCursor`, `NavBar` | Framer Motion |
| **Page 1** 현실→터널→코어/칩 | `HeroScene` · `TunnelScene` · `CoreChipScene` | Framer Motion / **GSAP ScrollTrigger** / **Three.js(R3F)** |
| **Page 2** 살아있는 PCB | `CircuitScene` (+ `CourseNetwork`, `ChipScene`) | Framer Motion (스크롤 트랜스폼) |
| **Page 3** 스파크→커리어→미래 | `SparkScene` · `CareerNetwork` · `FutureScene` | Framer Motion / Canvas 2D |

---

## 5. 의존성 원본 (package.json)

**dependencies**
- `react` ^19.2.6 · `react-dom` ^19.2.6
- `three` ^0.184.0 · `@react-three/fiber` ^9.6.1 · `@react-three/drei` ^10.7.7
- `framer-motion` ^12.40.0
- `gsap` ^3.15.0

**devDependencies**
- `vite` ^8.0.12 · `@vitejs/plugin-react` ^6.0.1
- `tailwindcss` ^4.3.1 · `@tailwindcss/vite` ^4.3.1
- `eslint` ^10.3.0 · `@eslint/js` · `eslint-plugin-react-hooks` · `eslint-plugin-react-refresh` · `globals`
- `@types/react` · `@types/react-dom`

**scripts**: `dev` (vite) · `build` (vite build) · `lint` (eslint .) · `preview` (vite preview)
