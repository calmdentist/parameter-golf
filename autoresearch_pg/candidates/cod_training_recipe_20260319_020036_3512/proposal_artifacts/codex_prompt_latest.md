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
- parent_candidate_id: rep_cod_training_recipe_20260318_235033_8561_20260319_015231_7617
- inherited_env_overrides: {"MATRIX_LR": 0.05, "SCALAR_LR": 0.04, "WARMDOWN_ITERS": 256, "WARMUP_STEPS": 64}
- family_progress: completed=16 valid=15 invalid=1 wins=9 best=2.83985068 last=2.84038718

Family focus:
- learning-rate schedule
- optimizer-group balance
- sample-efficiency under a short wallclock

Curated starter ideas:
- `mtp_aux_heads_train_only` [near_term | risk=medium]: Train-only multi-token heads. Add 2 auxiliary future-token heads on top of the shared trunk during training, then drop them before export so bytes stay nearly unchanged.
- `staged_qat_large_matrices` [near_term | risk=medium]: Staged fake-quant final phase. During the last 10-15% of training, periodically fake-quantize only the large 2D block matrices while keeping small control tensors in higher precision.

Scoreboard:
- same_tier_best: rep_cod_training_recipe_20260318_235033_8561_20260319_015231_7617 tier=smoke_mlx_local score=2.839851 bytes=6.48MB valid=true
- family_best: rep_cod_training_recipe_20260318_235033_8561_20260319_015231_7617 tier=smoke_mlx_local score=2.839851 gap=0.004451 bytes=6.48MB op=replicate valid=true
- same_tier_family_best: rep_cod_training_recipe_20260318_235033_8561_20260319_015231_7617 tier=smoke_mlx_local score=2.839851 gap=0.004451 bytes=6.48MB op=replicate valid=true
- low_quant_gap_anchor: rep_cod_training_recipe_20260318_235033_8561_20260319_015231_7617 tier=smoke_mlx_local score=2.839851 gap=0.004451 bytes=6.48MB op=replicate valid=true
- byte_headroom_anchor: port_architecture_20260319_020016_7076 tier=smoke_mlx_local score=3.246039 gap=0.024239 bytes=2.83MB op=mlp_sweep valid=true
- cross_tier_global_best: first_candidate tier=smoke_local score=1.224366 bytes=15.86MB valid=true (informational only; do not rank it above same-tier evidence)

Recent local evidence:
- rep_cod_training_recipe_20260318_235033_8561_20260319_015921_2258 tier=smoke_mlx_local score=2.840387 gap=0.005287 bytes=6.48MB op=replicate valid=true
- port_training_recipe_20260319_015326_6382 tier=smoke_mlx_local score=3.022132 gap=0.043332 bytes=5.59MB op=warmdown_sweep valid=true
- rep_cod_training_recipe_20260318_235033_8561_20260319_015231_7617 tier=smoke_mlx_local score=2.839851 gap=0.004451 bytes=6.48MB op=replicate valid=true
- port_training_recipe_20260319_014455_4479 tier=smoke_mlx_local score=3.189776 gap=0.058776 bytes=5.39MB op=recipe_long_warmup_low_scalar tpl=recipe_long_warmup_low_scalar valid=true

Recent failure evidence:
- cod_training_recipe_20260319_014628_8728 tier=smoke_mlx_local score=1000000.000000 op=codex_proposal valid=false

Recent operator pattern:
- replicate x3, codex_proposal x1, recipe_long_warmup_low_scalar x1

Observations:
- Current base already inherits env overrides {"MATRIX_LR": 0.05, "SCALAR_LR": 0.04, "WARMDOWN_ITERS": 256, "WARMUP_STEPS": 64}. Build incrementally unless you mean to replace that behavior in code.
- training_recipe family best still has a material quant gap (0.004451 BPB). Export-aware changes remain valuable.
- Use same-tier results as the main local compass. Current tier best is rep_cod_training_recipe_20260318_235033_8561_20260319_015231_7617 at proxy_score=2.83985068.
- Recent failed family attempts include operators ["codex_proposal"]. Avoid repeating the same weak pattern without a sharper hypothesis.

Parent context:
- parent_best_run: rep_cod_training_recipe_20260318_235033_8561_20260319_015231_7617 tier=smoke_mlx_local score=2.839851 gap=0.004451 bytes=6.48MB op=replicate valid=true
- parent_hypothesis: Scheduler cycle 6: action=replicate source=cod_training_recipe_20260318_235033_8561
- parent_knobs: 
- parent_changed_symbols: none
- parent_last_proposal: none


Mode rationale:
- top same-tier family contenders are within 0.0030 BPB

Ambiguous contenders:
- rep_cod_training_recipe_20260318_235033_8561_20260319_015231_7617 tier=smoke_mlx_local score=2.839851 gap=0.004451 bytes=6.48MB op=replicate valid=true
- rep_cod_training_recipe_20260318_235033_8561_20260319_014400_9249 tier=smoke_mlx_local score=2.839957 gap=0.004657 bytes=6.48MB op=replicate valid=true
- rep_cod_training_recipe_20260318_235033_8561_20260319_015921_2258 tier=smoke_mlx_local score=2.840387 gap=0.005287 bytes=6.48MB op=replicate valid=true

Champion set:
- first_candidate tier=smoke_local score=1.224366 bytes=15.86MB valid=true
- port_architecture_20260319_020016_7076 tier=smoke_mlx_local score=3.246039 gap=0.024239 bytes=2.83MB op=mlp_sweep valid=true
- rec_validate_compression_recipe_warmdown tier=smoke_mlx_local score=3.045570 gap=0.018170 bytes=5.97MB op=recombine valid=true
- rep_cod_training_recipe_20260318_235033_8561_20260319_015231_7617 tier=smoke_mlx_local score=2.839851 gap=0.004451 bytes=6.48MB op=replicate valid=true

Broader family history:
- rep_cod_training_recipe_20260318_235033_8561_20260319_015921_2258 tier=smoke_mlx_local score=2.840387 gap=0.005287 bytes=6.48MB op=replicate valid=true
- port_training_recipe_20260319_015326_6382 tier=smoke_mlx_local score=3.022132 gap=0.043332 bytes=5.59MB op=warmdown_sweep valid=true
- rep_cod_training_recipe_20260318_235033_8561_20260319_015231_7617 tier=smoke_mlx_local score=2.839851 gap=0.004451 bytes=6.48MB op=replicate valid=true
- cod_training_recipe_20260319_014628_8728 tier=smoke_mlx_local score=1000000.000000 op=codex_proposal valid=false
- port_training_recipe_20260319_014455_4479 tier=smoke_mlx_local score=3.189776 gap=0.058776 bytes=5.39MB op=recipe_long_warmup_low_scalar tpl=recipe_long_warmup_low_scalar valid=true
- rep_cod_training_recipe_20260318_235033_8561_20260319_014400_9249 tier=smoke_mlx_local score=2.839957 gap=0.004657 bytes=6.48MB op=replicate valid=true

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
