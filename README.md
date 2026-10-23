# crossview-masked-reconstruction

A Vision Transformer built from scratch that reconstructs a heavily masked image of a room using a second, unmasked view of the same room. The model sees the full **reference** view and only 5 to 25% of the **target** view, and has to fill in the rest of the target.

**Model**

<img width="464" height="517" alt="Screenshot 2026-10-23 at 3 16 24 PM" src="https://github.com/user-attachments/assets/8b9cddc8-23b9-4b97-a106-c755593464dd" />

The model is trained to predict each hidden patch in normalized form, with its own brightness and contrast removed, because that gives better features. But to turn those predictions back into an image, you need each patch’s real brightness and contrast, and for hidden patches those come from the ground truth the model is supposed to be guessing. The stats head predicts these two values for every hidden patch, so the reconstruction is built entirely from the model’s own outputs, with no help from the answer.

**With Reference**
<img width="2070" height="1650" alt="Unknown-7" src="https://github.com/user-attachments/assets/e0d960b3-d2a2-458c-92e2-476e8fadbb4c" />

**Without Reference**

<img width="2070" height="1650" alt="Unknown-8" src="https://github.com/user-attachments/assets/a408cd6d-c1e5-4346-8969-e9cd8cb68fc1" />

This shows that cross attention with reference image does help in reconstruction

## Results

| | Baseline (no stats) | Linear head | MLP head, w=0.5 | MLP head, w=0.2 | **MLP head, w=0.2 + MAE encoder** |
|---|---|---|---|---|---|
| Test loss @ 90% masked | 0.3342 | 0.3538 | 0.3475 | 0.3376 | **0.3285** |
| PSNR on hidden patches, ARKitScenes | 14.96* | 21.62 | 21.68 | 21.75 | **21.86** |

\* brightness and contrast borrowed from neighboring visible patches
