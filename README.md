<div align="center" width="100" height="100" >
  <img src="https://github.com/rapersonal/creativeflow/blob/main/assets/flow.gif" />
  <div align="center" style="font-style: italic;" >
</div>


# Text To Animation
## Text To Animation using Diffusers 🧨 
### Requirements & Installation

```bash
git clone https://github.com/ExponentialML/Text-To-Video-Finetuning.git
cd Text-To-Video-Finetuning
git lfs install
git clone https://huggingface.co/damo-vilab/text-to-video-ms-1.7b ./models/model_scope_diffusers/
```

## Alternatively Training Models

| Contributer    |Model Name    | Link                                                |
| -------------- | ------------ | --------------------------------------------------- | 
| cerspense      | ZeroScope    | https://huggingface.co/cerspense/zeroscope_v2_576w  |
| cameduru       | Potat1       | https://huggingface.co/camenduru/potat1             |
| strangeman3107 | animov-512x  | https://huggingface.co/strangeman3107/animov-512x   |

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

## Citations
[ModelScope Text-to-Video Technical Report](https://arxiv.org/abs/2308.06571):
```bibtex```
