# cod_training_recipe_20260319_023810_8693

1. Hypothesis

Keep the parent LR-floor recipe intact, but cap the late scalar/control Adam tail relative to the Muon matrix tail once warmdown begins. The current best recipe already keeps matrices alive with a tiny floor, yet the float-kept control tensors can still end up moving much faster than the exported block matrices in the last few updates; limiting that imbalance should preserve late co-adaptation while reducing deployment mismatch.

2. Expected upside

Lower `final_int8_zlib_roundtrip_exact val_bpb` without changing artifact bytes by keeping the float-kept control group from outrunning the quantized block matrices in the deepest part of warmdown. This is a narrow optimizer-balance change that composes with the inherited `MATRIX_LR=0.05`, `SCALAR_LR=0.04`, `WARMDOWN_ITERS=256`, and `WARMUP_STEPS=64` overrides instead of replacing them.

3. Expected risk

If the cap is too strict, the scalar/control tensors may lose useful late calibration capacity and give back some of the parent's raw-validation gain. Because the parent already benefits from a scalar tail, over-constraining it could make the model slightly underfit the end of training.

4. Exact knobs changed

Added one default-on knob in `train_gpt_mlx.py`:
- `SCALAR_MAX_MATRIX_LR_RATIO=4.0`

Wired `train_gpt_mlx.py` so:
- `matrix_lr_mul` is still computed from the existing matrix floor
- `scalar_lr_mul` still starts from the existing scalar floor
- once `base_lr_mul < 1.0`, the scalar/control effective LR is capped to at most `4x` the matrix effective LR
- tied embedding behavior, export format, and artifact bytes remain unchanged

The optimizer config log now prints `scalar_max_matrix_lr_ratio` so runs stay reproducible and promotable.

5. Promotion bar

`smoke_mlx_local`: no runtime breakage, logs show `scalar_max_matrix_lr_ratio`, and bytes stay effectively unchanged.
`proxy_1gpu_fast`: beat the parent on `final_int8_zlib_roundtrip_exact val_bpb`, or at minimum reduce the roundtrip gap without giving back most of the parent's raw-validation level.
