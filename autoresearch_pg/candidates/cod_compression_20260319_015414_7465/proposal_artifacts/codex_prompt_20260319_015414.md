You are preparing one new candidate for the OpenAI Parameter Golf challenge.

Make one coherent proposal in this candidate-local workspace.

Mission:
- minimize `final_int8_zlib_roundtrip_exact val_bpb`
- keep `bytes_total <= 16_000_000`
- keep experiments reproducible and promotable to the official `records/` format

Read first:
- `train_gpt_mlx.py`
- `notes.md`
- `../../program.md`
- `proposal_artifacts/codex_context_latest.json` if you need deeper history or raw context

Current family:
- family: compression
- proposal_mode: deep
- tier: smoke_mlx_local
- parent_candidate_id: None
- inherited_env_overrides: {}
- family_progress: completed=2 valid=2 invalid=0 wins=2 best=3.04557018 last=3.04557018

Family focus:
- export sensitivity
- post-quant gap reduction
- artifact-byte awareness

Curated starter ideas:
- `staged_qat_large_matrices` [near_term | risk=medium]: Staged fake-quant final phase. During the last 10-15% of training, periodically fake-quantize only the large 2D block matrices while keeping small control tensors in higher precision.
- `aqlm_style_codebook_mlp` [moonshot | risk=high]: AQLM-style codebook MLP compression. Treat the largest MLP matrices as additive-codebook or prototype-compressed weights so artifact bytes fall faster than with naive int8 per-row quantization.

Scoreboard:
- same_tier_best: rep_cod_training_recipe_20260318_235033_8561_20260319_015231_7617 tier=smoke_mlx_local score=2.839851 bytes=6.48MB valid=true
- family_best: rec_validate_compression_recipe_warmdown tier=smoke_mlx_local score=3.045570 gap=0.018170 bytes=5.97MB op=recombine valid=true
- same_tier_family_best: rec_validate_compression_recipe_warmdown tier=smoke_mlx_local score=3.045570 gap=0.018170 bytes=5.97MB op=recombine valid=true
- low_quant_gap_anchor: rep_cod_training_recipe_20260318_235033_8561_20260319_015231_7617 tier=smoke_mlx_local score=2.839851 gap=0.004451 bytes=6.48MB op=replicate valid=true
- byte_headroom_anchor: port_architecture_20260319_014604_8294 tier=smoke_mlx_local score=3.246649 gap=0.027949 bytes=3.67MB op=arch_compact_8x448 tpl=arch_compact_8x448 valid=true
- cross_tier_global_best: first_candidate tier=smoke_local score=1.224366 bytes=15.86MB valid=true (informational only; do not rank it above same-tier evidence)

Recent local evidence:
- rec_validate_compression_recipe_warmdown tier=smoke_mlx_local score=3.045570 gap=0.018170 bytes=5.97MB op=recombine valid=true
- port_compression_20260318_210204_9293 tier=smoke_mlx_local score=3.237472 gap=0.029272 bytes=5.16MB op=logit_softcap_sweep valid=true

Recent failure evidence:
- none yet

Recent operator pattern:
- logit_softcap_sweep x1, recombine x1

Observations:
- compression family best still has a material quant gap (0.018170 BPB). Export-aware changes remain valuable.
- Use same-tier results as the main local compass. Current tier best is rep_cod_training_recipe_20260318_235033_8561_20260319_015231_7617 at proxy_score=2.83985068.
- Best low-quant-gap candidate currently comes from training_recipe. Borrow export-stable habits if relevant.

Parent context:
- parent_best_run: none
- parent_hypothesis: none
- parent_knobs: none
- parent_changed_symbols: none
- parent_last_proposal: none


Mode rationale:
- compression defaults to deep mode
- no parent candidate selected

Ambiguous contenders:
- rec_validate_compression_recipe_warmdown tier=smoke_mlx_local score=3.045570 gap=0.018170 bytes=5.97MB op=recombine valid=true
- port_compression_20260318_210204_9293 tier=smoke_mlx_local score=3.237472 gap=0.029272 bytes=5.16MB op=logit_softcap_sweep valid=true

Champion set:
- first_candidate tier=smoke_local score=1.224366 bytes=15.86MB valid=true
- port_architecture_20260319_014604_8294 tier=smoke_mlx_local score=3.246649 gap=0.027949 bytes=3.67MB op=arch_compact_8x448 tpl=arch_compact_8x448 valid=true
- rec_validate_compression_recipe_warmdown tier=smoke_mlx_local score=3.045570 gap=0.018170 bytes=5.97MB op=recombine valid=true
- rep_cod_training_recipe_20260318_235033_8561_20260319_015231_7617 tier=smoke_mlx_local score=2.839851 gap=0.004451 bytes=6.48MB op=replicate valid=true

Broader family history:
- rec_validate_compression_recipe_warmdown tier=smoke_mlx_local score=3.045570 gap=0.018170 bytes=5.97MB op=recombine valid=true
- port_compression_20260318_210204_9293 tier=smoke_mlx_local score=3.237472 gap=0.029272 bytes=5.16MB op=logit_softcap_sweep valid=true

Parent delta excerpt:
```diff
no parent candidate
```


Workspace rules:
- Edit only the candidate-local files listed below.
- Keep the change small enough that it is attributable.
- Do not launch training or any long-running experiments.
- Preserve runnability of `train_gpt_mlx.py`.
- If you change the hypothesis materially, update `notes.md` sections 1-4.
- Prefer same-tier signals over cross-tier scoreboard noise when they disagree.
- Keep the improvement compatible with the inherited env overrides unless you have a clear reason to change behavior in code.
- Prefer one targeted change that composes with the parent over a broad rewrite.

Allowed files:
- train_gpt.py
- train_gpt_mlx.py
- notes.md

Primary task:
- Inspect `train_gpt_mlx.py`.
- Implement one promising improvement for the compression family.
- Prefer a small but real code change over commentary.
- Do not touch files outside this candidate workspace.
- Use the context above to avoid duplicating already-tried weak ideas.

Final response:
- Briefly state the hypothesis.
- Mention the exact files you changed.
- Mention expected upside and main risk.
