# cod_training_recipe_20260319_014628_8728

1. Hypothesis

Keep the parent matrix-LR-floor recipe intact, but make training more sample-efficient by adding a train-only multi-token objective on the shared tied output head. Reusing the same final hidden states to predict `t+2` and `t+3` during optimization should improve the quality of the exported next-token model under the same short wallclock, while leaving eval and serialized weights on the original next-token path.

2. Expected upside

Lower `final_int8_zlib_roundtrip_exact val_bpb` by getting more learning signal out of each observed token sequence without paying any artifact-byte cost. This is aimed at the family’s short-wallclock regime where export-free training objectives can be more attractive than another pure schedule tweak.

3. Expected risk

If the auxiliary future-token targets are too noisy for this small model, they can dilute the next-token objective, slow convergence, or add enough extra head compute to offset the sample-efficiency gain.

4. Exact knobs changed

Added two default-on knobs in `train_gpt_mlx.py`:
- `MTP_LOSS_WEIGHT_2=0.15`
- `MTP_LOSS_WEIGHT_3=0.05`

Wired `train_gpt_mlx.py` so:
- validation, export, and final roundtrip evaluation still use the original next-token loss
- training uses a weighted average of next-token, `t+2`, and `t+3` cross-entropy terms
- the common no-chunk path reuses one logits projection for all three targets to keep the wallclock tax modest
- artifact bytes and export format remain unchanged

5. Promotion bar

`smoke_mlx_local`: no runtime breakage, logs show the MTP weights, and submission bytes stay flat.
`proxy_1gpu_fast`: beat the parent on `final_int8_zlib_roundtrip_exact val_bpb`, or at minimum show better pre-quant efficiency without widening the post-quant gap.
