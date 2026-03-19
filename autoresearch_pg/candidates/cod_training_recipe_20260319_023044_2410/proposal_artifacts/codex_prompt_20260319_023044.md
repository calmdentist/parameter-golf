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
- parent_candidate_id: rep_cod_training_recipe_20260318_235033_8561_20260319_021109_9967
- inherited_env_overrides: {"MATRIX_LR": 0.05, "SCALAR_LR": 0.04, "WARMDOWN_ITERS": 256, "WARMUP_STEPS": 64}
- family_progress: completed=26 valid=25 invalid=1 wins=10 best=2.83917722 last=3.22210155

Family focus:
- learning-rate schedule
- optimizer-group balance
- sample-efficiency under a short wallclock

Curated starter ideas:
- `mtp_aux_heads_train_only` [near_term | risk=medium]: Train-only multi-token heads. Add 2 auxiliary future-token heads on top of the shared trunk during training, then drop them before export so bytes stay nearly unchanged.
- `staged_qat_large_matrices` [near_term | risk=medium]: Staged fake-quant final phase. During the last 10-15% of training, periodically fake-quantize only the large 2D block matrices while keeping small control tensors in higher precision.

Scoreboard:
- same_tier_best: rep_cod_training_recipe_20260318_235033_8561_20260319_021109_9967 tier=smoke_mlx_local score=2.839177 bytes=6.48MB valid=true
- family_best: rep_cod_training_recipe_20260318_235033_8561_20260319_021109_9967 tier=smoke_mlx_local score=2.839177 gap=0.003777 bytes=6.48MB op=replicate valid=true
- same_tier_family_best: rep_cod_training_recipe_20260318_235033_8561_20260319_021109_9967 tier=smoke_mlx_local score=2.839177 gap=0.003777 bytes=6.48MB op=replicate valid=true
- low_quant_gap_anchor: port_training_recipe_20260319_022239_9771 tier=smoke_mlx_local score=2.917908 gap=0.002908 bytes=6.51MB op=embed_lr_sweep valid=true
- byte_headroom_anchor: port_architecture_20260319_020016_7076 tier=smoke_mlx_local score=3.246039 gap=0.024239 bytes=2.83MB op=mlp_sweep valid=true
- cross_tier_global_best: first_candidate tier=smoke_local score=1.224366 bytes=15.86MB valid=true (informational only; do not rank it above same-tier evidence)

Recent local evidence:
- port_training_recipe_20260319_022334_6969 tier=smoke_mlx_local score=3.222102 gap=0.069902 bytes=5.25MB op=warmdown_sweep valid=true
- port_training_recipe_20260319_022239_9771 tier=smoke_mlx_local score=2.917908 gap=0.002908 bytes=6.51MB op=embed_lr_sweep valid=true
- port_training_recipe_20260319_022130_6512 tier=smoke_mlx_local score=2.943740 gap=0.031740 bytes=5.59MB op=recipe_fast_decay_bundle valid=true
- port_training_recipe_20260319_021236_6366 tier=smoke_mlx_local score=3.190262 gap=0.088262 bytes=5.32MB op=warmdown_sweep valid=true

Recent failure evidence:
- cod_training_recipe_20260319_014628_8728 tier=smoke_mlx_local score=1000000.000000 op=codex_proposal valid=false

Recent operator pattern:
- replicate x2, warmdown_sweep x2, embed_lr_sweep x1

Observations:
- Current base already inherits env overrides {"MATRIX_LR": 0.05, "SCALAR_LR": 0.04, "WARMDOWN_ITERS": 256, "WARMUP_STEPS": 64}. Build incrementally unless you mean to replace that behavior in code.
- Use same-tier results as the main local compass. Current tier best is rep_cod_training_recipe_20260318_235033_8561_20260319_021109_9967 at proxy_score=2.83917722.
- Recent failed family attempts include operators ["codex_proposal"]. Avoid repeating the same weak pattern without a sharper hypothesis.

Parent context:
- parent_best_run: rep_cod_training_recipe_20260318_235033_8561_20260319_021109_9967 tier=smoke_mlx_local score=2.839177 gap=0.003777 bytes=6.48MB op=replicate valid=true
- parent_hypothesis: Scheduler cycle 17: action=replicate source=cod_training_recipe_20260318_235033_8561
- parent_knobs: 
- parent_changed_symbols: none
- parent_last_proposal: none


Mode rationale:
- top same-tier family contenders are within 0.0030 BPB

Ambiguous contenders:
- rep_cod_training_recipe_20260318_235033_8561_20260319_021109_9967 tier=smoke_mlx_local score=2.839177 gap=0.003777 bytes=6.48MB op=replicate valid=true
- rep_cod_training_recipe_20260318_235033_8561_20260319_015231_7617 tier=smoke_mlx_local score=2.839851 gap=0.004451 bytes=6.48MB op=replicate valid=true
- rep_cod_training_recipe_20260318_235033_8561_20260319_021014_3929 tier=smoke_mlx_local score=2.839957 gap=0.004657 bytes=6.48MB op=replicate valid=true

Champion set:
- first_candidate tier=smoke_local score=1.224366 bytes=15.86MB valid=true
- port_architecture_20260319_023023_5357 tier=smoke_mlx_local score=3.240207 gap=0.024907 bytes=2.95MB op=depth_sweep valid=true
- rec_validate_compression_recipe_warmdown tier=smoke_mlx_local score=3.045570 gap=0.018170 bytes=5.97MB op=recombine valid=true
- rep_cod_training_recipe_20260318_235033_8561_20260319_021109_9967 tier=smoke_mlx_local score=2.839177 gap=0.003777 bytes=6.48MB op=replicate valid=true

Broader family history:
- port_training_recipe_20260319_022334_6969 tier=smoke_mlx_local score=3.222102 gap=0.069902 bytes=5.25MB op=warmdown_sweep valid=true
- port_training_recipe_20260319_022239_9771 tier=smoke_mlx_local score=2.917908 gap=0.002908 bytes=6.51MB op=embed_lr_sweep valid=true
- port_training_recipe_20260319_022130_6512 tier=smoke_mlx_local score=2.943740 gap=0.031740 bytes=5.59MB op=recipe_fast_decay_bundle valid=true
- port_training_recipe_20260319_021236_6366 tier=smoke_mlx_local score=3.190262 gap=0.088262 bytes=5.32MB op=warmdown_sweep valid=true
- rep_cod_training_recipe_20260318_235033_8561_20260319_021109_9967 tier=smoke_mlx_local score=2.839177 gap=0.003777 bytes=6.48MB op=replicate valid=true
- rep_cod_training_recipe_20260318_235033_8561_20260319_021014_3929 tier=smoke_mlx_local score=2.839957 gap=0.004657 bytes=6.48MB op=replicate valid=true

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
