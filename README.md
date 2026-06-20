# Text to Image Pipeline

한국어 또는 영어 텍스트를 입력하면 **Claude API**가 프롬프트를 최적화하고 **FLUX.1-schnell**이 고품질 이미지를 생성하는 AI 파이프라인

---

## 프로젝트 개요

일반 사용자가 "석양이 지는 바닷가" 같은 자연스러운 문장을 입력하면, Claude API가 이를 FLUX.1에 최적화된 영어 프롬프트로 변환하고 이미지를 생성합니다. 한국어 입력을 자동으로 처리하기 때문에 별도의 프롬프트 작성 지식이 필요 없습니다.

---

## 아키텍처

```
사용자 텍스트 입력 (한국어/영어)
            ↓
    [ Claude API ]
    claude-sonnet-4-6
    프롬프트 최적화 + 영어 변환
    스타일/조명/구도 키워드 추가
            ↓
    [ HuggingFace API ]
    FLUX.1-schnell
    1024x1024 고품질 이미지 생성
            ↓
    이미지 파일 저장 + 반환
```

---

## 사용 기술 스택

| 기술 | 버전/모델 | 역할 |
|------|-----------|------|
| **Claude API** | claude-sonnet-4-6 | 자연어 → FLUX 최적화 프롬프트 변환 |
| **FLUX.1-schnell** | black-forest-labs | 텍스트 → 이미지 생성 |
| **HuggingFace Inference API** | router.huggingface.co | FLUX 모델 호스팅 |
| **FastAPI** | 최신 | REST API 서버 |
| **Pillow** | 최신 | 이미지 저장 처리 |
| **RunPod** | RTX 2000 Ada (VRAM 16GB) | GPU 서버 환경 |

---

## 디렉토리 구조

```
claude-flux-pipeline/
├── app/
│   ├── main.py              # FastAPI 서버 및 엔드포인트 정의
│   ├── prompt_optimizer.py  # Claude API로 프롬프트 최적화
│   └── image_generator.py   # HuggingFace FLUX.1 API 호출
├── outputs/                 # 생성된 이미지 저장 디렉토리
├── models/                  # 로컬 모델 저장 (미사용)
├── requirements.txt         # Python 패키지 목록
├── .env.example             # 환경 변수 템플릿
└── README.md
```

---

## API 엔드포인트

| 메서드 | 경로 | 설명 |
|--------|------|------|
| `GET` | `/` | 서버 상태 확인 |
| `POST` | `/generate` | 텍스트 → 이미지 생성 |
| `GET` | `/image/{filename}` | 생성된 이미지 파일 조회 |
| `GET` | `/docs` | Swagger UI (API 문서 및 테스트) |

---

## 요청 / 응답 예시

### 이미지 생성

```bash
curl -X POST http://localhost:8000/generate \
  -H "Content-Type: application/json" \
  -d '{"text": "석양이 지는 바닷가"}'
```

**응답:**

```json
{
  "original_input": "석양이 지는 바닷가",
  "optimized_prompt": "golden sunset over ocean beach, warm orange and pink sky, gentle waves, wet sand reflections, dramatic lighting, silhouette of rocks, cinematic atmosphere, high quality, detailed, 8k, photorealistic",
  "image_filename": "a3f1c2d4e5b6a7b8.png"
}
```

### 생성된 이미지 조회

```bash
curl http://localhost:8000/image/a3f1c2d4e5b6a7b8.png --output result.png
```

---

## 실행 방법

### 1. 환경 변수 설정

```bash
cp .env.example .env
```

`.env` 파일을 열고 API 키를 입력합니다:

```
ANTHROPIC_API_KEY=sk-ant-...
HF_TOKEN=hf_...
```

### 2. 패키지 설치

```bash
pip install -r requirements.txt
```

### 3. 서버 실행

```bash
cd app
uvicorn main:app --host 0.0.0.0 --port 8000
```

### 4. 테스트

브라우저에서 `http://localhost:8000/docs` 접속하면 Swagger UI로 바로 테스트 가능

---

## 환경 변수

| 변수 | 설명 | 발급 위치 |
|------|------|-----------|
| `ANTHROPIC_API_KEY` | Claude API 인증 키 | console.anthropic.com |
| `HF_TOKEN` | HuggingFace API 토큰 | huggingface.co/settings/tokens |

---

## 주요 특징

- **한국어 완벽 지원** — 한국어 입력을 Claude가 자동으로 영어 프롬프트로 변환
- **프롬프트 자동 최적화** — 단순 문장을 스타일/조명/품질 키워드가 포함된 전문 프롬프트로 확장
- **1024×1024 고해상도** — FLUX.1-schnell으로 고품질 이미지 생성
- **REST API** — 어떤 클라이언트에서도 HTTP 요청으로 사용 가능
