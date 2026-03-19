# cod_compression_20260319_021357_6229

1. Hypothesis

Add a genuinely late export-aware phase to the MLX compression recipe: during only the last ~12% of the run, periodically snap the largest 2D block matrices back onto their exact int8 roundtrip lattice using the same per-row quantizer used at export. This targets the family's still-material post-quant gap without changing bytes, while avoiding the repo's earlier warmdown-trigger pitfall where `lr_mul < 1` turns on far too early under wallclock-capped MLX runs.

2. Expected upside

Lower `final_int8_zlib_roundtrip_exact val_bpb` by reducing drift between the trained float matrices and the deployed int8-dequantized ones. Because the hook only touches large exported block matrices late in training, it should improve export stability with effectively no artifact-byte cost and less optimization disruption than an always-on or early warmdown projection.

3. Expected risk

If the remaining quant gap is dominated by the tied embedding or by smaller matrices that stay outside the projection set, the gain may be limited. The extra numpy quantize/dequantize work every few late steps could also trim a small number of final updates under the 600s cap, and any late snapping still risks slightly hurting raw validation if the trigger is too aggressive.

4. Exact knobs changed

Added four default-on knobs in `train_gpt_mlx.py`:
- `INT8_QAT_START_FRAC=0.88`
- `INT8_QAT_INTERVAL=4`
- `INT8_QAT_MIN_NUMEL=262_144`
- `INT8_QAT_INCLUDE_EMBEDDING=0`

Wired `train_gpt_mlx.py` so:
- `int8_qat_should_project()` triggers from absolute run progress, using wallclock fraction when `MAX_WALLCLOCK_SECONDS > 0` and iteration fraction otherwise
- `build_int8_qat_keys()` selects only large 2D matrices from the Muon-managed block set by default
- `project_int8_qat_tensors()` reuses the exact existing int8 quantizer/dequantizer after optimizer steps
- logs record the QAT config, first activation step, candidate tensor count, and total projected tensors for reproducibility

5. Promotion bar

`smoke_mlx_local`: no runtime breakage, logs show the new `int8_qat:*` settings, the run reaches a nonzero projection count, and submission bytes stay flat.
`proxy_1gpu_fast`: beat the current compression family best on `final_int8_zlib_roundtrip_exact val_bpb`, or at minimum shrink the post-quant gap relative to the same pre-quant regime without giving back most of the raw-validation gain.
