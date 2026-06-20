# Diffusion Pipeline

Text-to-Image 생성 파이프라인 — SDXL + Claude API

## 구조

```
사용자 텍스트 입력 (한국어/영어)
        ↓
Claude API (프롬프트 최적화)
        ↓
SDXL (이미지 생성)
        ↓
결과 이미지 반환
```

## 환경

- GPU: NVIDIA RTX 2000 Ada (16GB VRAM)
- 모델: Stable Diffusion XL (SDXL)
- API: Claude API (Anthropic)
- 서버: FastAPI

## 디렉토리

```
├── app/          # FastAPI 서버 코드
├── models/       # SDXL 모델 파일
└── outputs/      # 생성된 이미지
```

## 실행 방법

추후 작성 예정
