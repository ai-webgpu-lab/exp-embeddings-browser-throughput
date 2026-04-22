# Results

## 1. 실험 요약
- 저장소: exp-embeddings-browser-throughput
- 커밋 해시: c4319b0
- 실험 일시: 2026-04-22T06:13:00.084Z -> 2026-04-22T06:13:00.487Z
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
- adapter: not-applicable
- backend: `mixed`
- fallback triggered: `false`
- worker mode: `main`
- cache state: `cold, warm`
- required features: []
- limits snapshot: {}

## 4. 워크로드 정의
- 시나리오 이름: Cold Index, Warm Query
- 입력 프로필: 6-docs-3-queries
- 데이터 크기: synthetic fixture; batchSize=3; docs=6; queries=3; cacheState=cold; automation=playwright-chromium, synthetic fixture; batchSize=3; docs=6; queries=3; cacheState=warm; automation=playwright-chromium
- dataset: docs-fixture-v1
- model_id 또는 renderer: synthetic-browser-embedder-v1
- 양자화/정밀도: -
- resolution: -
- context_tokens: -
- output_tokens: -

## 5. 측정 지표
### 공통
- time_to_interactive_ms: 96.4 ~ 499 ms
- init_ms: 0 ~ 8.9 ms
- success_rate: 1
- peak_memory_note: 16 GB reported by browser
- error_type: -

### Embeddings / ML
- docs_per_sec: 674.16 ~ 6000
- queries_per_sec: 3000
- p95_ms: 0 ~ 0.3 ms
- recall_at_10: 1
- index_build_ms: 0 ~ 8.9 ms

## 6. 결과 표
| Run | Scenario | Backend | Cache | Mean | P95 | Notes |
|---|---|---:|---:|---:|---:|---|
| 1 | Cold Index | mixed | cold | 674.16 | 0.3 | queries/s=3000, recall@10=1, metric=docs/s |
| 2 | Warm Query | mixed | warm | 6000 | 0 | queries/s=3000, recall@10=1, metric=docs/s |

## 7. 관찰
- warm run docs_per_sec는 6000이고 cold 대비 delta는 5325.84였다.
- recall@10은 cold=1, warm=1로 유지됐다.
- playwright-chromium로 수집된 automation baseline이며 headless=true, browser=Chromium 147.0.7727.15.
- 실제 runtime/model/renderer 교체 전 deterministic harness 결과이므로, 절대 성능보다 보고 경로와 재현성 확인에 우선 의미가 있다.

## 8. 결론
- cold/warm embeddings baseline 결과와 문서화 경로가 처음으로 연결됐다.
- 다음 단계는 synthetic embedder를 실제 browser runtime으로 치환하고 동일한 결과 파일명을 유지하는 것이다.
- WebGPU vs fallback 비교는 실제 runtime integration 후 같은 fixture로 추가해야 한다.

## 9. 첨부
- 스크린샷: ./reports/screenshots/01-cold-index.png, ./reports/screenshots/02-warm-query.png
- 로그 파일: ./reports/logs/01-cold-index.log, ./reports/logs/02-warm-query.log
- raw json: ./reports/raw/01-cold-index.json, ./reports/raw/02-warm-query.json
- 배포 URL: https://ai-webgpu-lab.github.io/exp-embeddings-browser-throughput/
- 관련 이슈/PR: -
