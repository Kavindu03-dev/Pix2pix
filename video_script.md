Pix2Pix — Sketch to Facade
15-minute teleprompter-ready recording script

Recording notes:
- Speak clearly and slowly (approx. 120–140 wpm).
- Use a clean screen recorder (OBS recommended). Capture the notebook window and your webcam for a personal touch.
- Show code blocks and results at 1080p; use zoom/annotations for small text.
- If you will demo training, capture short clips (10–30s) of TensorBoard and generated images over steps.

[00:00] Title Card (0:00–0:05)
- Visual: Project title card: "Pix2Pix — Sketch to Facade"
- Audio: 1–2s music sting

[00:05] Hook (0:05–0:25)
Narration (read):
"Want to convert simple architectural sketches into photorealistic facades? In the next 15 minutes I’ll walk you through a complete Pix2Pix implementation: the data pipeline, the U‑Net generator, the PatchGAN discriminator, the loss functions, and how to train and visualize results."

On-screen actions:
- Show a before/after sample image pair (input sketch → generated facade).
- Lower-third: "Pix2Pix — sketch→facade | 15-minute walkthrough"

[00:25] Roadmap (0:25–0:50)
Narration:
"Here’s the plan: first the dataset and preprocessing, then the generator and discriminator architectures, followed by losses and the training loop, and finally results and reproduction steps so you can run this yourself."

On-screen:
- Bulleted roadmap overlay.

SECTION 1 — Dataset & Preprocessing (0:50–3:00)
[00:50] Dataset overview (0:50–1:20)
Narration:
"We use the official 'facades' dataset (400 paired images). Each file is 256x512: the left half is the real facade and the right half is the sketch." 

On-screen:
- Show README snippet and the concatenated sample image; draw a vertical line to show the left/right split.

[01:20] Download & path (1:20–1:50)
Narration:
"The notebook uses tf.keras.utils.get_file to download and extract the dataset and sets PATH to the extracted folder." 

On-screen:
- Display the download cell and highlight the URL and PATH assignment.

[01:50] Loading & splitting (1:50–2:35)
Narration:
"The `load()` function reads each JPEG, decodes it, computes width//2, and slices into input_image (sketch) and real_image (facade). Both are cast to float32." 

On-screen actions:
- Show the `load()` cell and visually annotate the slicing lines. Run the sample cell showing `plt.imshow()` for both halves if possible.

[02:35] Augmentation & normalization (2:35–3:00)
Narration:
"Data augmentation: resize to 286, random crop to 256, and random horizontal flip. Then normalize pixels to [-1,1] to match the generator's tanh output." 

On-screen:
- Show `random_jitter()` and `normalize()` cells and a 2x2 grid of augmented inputs.

SECTION 2 — Generator (U‑Net) (3:00–5:40)
[03:00] U‑Net intuition (3:00–3:30)
Narration:
"The generator is a U‑Net: an encoder that compresses spatial info and a decoder that upsamples, with skip connections to preserve fine details." 

On-screen:
- Animated U‑Net diagram (encoder → bottleneck → decoder + skip arrows).

[03:30] Encoder blocks (3:30–4:15)
Narration:
"Each downsample block is Conv2D (4x4, stride 2), optional BatchNorm, and LeakyReLU. Initialization: random normal (std=0.02)."

On-screen:
- Highlight `downsample()` cell and show a single-block shape example.

[04:15] Decoder blocks & skip connections (4:15–5:15)
Narration:
"Decoder blocks use Conv2DTranspose + BatchNorm + ReLU; dropout is applied to early upsampling layers. We concatenate matching encoder outputs (skips) to recover spatial details." 

On-screen actions:
- Show `upsample()` and the `Generator()` cell, highlight `skips.append` and `Concatenate()` lines.

[05:15] Output & quick test (5:15–5:40)
Narration:
"Final layer uses tanh to output images in [-1,1]. You can instantiate the generator and run a sample input to confirm output shape 256x256x3." 

On-screen:
- Show generator instantiation and a single generated image (note: untrained will look random).

SECTION 3 — Discriminator (PatchGAN) (5:40–7:10)
[05:40] PatchGAN concept (5:40–6:10)
Narration:
"PatchGAN classifies whether each NxN patch in the image is real or fake. For 256x256 inputs this produces a 30x30 output map focusing on local structure and texture." 

On-screen:
- Visual: grid overlay showing patch decisions.

[06:10] Architecture walkthrough (6:10–6:50)
Narration:
"We concatenate input and target, then apply three downsample blocks and a couple of classification conv layers to produce a single-channel patch map." 

On-screen:
- Show `Discriminator()` cell and highlight the final Conv2D(1,4) output.

