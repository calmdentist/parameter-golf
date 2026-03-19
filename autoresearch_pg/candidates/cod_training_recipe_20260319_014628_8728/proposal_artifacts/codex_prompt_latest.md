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
- family: training_recipe
- proposal_mode: deep
- tier: smoke_mlx_local
- parent_candidate_id: rep_cod_training_recipe_20260318_235033_8561_20260319_014400_9249
- inherited_env_overrides: {"MATRIX_LR": 0.05, "SCALAR_LR": 0.04, "WARMDOWN_ITERS": 256, "WARMUP_STEPS": 64}
- family_progress: completed=12 valid=12 invalid=0 wins=8 best=2.83995742 last=3.1897757

Family focus:
- learning-rate schedule
- optimizer-group balance
- sample-efficiency under a short wallclock

Curated starter ideas:
- `mtp_aux_heads_train_only` [near_term | risk=medium]: Train-only multi-token heads. Add 2 auxiliary future-token heads on top of the shared trunk during training, then drop them before export so bytes stay nearly unchanged.
- `staged_qat_large_matrices` [near_term | risk=medium]: Staged fake-quant final phase. During the last 10-15% of training, periodically fake-quantize only the large 2D block matrices while keeping small control tensors in higher precision.

Scoreboard:
- same_tier_best: rep_cod_training_recipe_20260318_235033_8561_20260319_014400_9249 tier=smoke_mlx_local score=2.839957 bytes=6.48MB valid=true
- family_best: rep_cod_training_recipe_20260318_235033_8561_20260319_014400_9249 tier=smoke_mlx_local score=2.839957 gap=0.004657 bytes=6.48MB op=replicate valid=true
- same_tier_family_best: rep_cod_training_recipe_20260318_235033_8561_20260319_014400_9249 tier=smoke_mlx_local score=2.839957 gap=0.004657 bytes=6.48MB op=replicate valid=true
- low_quant_gap_anchor: rep_cod_training_recipe_20260318_235033_8561_20260319_014400_9249 tier=smoke_mlx_local score=2.839957 gap=0.004657 bytes=6.48MB op=replicate valid=true
- byte_headroom_anchor: port_architecture_20260319_014604_8294 tier=smoke_mlx_local score=3.246649 gap=0.027949 bytes=3.67MB op=arch_compact_8x448 tpl=arch_compact_8x448 valid=true
- cross_tier_global_best: first_candidate tier=smoke_local score=1.224366 bytes=15.86MB valid=true (informational only; do not rank it above same-tier evidence)

Recent local evidence:
- port_training_recipe_20260319_014455_4479 tier=smoke_mlx_local score=3.189776 gap=0.058776 bytes=5.39MB op=recipe_long_warmup_low_scalar tpl=recipe_long_warmup_low_scalar valid=true
- rep_cod_training_recipe_20260318_235033_8561_20260319_014400_9249 tier=smoke_mlx_local score=2.839957 gap=0.004657 bytes=6.48MB op=replicate valid=true
- cod_training_recipe_20260319_013741_9834 tier=smoke_mlx_local score=3.066825 gap=0.017525 bytes=5.46MB op=codex_proposal valid=true
- cod_training_recipe_20260319_012138_9154 tier=smoke_mlx_local score=3.040639 gap=0.006239 bytes=5.45MB op=codex_proposal valid=true

Recent failure evidence:
- none yet

Recent operator pattern:
- codex_proposal x4, recipe_long_warmup_low_scalar x1, replicate x1

Observations:
- Current base already inherits env overrides {"MATRIX_LR": 0.05, "SCALAR_LR": 0.04, "WARMDOWN_ITERS": 256, "WARMUP_STEPS": 64}. Build incrementally unless you mean to replace that behavior in code.
- training_recipe family best still has a material quant gap (0.004657 BPB). Export-aware changes remain valuable.
- Use same-tier results as the main local compass. Current tier best is rep_cod_training_recipe_20260318_235033_8561_20260319_014400_9249 at proxy_score=2.83995742.

Parent context:
- parent_best_run: rep_cod_training_recipe_20260318_235033_8561_20260319_014400_9249 tier=smoke_mlx_local score=2.839957 gap=0.004657 bytes=6.48MB op=replicate valid=true
- parent_hypothesis: Scheduler cycle 2: action=replicate source=cod_training_recipe_20260318_235033_8561
- parent_knobs: 
- parent_changed_symbols: none
- parent_last_proposal: none


Mode rationale:
- top same-tier family contenders are within 0.0030 BPB

Ambiguous contenders:
- rep_cod_training_recipe_20260318_235033_8561_20260319_014400_9249 tier=smoke_mlx_local score=2.839957 gap=0.004657 bytes=6.48MB op=replicate valid=true
- cod_training_recipe_20260318_235033_8561 tier=smoke_mlx_local score=2.840899 gap=0.005699 bytes=6.48MB op=codex_proposal valid=true
- cod_training_recipe_20260318_222108_4091 tier=smoke_mlx_local score=2.858634 gap=0.004734 bytes=6.38MB op=codex_proposal valid=true

Champion set:
- first_candidate tier=smoke_local score=1.224366 bytes=15.86MB valid=true
- port_architecture_20260319_014604_8294 tier=smoke_mlx_local score=3.246649 gap=0.027949 bytes=3.67MB op=arch_compact_8x448 tpl=arch_compact_8x448 valid=true
- rec_validate_compression_recipe_warmdown tier=smoke_mlx_local score=3.045570 gap=0.018170 bytes=5.97MB op=recombine valid=true
- rep_cod_training_recipe_20260318_235033_8561_20260319_014400_9249 tier=smoke_mlx_local score=2.839957 gap=0.004657 bytes=6.48MB op=replicate valid=true

Broader family history:
- port_training_recipe_20260319_014455_4479 tier=smoke_mlx_local score=3.189776 gap=0.058776 bytes=5.39MB op=recipe_long_warmup_low_scalar tpl=recipe_long_warmup_low_scalar valid=true
- rep_cod_training_recipe_20260318_235033_8561_20260319_014400_9249 tier=smoke_mlx_local score=2.839957 gap=0.004657 bytes=6.48MB op=replicate valid=true
- cod_training_recipe_20260319_013741_9834 tier=smoke_mlx_local score=3.066825 gap=0.017525 bytes=5.46MB op=codex_proposal valid=true
- cod_training_recipe_20260319_012138_9154 tier=smoke_mlx_local score=3.040639 gap=0.006239 bytes=5.45MB op=codex_proposal valid=true
- cod_training_recipe_20260319_010431_7532 tier=smoke_mlx_local score=3.046383 gap=0.005883 bytes=5.45MB op=codex_proposal valid=true
- cod_training_recipe_20260318_235033_8561 tier=smoke_mlx_local score=2.840899 gap=0.005699 bytes=6.48MB op=codex_proposal valid=true

Parent delta excerpt:
```diff
no diff from source
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
- Implement one promising improvement for the training_recipe family.
- Prefer a small but real code change over commentary.
- Do not touch files outside this candidate workspace.
- Use the context above to avoid duplicating already-tried weak ideas.

Final response:
- Briefly state the hypothesis.
- Mention the exact files you changed.
- Mention expected upside and main risk.
