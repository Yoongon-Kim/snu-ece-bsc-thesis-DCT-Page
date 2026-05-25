# DCT-Page 학사논문 진행 상황

> 작업 중인 working note. 새 세션 시작할 때 먼저 읽으면 컨텍스트가 빨리 잡힘.
> 마지막 업데이트: 2026-05-25

## 결정된 스코프

- **언어**: 영문 (`\documentclass[en]{snu-ece-bsc-thesis}`)
- **양식**: snu-ece-bsc-thesis (전기·정보공학부, CSE 양식 아님)
- **저자**: 김윤곤 (2021-10145), 지도교수 김재준 (VLSI Lab)
- **모드**: `drop` only — `compressed`/`hybrid` mode 전부 본문에서 제외
- **모델**: Llama-3.1-8B-Instruct, Qwen3-8B
- **벤치마크**: RULER (13 task, 32K) + LongBench v1 (6 task: NarrativeQA, Qasper, MultiFieldQA-en, 2WikiMultihopQA, GovReport, TriviaQA) + LongBench v2. **Reasoning(AIME25/GPQA) 제외**
- **Baseline**:
  - Page-based (직접 비교): Quest, SeerAttention-R (Qwen3 only), InfLLM (Llama only)
  - Cluster-based (별도 §5.4 Pareto 비교): Multipole (양 모델)
  - Full-KV reference (양 모델)
- **Budget 매칭**: `B * P = 64 * 32 = 2,048` selected tokens. Page size `P = 32`, sink `S = 1`, recent `R = 4`, middle-page top-k `k = 59`
- **속도 측정**: A6000 single GPU, `DCT-Page/speed/profile_decode_upstream_flash_infer.py`, CUDA graphs 사용

## 챕터별 진행 상황

| 챕터 | 파일 | 상태 |
|---|---|---|
| Abstract (en) | main.tex (인라인) | TODO — 본문 다 쓴 뒤 마지막 |
| Abstract (ko) | main.tex (인라인) | TODO — 영문 abstract 번역 |
| Ch.1 Introduction | chapters/01_introduction.tex | 스켈레톤만 (TODO 주석) |
| Ch.2 Background and Related Work | chapters/02_background.tex | 스켈레톤만 |
| Ch.3 Method: DCT-Page | chapters/03_method.tex | **본문 완성** (6절, TikZ + DCT energy + pipeline figure) |
| Ch.4 Experimental Setup | chapters/04_experiments.tex | **본문 완성** (4절, Table 4.1 baseline config) |
| Ch.5 Results | chapters/05_results.tex | **표 구조만 완성, 수치 비어있음** (9개 표 + 1 figure placeholder) |
| Ch.6 Analysis | chapters/06_analysis.tex | 스켈레톤만 |
| Ch.7 Conclusion | chapters/07_conclusion.tex | 스켈레톤만 |

## 남은 TODO (우선순위 순)

### 채워 넣기
- [x] Table 5.1 RULER per-task — Llama: DCT-Page 86.26 (+5.16 vs Quest); Qwen3: 83.72 (+6.40 vs Quest)
- [x] Table 5.2 LongBench v1 per-task — Llama 50.27 / Qwen3 48.63 (best sparse, Δ ≤ 0.07 vs Full-KV)
- [x] Table 5.3 LongBench v2 breakdown — Overall에서 DCT-Page가 Full-KV 초과 (Llama +0.20, Qwen3 +2.37). Qwen3에서 SeerAttention-R(32.60)이 DCT-Page(32.01) 살짝 앞섬.
- [x] Figure 5.1 speedup curves — `figures/dct_speedup.pdf` (포스터 Fig.4 PDF 재사용)
- [x] Table 5.4 per-stage profile (ms/step) — Full-KV 6.400 vs DCT-Page total 1.389. top-k와 KV assembly가 한 커널에서 처리되어 fused 행으로 reporting.
- [x] Table 5.5 Multipole comparison — Multipole이 RULER에서 +3-4pt 앞서지만 LongBench는 동률, throughput은 DCT-Page가 5.14× 빠름 (38.52 vs 7.50 tok/s).
- [ ] Table 5.6 ablation: page size — **외부 세션 ablation script로 처리 중** (아래 참조)
- [ ] Table 5.7 ablation: budget — 외부 세션
- [ ] Table 5.8 ablation: compression ratio — 외부 세션
- [ ] Table 5.9 ablation: aggregators — 외부 세션

**Ablation 외부 세션 (2026-05-25 시작):**
별도 Claude 세션을 `/home/yoongonkim/DCT-Page`에서 시작해 ablation grid 15 RULER runs + 4 speed runs를 돌리고 markdown 표 4개로 출력하도록 함. 결과 받으면 5.6~5.9에 그대로 paste.

### 작성
- [ ] Ch.2 Background + Related Work — Ch.4.3에서 빼낸 baseline 메서드 설명을 여기로 가져옴
- [ ] Ch.6 Analysis — mass-recall (poster Fig.2), scoring-method comparison, oracle upper bound, limitations
- [ ] Ch.1 Introduction (Method/Results 후 작성)
- [ ] Ch.7 Conclusion (Method/Results 후 작성)
- [ ] Abstract (en + ko) — 가장 마지막

### Cleanup
- [ ] Multipole citation 정확한 저자/venue로 교체 (현재 placeholder)
- [ ] 인준일/제출일 (`main.tex`의 `\date{}` / `\approvaldate{}`) 실제 일정 확정 시 갱신
- [ ] hyperref `Object @page.1 already defined` warning — 외표지/인준지 중복 page 번호 충돌, 마이너
- [ ] 모든 reference의 venue/저자 최종 검증

## 빌드

```bash
cd /home/yoongonkim/snu-ece-bsc-thesis-DCT-Page/thesis
latexmk -xelatex main
```

- TinyTeX 설치되어 있음 (`~/.TinyTeX`, `~/.local/bin`에 심볼릭 링크)
- biber, xetexko, kotex, tabularray, tikz, biblatex-ieee 등 필요 패키지 모두 설치 완료
- 현재 빌드: 31쪽, ~277KB PDF

## 주요 파일

- 본문: `chapters/01_introduction.tex` ~ `chapters/07_conclusion.tex`
- 메인: `main.tex` (메타데이터, 패키지, include 순서)
- 참고문헌: `bib.bib`
- 그림: `figures/energy_curve.png` (3.2에서 사용)
- 클래스: `snu-ece-bsc-thesis.cls` (root에서 복사)
- 포스터 (원본): `/home/yoongonkim/DCT-Page/김재준교수님_김윤곤.pdf`
- 코드: `/home/yoongonkim/DCT-Page/`

## 참고 메모리 파일

영구 저장된 high-level 결정사항은 `~/.claude/projects/-home-yoongonkim/memory/` 에 있음:
- `project_dct_page_thesis.md` — 프로젝트 스코프
- `user_bsc_thesis.md` — 저자 정보