[06:50] Visual test (6:50–7:10)
Narration:
"Before training you'll see random scores; during training the map highlights areas the discriminator considers fake."

On-screen:
- Show the `disc_out` visualization (RdBu heatmap).

SECTION 4 — Losses & Training (7:10–10:00)
[07:10] Loss components (7:10–7:50)
Narration:
"Generator loss = GAN loss (BCE) + LAMBDA * L1 loss (MAE). L1=mean absolute error enforces structural similarity; lambda=100 balances the two. Discriminator loss is BCE on real vs generated patches." 

On-screen:
- Show `generator_loss()` and `discriminator_loss()` cells; call out LAMBDA.

[07:50] Optimizers & checkpoints (7:50–8:20)
Narration:
"Both networks use Adam with lr=2e‑4 and beta1=0.5. Checkpoints are saved every 5000 steps under `training_checkpoints/`."

On-screen:
- Show optimizer and checkpoint code cells.

[08:20] train_step() (8:20–9:10)
Narration:
"The train_step uses two GradientTapes for generator and discriminator, computes gen & disc losses, then applies gradients. The function is decorated with @tf.function for speed." 

On-screen:
- Highlight `train_step` cell and the gradient/apply_gradients lines; annotate the logging to TensorBoard.

[09:10] fit() loop & monitoring (9:10–10:00)
Narration:
"fit() runs for a given number of steps (40k default). It visualizes progress every 1k steps, prints dots frequently, and saves checkpoints every 5k. Use TensorBoard to monitor 'gen_total_loss', 'gen_l1_loss', and 'disc_loss'."

On-screen actions:
- Show `fit()` cell and `%tensorboard` cell; if recording, include a brief TensorBoard clip showing loss curves.

SECTION 5 — Results & Evaluation (10:00–12:15)
[10:00] generate_images() demo (10:00–10:40)
Narration:
"generate_images() plots Input, Ground Truth, and Predicted image side-by-side. Note conversion from [-1,1] to [0,1] (x*0.5+0.5)." 

On-screen:
- Show `generate_images()` cell and run it on a few test examples (capture the outputs).

[10:40] Interpreting results (10:40–11:40)
Narration:
"With sufficient training the generated facades show realistic textures and preserved architectural lines. Watch for common failure modes: blurring, checkerboard artifacts, or mode collapse. L1 loss helps maintain structure." 

On-screen:
- Show a short montage of generated images across training steps if available.

[11:40] Suggested improvements (11:40–12:15)
Narration:
"To improve results: more data, higher-res images, perceptual loss (VGG), or alternative GAN variants. Also consider unpaired methods (CycleGAN) for unpaired datasets." 

On-screen:
- Bullet list of improvements.

SECTION 6 — Reproduce & Demo Commands (12:15–13:25)
[12:15] Quick reproduction steps (12:15–12:45)
Narration:
"To reproduce locally: create a Python venv, install TensorFlow, open the notebook, and run cells. For GPU use Google Colab or a local GPU."

On-screen: show these commands in the video description and overlay (PowerShell):

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
pip install --upgrade pip
pip install tensorflow matplotlib numpy jupyterlab
jupyter lab
```

[12:45] Smoke test & inference (12:45–13:25)
Narration:
"Run a quick smoke test to ensure TensorFlow is installed: build the generator and discriminator and run a forward pass on random input. Then restore a checkpoint to run inference on new sketches." 

On-screen:
- Show the quick_test.py idea or the `checkpoint.restore` cell.

CLOSING & CTAs (13:25–15:00)
[13:25] Final summary (13:25–14:00)
Narration:
"We covered the full Pix2Pix pipeline: data, model architectures, losses, training, and evaluation. The repository contains everything you need to reproduce the results."

[14:00] Creative touches and grading tips (14:00–14:40)
Narration:
"To score highly on presentation: add animated diagrams, crisp voiceover, captions, and a short before/after montage. For creativity, use GenAI to generate a brief animation explaining skip connections or to synthesize a voiceover draft you can read."

On-screen:
- Quick checklist for the marking guide: Technical accuracy (code runs), Explanation (clear walkthrough), Dataset (use real facades), Video quality (good audio+visuals), Creativity (AI visuals/editing).

[14:40] Closing CTA (14:40–15:00)
Narration:
"If you want, I can export this script as a teleprompter file, create a short slide deck, or prepare a Colab that runs on GPU. Which would you like me to produce next?"

---
File location: video_script.md

Next steps I can take for you (pick one):
- Export a plain text teleprompter file (ready to paste into a prompter app).
- Create quick_test.py for a smoke test that builds models and runs a forward pass.
- Prepare a Colab-ready notebook that runs training on GPU with preconfigured cells and optional timers.
