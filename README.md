# Stable Diffusion

Welcome to the official repository for the Sensorial System's Stable Diffusion projects. For now, this repository only hosts the codebase for our Stable Diffusion trainer, designed to make it easier to automate all the steps of finetuning Stable Diffusion models.

## Features

- **Stable Diffusion LoRA training**: An automatable trainer for Stable Diffusion LoRA.
- **Rust Library**: The trainer is available as a Rust library.
- **Command Line Interface (CLI)**: For ease of use, the trainer can be accessed via a CLI, making it accessible for various use cases.

## Requirements

- **kohya_ss**: Follow the installation guidelines here [https://github.com/bmaltais/kohya_ss](https://github.com/bmaltais/kohya_ss).

## Stable Diffusion CLI

Install the CLI tool:
```bash
cargo install stable-diffusion-cli
```

Setup the environment:
```bash
stable-diffusion-cli setup
```

Get help to use the cli:
```bash
stable-diffusion-cli train --help
```

## Examples

We have a [dataset with photos of Bacana](examples/training/lora/bacana/images), a Coton de Tuléar, conceptualized as `bacana white dog` to not mix with the existing `Coton de Tuléar` concept in the `Stable Diffusion XL` model.

Some of the training images in [examples/training/lora/bacana/images](examples/training/lora/bacana/images):
<p>
<img src="examples/training/lora/bacana/images/IMG_5175.PNG" width="128">
<img src="examples/training/lora/bacana/images/IMG_5176.PNG" width="128">
<img src="examples/training/lora/bacana/images/IMG_5180.PNG" width="128">
</p>

The training parameters looks like this:

```json
{
    "prompt": {
        "instance": "bacana",
        "class": "white dog"
    },
    "dataset": {
        "training": "images"
    },
    "network": {
        "dimension": 8,
        "alpha": 1.0
    },
    "output": {
        "name": "{prompt.instance}({prompt.class})d{network.dimension}a{network.alpha}",
        "directory": "./output"
    },
    "training": {
        "optimizer": "Adafactor",
        "learning_rate": {
            "scheduler": "Constant"
        }    
    }
}
```

Note that the `output.name` is a format string that captures the parameters values. This is useful for experimenting with different parameters and keeping track of them in the model file name.

Train the example with:
```bash
stable-diffusion-cli train --config examples/training/lora/bacana/parameters.json
```

The LoRA safetensor file will be generated as
```bash
examples/training/lora/bacana/output/bacana(white dog)d8a1-000001.safetensors
examples/training/lora/bacana/output/bacana(white dog)d8a1.safetensors
```

Where, in this case, `bacana(white dog)d8a1-000001.safetensors` is the first epoch and `bacana(white dog)d8a1.safetensors` is the final epoch.

You can then
```bash
cd examples/training/lora/bacana/generation
```
and run
```bash
python generate.py
```
to test image generation with the LoRA model. The generated images will be present in [examples/training/lora/bacana/generation](examples/training/lora/bacana/generation).

Some of the generated images:
<p>
<img src="examples/training/lora/bacana/generation/bacana as a fireman.png" width="128" />
<img src="examples/training/lora/bacana/generation/bacana as a scientist.png" width="128" />
<img src="examples/training/lora/bacana/generation/bacana as an astronaut.png" width="128" />
</p>

## Development tips

### Debugging

To check the training folder structure required by `kohya_ss` set the `TRAINING_DIR` to, for example, `./training` like:

`TRAINING_DIR=./training stable-diffusion-cli train ...`