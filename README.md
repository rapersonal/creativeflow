<div align="center" width="100" height="100" >
  <img src="https://github.com/rapersonal/creativeflow/blob/main/assets/flow.gif" />
  <div align="center" style="font-style: italic;" >
</div>


# Text-To-Video
## Text To Video model using Diffusers 🧨 

Recommendation:@damo-vilab
https://github.com/damo-vilab/i2vgen-xl

https://github.com/rapersonal/creativeflow/blob/main/assets/flow.gif

## Getting Started

### Requirements & Installation

```bash
git clone https://github.com/ExponentialML/Text-To-Video-Finetuning.git
cd Text-To-Video-Finetuning
git lfs install
git clone https://huggingface.co/damo-vilab/text-to-video-ms-1.7b ./models/model_scope_diffusers/
```

## Other Models
Alternatively, you can train starting from other models made by the community.

| Contributer    |Model Name    | Link                                                |
| -------------- | ------------ | --------------------------------------------------- | 
| cerspense      | ZeroScope    | https://huggingface.co/cerspense/zeroscope_v2_576w  |
| cameduru       | Potat1       | https://huggingface.co/camenduru/potat1             |
| strangeman3107 | animov-512x  | https://huggingface.co/strangeman3107/animov-512x   |

### Create Conda Environment (Optional)
It is recommended to install Anaconda.

**Windows Installation:** https://docs.anaconda.com/anaconda/install/windows/

**Linux Installation:** https://docs.anaconda.com/anaconda/install/linux/

```bash
conda create -n text2video-finetune python=3.10
conda activate text2video-finetune
```

### Python Requirements
```bash
pip install -r requirements.txt
```

## Hardware
Recommended to use a RTX 3090, but you should be able to train on GPUs with <= 16GB ram with:
- Validation turned off.
- Xformers or Torch 2.0 Scaled Dot-Product Attention 
- Gradient checkpointing enabled. 
- Resolution of 256.
- Hybrid LoRA training.
- Training only using LoRA with ranks between 4-16.

## Preprocessing your data

### Using Captions

You can use caption files when training on images or video. Simply place them into a folder like so:

**Images**: `/images/img.png /images/img.txt`
**Videos**: `/videos/vid.mp4 | /videos/vid.txt`

Then in your config, make sure to have `-folder` enabled, along with the root directory containing the files.

### Process Automatically

