# Results

## 1. 실험 요약
- 저장소: exp-embeddings-browser-throughput
- 커밋 해시: 07ce4fa
- 실험 일시: 2026-04-22T07:23:16.757Z -> 2026-04-22T07:23:20.410Z
- 담당자: ai-webgpu-lab
- 실험 유형: `ml`
- 상태: `success`

## 2. 질문
- cold index build와 warm query reuse 차이가 브라우저 내 deterministic fixture에서도 명확하게 드러나는가
- 같은 fixture에서 recall@10과 throughput이 안정적으로 재현되는가
- cache state를 분리 기록했을 때 이후 실제 embedder 교체 전 baseline으로 쓸 수 있는가

## 3. 실행 환경
### 브라우저
- 이름: Chrome
- 버전: 147.0.7727.15

### 운영체제
- OS: Linux
- 버전: unknown

### 디바이스
- 장치명: Linux x86_64
- device class: `desktop-high`
- CPU: 16 threads
- 메모리: 16 GB
- 전원 상태: `unknown`

### GPU / 실행 모드
- adapter: synthetic-webgpu-profile, wasm-fallback-simulated
- backend: `webgpu, wasm`
- fallback triggered: `false, true`
- worker mode: `main`
- cache state: `cold, warm`
- required features: ["shader-f16"], []
- limits snapshot: {}

## 4. 워크로드 정의
- 시나리오 이름: Cold Index / WebGPU, Warm Query / WebGPU, Cold Index / Fallback, Warm Query / Fallback
- 입력 프로필: 6-docs-3-queries
- 데이터 크기: synthetic fixture; batchSize=3; docs=6; queries=3; cacheState=cold; executionMode=webgpu; backend=webgpu; automation=playwright-chromium, synthetic fixture; batchSize=3; docs=6; queries=3; cacheState=warm; executionMode=webgpu; backend=webgpu; automation=playwright-chromium, synthetic fixture; batchSize=3; docs=6; queries=3; cacheState=cold; executionMode=fallback; backend=wasm; automation=playwright-chromium, synthetic fixture; batchSize=3; docs=6; queries=3; cacheState=warm; executionMode=fallback; backend=wasm; automation=playwright-chromium
- dataset: docs-fixture-v1
- model_id 또는 renderer: synthetic-browser-embedder-v1
- 양자화/정밀도: -
- resolution: -
- context_tokens: -
- output_tokens: -

## 5. 측정 지표
### 공통
- time_to_interactive_ms: 89.3 ~ 195 ms
- init_ms: 0 ~ 80.5 ms
- success_rate: 1
- peak_memory_note: 16 GB reported by browser
- error_type: -

### Embeddings / ML
- docs_per_sec: 74.53 ~ 6000
- queries_per_sec: 133.33 ~ 227.27
- p95_ms: 0 ~ 17.5 ms
- recall_at_10: 1
- index_build_ms: 0 ~ 80.5 ms
- backends: webgpu, wasm
- fallback states: false, true

## 6. 결과 표
| Run | Scenario | Backend | Cache | Mean | P95 | Notes |
|---|---|---:|---:|---:|---:|---|
| 1 | Cold Index / WebGPU | webgpu | cold | 139.21 | 17.5 | queries/s=227.27, recall@10=1, metric=docs/s |
| 2 | Warm Query / WebGPU | webgpu | warm | 6000 | 0 | queries/s=206.9, recall@10=1, metric=docs/s |
| 3 | Cold Index / Fallback | wasm | cold | 74.53 | 13 | queries/s=133.33, recall@10=1, metric=docs/s |
| 4 | Warm Query / Fallback | wasm | warm | 6000 | 0 | queries/s=133.93, recall@10=1, metric=docs/s |

## 7. 관찰
- warm run docs_per_sec는 6000이고 cold 대비 delta는 5860.79였다.
- recall@10은 cold=1, warm=1로 유지됐다.
- playwright-chromium로 수집된 automation baseline이며 headless=true, browser=Chromium 147.0.7727.15.
- 실제 runtime/model/renderer 교체 전 deterministic harness 결과이므로, 절대 성능보다 보고 경로와 재현성 확인에 우선 의미가 있다.

## 8. WebGPU vs Fallback
- cold cache: docs/s webgpu=139.21, fallback=74.53, delta=+64.68; queries/s delta=+93.94; recall delta=0
- warm cache: docs/s webgpu=6000, fallback=6000, delta=0; queries/s delta=+72.97; recall delta=0

## 9. 결론
- cold/warm embeddings baseline 결과와 문서화 경로가 처음으로 연결됐다.
- 동일 fixture와 cache state에서 WebGPU vs fallback 비교 경로가 raw JSON과 RESULTS.md 양쪽에 생겼다.
- 다음 단계는 synthetic embedder를 실제 browser runtime으로 치환하고 동일한 결과 파일명을 유지하는 것이다.

## 10. 첨부
- 스크린샷: ./reports/screenshots/01-cold-index-webgpu.png, ./reports/screenshots/02-warm-query-webgpu.png, ./reports/screenshots/03-cold-index-fallback.png, ./reports/screenshots/04-warm-query-fallback.png
- 로그 파일: ./reports/logs/01-cold-index-webgpu.log, ./reports/logs/02-warm-query-webgpu.log, ./reports/logs/03-cold-index-fallback.log, ./reports/logs/04-warm-query-fallback.log
- raw json: ./reports/raw/01-cold-index-webgpu.json, ./reports/raw/02-warm-query-webgpu.json, ./reports/raw/03-cold-index-fallback.json, ./reports/raw/04-warm-query-fallback.json
- 배포 URL: https://ai-webgpu-lab.github.io/exp-embeddings-browser-throughput/
- 관련 이슈/PR: -
