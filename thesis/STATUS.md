# DCT-Page 학사논문 진행 상황

> 마지막 업데이트: 2026-05-25
> 빌드: 47쪽, ~471KB, warning 없음

## 본문 작성 상태 — 완료 ✅

| 챕터 | 파일 | 상태 |
|---|---|---|
| Abstract (en, 주) | main.tex | ✅ ~300단어 |
| Abstract (ko, 부) | main.tex | ✅ 번역됨 |
| Ch.1 Introduction | chapters/01_introduction.tex | ✅ 4 sections |
| Ch.2 Background and Related Work | chapters/02_background.tex | ✅ 3 sections (KV bottleneck, 4 baselines, DCT background) |
| Ch.3 Method: DCT-Page | chapters/03_method.tex | ✅ 6 sections, TikZ + DCT energy + pipeline figure |
| Ch.4 Experimental Setup | chapters/04_experiments.tex | ✅ 4 sections, Table 4.1 |
| Ch.5 Results | chapters/05_results.tex | ✅ 5 sections, 9 tables, 1 figure |
| Ch.6 Analysis | chapters/06_analysis.tex | ✅ 3 sections, mass-recall figure |
| Ch.7 Conclusion | chapters/07_conclusion.tex | ✅ 2 paragraphs + future work |

## 결정된 스코프 (확정사항)

- **언어**: 영문 (`\documentclass[en]{snu-ece-bsc-thesis}`)
- **양식**: snu-ece-bsc-thesis (전기·정보공학부, CSE 양식 아님)
- **저자**: 김윤곤 (2021-10145), 지도교수 김재준 (VLSI Lab)
- **모드**: `drop` only (`compressed`/`hybrid` mode 본문에서 제외)
- **모델**: Llama-3.1-8B-Instruct, Qwen3-8B
- **벤치마크**: RULER (13 task, 32K) + LongBench v1 (6 task) + LongBench v2. Reasoning 제외
- **Baseline**: page-based 3개 (Quest, SeerAttention-R, InfLLM) + cluster-based 1개 (Multipole, 별도 §5.5 Pareto 비교)
- **Budget 매칭**: `B*P = 64*32 = 2,048` selected tokens. P=32, S=1, R=4, k=59
- **속도 측정**: A6000 single GPU, `profile_decode_upstream_flash_infer.py`, CUDA graphs

## 헤드라인 수치 (전 챕터 일관)

- RULER vs Quest: **+5.16** (Llama), **+6.40** (Qwen3)
- LongBench v1 vs Full-KV: Δ ≤ **0.07**
- LongBench v2 Overall: DCT-Page > Full-KV (+0.20, +2.37); Quest collapse on Long (0.00, 0.93)
- Mass-recall: **84.6%** (DCT-Page) vs **62.9%** (Quest)
- Speedup @ 128K B1: **5.65×** attention, **1.70×** end-to-end
- Multipole 비교 (eager): DCT-Page **3.59×** 빠름

## 사용자 확정 필요 (남은 TODO)

1. **인준일/제출일** in [main.tex:25-26](main.tex#L25-L26) — 현재 placeholder `2026년 12월`
2. **Multipole citation** in [bib.bib](bib.bib) `singh2025multipole` — 저자/venue 정확히 채워야 함. 다른 citations도 venue/pages 한 번 검증 권장
3. 최종 proofread (영문 표현, 일관성)

## 빌드

```bash
cd /home/yoongonkim/snu-ece-bsc-thesis-DCT-Page/thesis
latexmk -xelatex main
```

- TinyTeX 설치 완료 (`~/.TinyTeX`, `~/.local/bin` symlinks)
- 필요 패키지 모두 설치됨
- 현재: **47쪽, 471KB, 0 warning**

## 주요 파일

- 본문: `chapters/01_introduction.tex` ~ `chapters/07_conclusion.tex`
- 메인: `main.tex`
- 참고문헌: `bib.bib` (13 entries)
- 그림: `figures/energy_curve.png` (§3.2), `figures/dct_speedup.pdf` (§5.3), `figures/paged_mass_recall.png` (§6.1)
- 클래스: `snu-ece-bsc-thesis.cls`
- 코드/포스터: `/home/yoongonkim/DCT-Page/`

## 참고 메모리 파일

영구 저장된 high-level 결정사항은 `~/.claude/projects/-home-yoongonkim/memory/` 에 있음:
- `project_dct_page_thesis.md` — 프로젝트 스코프
- `user_bsc_thesis.md` — 저자 정보
