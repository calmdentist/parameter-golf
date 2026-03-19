# cod_compression_20260319_015414_7465

1. Hypothesis

Keep the current compression recipe intact, but add a very small nonzero LR floor to the Muon matrix group during warmdown so the large quantized 2D tensors do not fully freeze before export. The family still has a material post-quant gap, and the strongest same-tier low-gap signal elsewhere in the repo suggests late tiny matrix updates can keep the eventual int8 roundtrip better aligned with the float-trained controls and warmdown calibration.

2. Expected upside

Lower `final_int8_zlib_roundtrip_exact val_bpb` by trimming the post-quant gap without adding any artifact bytes. This is meant to be a small export-stability change that composes with the current `LOGIT_SOFTCAP=20` and `WARMDOWN_ITERS=256` style recipe instead of replacing it.

3. Expected risk

If the matrix tail stays active for too long, it can add late Muon noise and give back some of the warmdown stability that improved the family, hurting either pre-quant validation or the roundtrip score.

4. Exact knobs changed

Added one default-on knob in `train_gpt_mlx.py`:
- `MATRIX_LR_FLOOR_RATIO=0.01`

Wired `train_gpt_mlx.py` so:
- warmdown still computes the existing base `lr_mul`
- Muon uses `matrix_lr_mul = floor + (1 - floor) * lr_mul`
- tied-embedding Adam and scalar/control Adam keep using the original unfloored `lr_mul`
- logs now record `matrix_lr_floor_ratio` for reproducibility

5. Promotion bar

`smoke_mlx_local`: no runtime breakage, logs show `matrix_lr_floor_ratio`, and bytes stay effectively unchanged.
`proxy_1gpu_fast`: beat the current compression family best on `final_int8_zlib_roundtrip_exact val_bpb`, or at minimum reduce the raw-vs-roundtrip gap without sacrificing most of the warmdown gain.