You can automatically caption the videos using the [Video-BLIP2-Preprocessor Script](https://github.com/ExponentialML/Video-BLIP2-Preprocessor)

## Configuration

The configuration uses a YAML config borrowed from [Tune-A-Video](https://github.com/showlab/Tune-A-Video) reposotories. 

All configuration details are placed in `configs/v2/train_config.yaml`. Each parameter has a definition for what it does.

## Training

***Please read this section carefully if you are training ***

You can also train a LoRA that is compatible with the webui extension. 
By default it's set to `'cloneofsimo'`, which was the first LoRA implementation for Stable Diffusion.

This ('cloneofsimo') version you can use in the `inference.py` file in this repository. It is **not** compatible with the webui.

To train and ***use*** a LoRA with the webui, change the `lora_version` to **"stable_lora"** in your config if you already have one made.

This will train an [A1111 webui extension](https://github.com/kabachuha/sd-webui-text2video) compatibile LoRA.
You can get started at `configs/v2/stable_lora_config.yaml` and everything is set by default in there. During and after training, LoRAs will be saved in your outputs directory with the prefix `_webui`.

If you do not choose this setting, you *will not* currently be able to use these in the webui. If you train a Stable LoRA file, you cannot *currently* use them in `inference.py`.

### Continue training a LoRA
To continue training a LoRA, simply set your `lora_path` in your config to the **directory** that contains your LoRA file(s), not an individual file. 
Each specific LoRA should have `_unet` or `_text_encoder` in the file name respectively, or else it will not work.

You should then be able to resume training from a LoRA model, regardless of which method you use (as long as the trained LoRA matches the version in the config).

### What you cannot do:
- Use LoRA files that were made for SD image models in other trainers.
- Use 'cloneofsimo' LoRAs in another project (unless you build it or create a PR)
- Merge LoRA weights together (yet).

## Finetune.
```python
python train.py --config ./configs/v2/train_config.yaml
```
---

## Training Results

With a lot of data, you can expect training results to show at roughly 2500 steps at a constant learning rate of 5e-6. 

When finetuning on a single video, you should see results in half as many steps.

After training, you should see your results in your output directory. 

By default, it should be placed at the script root under `./outputs/train_<date>`

From my testing, I recommend:

- Keep the number of sample frames between 4-16. Use long frame generation for inference, *not* training.
- If you have a low VRAM system, you can try single frame training or just use `n_sample_frames: 2`.
- Using a learning rate of about `5e-6` seems to work well in all cases.
- The best quality will always come from training the text encoder. If you're limited on VRAM, disabling it can help.
- Leave some memory to avoid OOM when saving models during training.

## Running inference
The `inference.py` script can be used to render videos with trained checkpoints.

Example usage: 
```
python flow.py \
  --model camenduru/potat1 \
  --prompt "a fast moving fancy sports car" \
  --num-frames 60 \
  --window-size 12 \
  --width 1024 \
  --height 576 \
  --sdp
```

```
> python flow.py --help

usage: inference.py [-h] -m MODEL -p PROMPT [-n NEGATIVE_PROMPT] [-o OUTPUT_DIR]
                    [-B BATCH_SIZE] [-W WIDTH] [-H HEIGHT] [-T NUM_FRAMES]
                    [-WS WINDOW_SIZE] [-VB VAE_BATCH_SIZE] [-s NUM_STEPS]
                    [-g GUIDANCE_SCALE] [-i INIT_VIDEO] [-iw INIT_WEIGHT] [-f FPS]
                    [-d DEVICE] [-x] [-S] [-lP LORA_PATH] [-lR LORA_RANK] [-rw]

options:
  -h, --help            show this help message and exit
  -m MODEL, --model MODEL
                        HuggingFace repository or path to model checkpoint directory
  -p PROMPT, --prompt PROMPT
                        Text prompt to condition on
  -n NEGATIVE_PROMPT, --negative-prompt NEGATIVE_PROMPT
                        Text prompt to condition against
  -o OUTPUT_DIR, --output-dir OUTPUT_DIR
                        Directory to save output video to
  -B BATCH_SIZE, --batch-size BATCH_SIZE
                        Batch size for inference
  -W WIDTH, --width WIDTH
                        Width of output video
  -H HEIGHT, --height HEIGHT
                        Height of output video
  -T NUM_FRAMES, --num-frames NUM_FRAMES
                        Total number of frames to generate
  -WS WINDOW_SIZE, --window-size WINDOW_SIZE
                        Number of frames to process at once (defaults to full
                        sequence). When less than num_frames, a round robin diffusion
                        process is used to denoise the full sequence iteratively one
                        window at a time. Must be divide num_frames exactly!
  -VB VAE_BATCH_SIZE, --vae-batch-size VAE_BATCH_SIZE
                        Batch size for VAE encoding/decoding to/from latents (higher
                        values = faster inference, but more memory usage).
  -s NUM_STEPS, --num-steps NUM_STEPS
                        Number of diffusion steps to run per frame.
  -g GUIDANCE_SCALE, --guidance-scale GUIDANCE_SCALE
                        Scale for guidance loss (higher values = more guidance, but
                        possibly more artifacts).
  -i INIT_VIDEO, --init-video INIT_VIDEO
                        Path to video to initialize diffusion from (will be resized to
                        the specified num_frames, height, and width).
  -iw INIT_WEIGHT, --init-weight INIT_WEIGHT
                        Strength of visual effect of init_video on the output (lower
                        values adhere more closely to the text prompt, but have a less
                        recognizable init_video).
  -f FPS, --fps FPS     FPS of output video
  -d DEVICE, --device DEVICE
                        Device to run inference on (defaults to cuda).
  -x, --xformers        Use XFormers attnetion, a memory-efficient attention
                        implementation (requires `pip install xformers`).
  -S, --sdp             Use SDP attention, PyTorch's built-in memory-efficient
                        attention implementation.
  -lP LORA_PATH, --lora_path LORA_PATH
                        Path to Low Rank Adaptation checkpoint file (defaults to empty
                        string, which uses no LoRA).
  -lR LORA_RANK, --lora_rank LORA_RANK
                        Size of the LoRA checkpoint's projection matrix (defaults to
                        64).
  -rw, --remove-watermark
                        Post-process the videos with LAMA to inpaint ModelScope's
                        common watermarks.
```
## Shoutouts

- [Showlab](https://github.com/showlab/Tune-A-Video) and bryandlee[https://github.com/bryandlee/Tune-A-Video] for their Tune-A-Video contribution that made this much easier.
- [lucidrains](https://github.com/lucidrains) for their implementations around video diffusion.
- [cloneofsimo](https://github.com/cloneofsimo) for their diffusers implementation of LoRA.
- [kabachuha](https://github.com/kabachuha) for their conversion scripts, training ideas, and webui works.
- [JCBrouwer](https://github.com/JCBrouwer) Inference implementations.
- [sergiobr](https://github.com/sergiobr) Helpful ideas and bug fixes.

## Citations
[ModelScope Text-to-Video Technical Report](https://arxiv.org/abs/2308.06571):
```bibtex

```
