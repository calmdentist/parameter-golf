# cod_training_recipe_20260319_020036_3512

1. Hypothesis

Keep the parent training recipe intact, but add a much lighter train-only `t+2` auxiliary loss on the shared tied output head. A single low-weight future-token target should improve sample efficiency under the short wallclock without repeating the noisier `t+2`/`t+3` variant, and keeping that weight outside `model.state` preserves clean export/serialization behavior.

2. Expected upside

Lower `final_int8_zlib_roundtrip_exact val_bpb` by extracting a bit more learning signal from each observed sequence while leaving artifact bytes and final evaluation unchanged. This is aimed at the current family regime where same-tier contenders are clustered tightly and a small wallclock-efficiency gain can matter more than another broad schedule mutation.

3. Expected risk

Even a conservative `t+2` target can still dilute the main next-token objective, especially for this small model and short smoke tier. If the auxiliary loss is still too noisy, it can slow convergence or improve early training loss without translating into better final roundtrip score.

4. Exact knobs changed

Added one default-on knob in `train_gpt_mlx.py`:
- `MTP_LOSS_WEIGHT_2=0.05`

Refactored the GPT loss path so:
- validation, export, and final roundtrip evaluation remain pure next-token loss
- training uses a weighted average of next-token and a single `t+2` loss term
- the common no-chunk path reuses one logits projection for both targets to keep overhead modest
- the train-only auxiliary weight stays outside `model.state`, avoiding the earlier MLX serialization failure mode

This intentionally composes with the inherited parent env overrides rather than replacing them:
- `WARMUP_STEPS=64`
- `WARMDOWN_ITERS=256`
- `MATRIX_LR=0.05`
- `SCALAR_LR=0.04`

5. Promotion bar

`smoke_mlx_local`: no runtime breakage, logs show `train_objective:next_token+mtp_t2`, and serialization still succeeds.
`proxy_1gpu_fast`: beat the parent on `final_int8_zlib_roundtrip_exact val_bpb`, or at minimum show better short-run efficiency without widening the raw-to-roundtrip gap.
