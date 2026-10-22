# multiview-masked-reconstruction

A Vision Transformer built from scratch that reconstructs a heavily masked image of a room using a second, unmasked view of the same room. The model sees the full **reference** view and only 5 to 25% of the **target** view, and has to fill in the rest of the target.

<img width="1030" height="1650" alt="Unknown-5" src="https://github.com/user-attachments/assets/1e2d9885-3031-4885-8910-58cd693a2c29" />

**Model.**
- Encoder: ViT, width 192, 6 blocks, 3 heads, 8 x 8 patches (16 x 16 grid). The same encoder processes each view separately.
- Decoder: target tokens (visible and masked) attend to the reference tokens through cross attention.
- Target masking: 75 to 95% during training. The reference is never masked.
- Loss: mean squared error on hidden patches, with each target patch normalized by its own mean and std.
- Stats head: a small MLP predicts each hidden patch's brightness (mean) and contrast (log std), so reconstructions don't need to borrow them from the ground truth.

## Results

### Ablation: brightness and contrast head

| | Baseline (no stats) | Linear head | MLP head, w=0.5 | **MLP head, w=0.2** |
|---|---|---|---|---|
| Best val loss @ 90% masked | 0.3175 | 0.3332 | 0.3280 | **0.3200** |
| Test loss @ 90% masked | 0.3342 | 0.3538 | 0.3475 | **0.3376** |
| Test loss, ARKitScenes (unseen rooms) | 0.3434 | 0.3585 | 0.3548 | **0.3471** |
| PSNR on hidden patches, average | 14.6* | 21.60 | 21.70 | **21.81** |
| PSNR on hidden patches, ARKitScenes | 14.96* | 21.62 | 21.68 | **21.75** |
| Loss with reference hidden / shown (75% masked) | 2.26x | 1.93x | 1.94x | **2.32x** |
