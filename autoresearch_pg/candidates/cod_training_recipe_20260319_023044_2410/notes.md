# cod_training_recipe_20260319_023044_2410

1. Hypothesis

Keep the parent LR-floor recipe intact, but make the earlier near-miss `t+2` train-only auxiliary loss more target-aligned by using it only before warmdown. The light MTP variant was already close on smoke tier; turning it off once warmdown begins should keep the early sample-efficiency benefit while letting the late phase optimize the exact next-token/export objective.

2. Expected upside

Lower `final_int8_zlib_roundtrip_exact val_bpb` without changing artifact bytes. This specifically aims to improve short-wallclock sample efficiency early in training while avoiding the late objective mismatch that likely held back the always-on `t+2` variant.

3. Expected risk

Even a lighter, pre-warmdown-only auxiliary target still adds compute and optimization noise. If the extra signal is not useful enough, it can slow convergence, and the hard handoff at warmdown could create a small training discontinuity.

4. Exact knobs changed

Added two default-on knobs in `train_gpt_mlx.py`:
- `MTP_LOSS_WEIGHT_2=0.03`
- `MTP_DISABLE_ON_WARMDOWN=1`

Wired `train_gpt_mlx.py` so:
- training can use a weighted average of next-token and train-only `t+2` cross-entropy
- validation, export, and final roundtrip evaluation remain pure next-token
- the `t+2` auxiliary loss is active only while the base LR multiplier is still at `1.0`, then disables for warmdown
- both train graphs are compiled and logged explicitly so the behavior is reproducible
- the inherited parent overrides remain intact: `MATRIX_LR=0.05`, `SCALAR_LR=0.04`, `WARMDOWN_ITERS=256`, `WARMUP_STEPS=64`

5. Promotion bar

`smoke_mlx_local`: no runtime breakage, logs show the MTP settings plus the objective-phase switch, and bytes stay effectively unchanged.
`proxy_1gpu_fast`: beat the parent on `final_int8_zlib_roundtrip_exact val_bpb`, or at minimum beat the earlier always-on `t+2` attempt while keeping the post-quant gap from widening.
