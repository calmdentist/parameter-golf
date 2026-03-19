# cod_architecture_20260319_022421_4423

1. Hypothesis

Keep the parent `8x448`, `MLP_MULT=1` architecture intact, but normalize encoder skip activations before the decoder reuses them. The current stack already relies on learned per-channel `skip_weights`; feeding them RMS-normalized skip tensors should make skip fusion easier to calibrate across depth, improve decoder stability, and reduce the architecture family’s still-material post-quant gap without adding serialized parameters.

2. Expected upside

Lower `final_int8_zlib_roundtrip_exact val_bpb` by making the skip path more scale-stable at training time and after int8 roundtrip. This targets the current architecture family regime where the main weakness is export behavior rather than raw byte budget, and it composes directly with the inherited `NUM_LAYERS=8`, `MODEL_DIM=448`, `NUM_HEADS=7`, `NUM_KV_HEADS=1`, `MLP_MULT=1` overrides.

3. Expected risk

Raw skip magnitude may carry useful information, so normalizing it could over-regularize the decoder handoff and hurt raw validation or make the learned `skip_weights` too weak to recover lost signal. If that happens, the change could improve activation hygiene while still degrading the end metric.

4. Exact knobs changed

Added one default-on reproducibility knob in `train_gpt_mlx.py`:
- `NORMALIZE_SKIP_CONNECTIONS=1`

Wired `GPT.__call__` so decoder skip fusion becomes:
- pop encoder skip
- apply `rms_norm(skip)` when `NORMALIZE_SKIP_CONNECTIONS=1`
- scale by the existing learned per-channel `skip_weights`
- add into the decoder stream before the decoder block

The model logging line now prints `normalize_skip_connections` so runs are self-describing. Export format, artifact bytes, and all inherited env overrides otherwise stay unchanged.

5. Promotion bar

`smoke_mlx_local`: no runtime breakage, logs show `normalize_skip_connections:True`, and submission bytes stay effectively flat.
`proxy_1gpu_fast`: beat the parent on `final_int8_zlib_roundtrip_exact val_bpb`, or at minimum reduce the pre-vs-post-quant gap without giving back most of the parent’s raw-validation level.
