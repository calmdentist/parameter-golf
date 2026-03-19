Hypothesis: a small nonzero Muon matrix LR floor during warmdown will keep the large quantized matrices from fully freezing before export, which should reduce the post-quant gap without changing artifact bytes.

I changed [train_gpt_mlx.py](/Users/calmdentist/Desktop/parameter-golf/autoresearch_pg/candidates/cod_compression_20260319_015414_7465/train_gpt_mlx.py#L86) to add `MATRIX_LR_FLOOR_RATIO=0.01`, compute a floored `matrix_lr_mul`, apply it only to the Muon matrix group, and log it for reproducibility. I updated [notes.md](/Users/calmdentist/Desktop/parameter-golf/autoresearch_pg/candidates/cod_compression_20260319_015414_7465/notes.md#L3) to reflect the new hypothesis and promotion bar.

Expected upside is lower `final_int8_zlib_roundtrip_exact val_bpb` from a smaller raw-vs-roundtrip gap with effectively unchanged bytes. Main risk is that keeping Muon slightly active late in training adds warmdown noise and hurts either float validation or the final roundtrip score.

Verified with `python3 -m py_compile train_gpt_mlx.py`.