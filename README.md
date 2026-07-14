# Chatterbox TTS Streaming
Chatterbox is an open source TTS model. Licensed under MIT, Chatterbox has been benchmarked against leading closed-source systems like ElevenLabs, and is consistently preferred in side-by-side evaluations.
Whether you're working on memes, videos, games, or AI agents, Chatterbox brings your content to life. It's also the first open source TTS model to support **emotion exaggeration control**, a powerful feature that makes your voices stand out. This fork adds a streaming implementation that achieves a realtime factor of 0.499 (target < 1) on a 4090 gpu and a latency to first chunk of around 0.472s

# Key Details
- SoTA zeroshot TTS
- 0.5B Llama backbone
- Unique exaggeration/intensity control
- Ultra-stable with alignment-informed inference
- Trained on 0.5M hours of cleaned data
- Watermarked outputs
- Easy voice conversion script
- **Real-time streaming generation**
- [Outperforms ElevenLabs]

### ⚡ Model Zoo

Choose the right model for your application.

| Model                                                                                                           | Size | Languages | Key Features                                            | Best For                                     | 🤗                                                                  | Examples |
|:----------------------------------------------------------------------------------------------------------------| :--- | :--- |:--------------------------------------------------------|:---------------------------------------------|:--------------------------------------------------------------------------| :--- |
| **Chatterbox-Turbo**                                                                                            | **350M** | **English** | Paralinguistic Tags (`[laugh]`), Lower Compute and VRAM | Zero-shot voice agents,  Production          | [Demo](https://huggingface.co/spaces/ResembleAI/chatterbox-turbo-demo)        | [Listen](https://resemble-ai.github.io/chatterbox_turbo_demopage/) |
| **Chatterbox-Multilingual V3** [(Language list)](#supported-languages)                                          | **500M** | **23+** | Improved speaker similarity, reduced hallucinations, more natural multilingual speech | Global applications, localization, cross-language voice cloning | [Demo](https://huggingface.co/spaces/ResembleAI/Chatterbox-Multilingual-TTS) | [Listen](https://resemble-ai.github.io/chatterbox_demopage/) |
| **Single Language Pack** [(Models)](#single-language-pack)                                                      | 500M each | 6 dedicated finetunes | Language- and region-specific quality control           | Priority languages and dialect-sensitive applications | [Models](#single-language-pack) | [Demos](#single-language-pack) |
| Chatterbox [(Tips and Tricks)](#original-chatterbox-tips)                                                       | 500M | English | CFG & Exaggeration tuning                               | General zero-shot TTS with creative controls | [Demo](https://huggingface.co/spaces/ResembleAI/Chatterbox)              | [Listen](https://resemble-ai.github.io/chatterbox_demopage/) |

## Installation
```shell
pip install chatterbox-tts
```

Alternatively, you can install from source:
```shell
# conda create -yn chatterbox python=3.11
# conda activate chatterbox

git clone https://github.com/resemble-ai/chatterbox.git
cd chatterbox
pip install -e .
```
We developed and tested Chatterbox on Python 3.11 on Debian 11 OS; the versions of the dependencies are pinned in `pyproject.toml` to ensure consistency. You can modify the code or dependencies in this installation mode.

## Usage

##### Chatterbox-Turbo

```python
import torchaudio as ta
import torch
from chatterbox.tts_turbo import ChatterboxTurboTTS

# Load the Turbo model
model = ChatterboxTurboTTS.from_pretrained(device="cuda")

# Generate with Paralinguistic Tags
text = "Hi there, Sarah here from MochaFone calling you back [chuckle], have you got one minute to chat about the billing issue?"

# Generate audio (requires a reference clip for voice cloning)
wav = model.generate(text, audio_prompt_path="your_10s_ref_clip.wav")

ta.save("test-turbo.wav", wav, model.sr)
```

##### Chatterbox and Chatterbox-Multilingual

```python

import torchaudio as ta
from chatterbox.tts import ChatterboxTTS
from chatterbox.mtl_tts import ChatterboxMultilingualTTS

device = "cuda"  # or "cpu" / "mps"

# English example
model = ChatterboxTTS.from_pretrained(device=device)

text = "Ezreal and Jinx teamed up with Ahri, Yasuo, and Teemo to take down the enemy's Nexus in an epic late-game pentakill."
wav = model.generate(text)
ta.save("test-english.wav", wav, model.sr)

# Multilingual V3 examples
multilingual_model = ChatterboxMultilingualTTS.from_pretrained(device=device, t3_model="v3")
# To use the legacy V2 multilingual checkpoint, omit t3_model or pass t3_model="v2".

french_text = "Bonjour, comment ça va? Ceci est le modèle de synthèse vocale multilingue Chatterbox, il prend en charge 23 langues."
wav_french = multilingual_model.generate(french_text, language_id="fr")
ta.save("test-french.wav", wav_french, multilingual_model.sr)

chinese_text = "你好，今天天气真不错，希望你有一个愉快的周末。"
wav_chinese = multilingual_model.generate(chinese_text, language_id="zh")
ta.save("test-chinese.wav", wav_chinese, multilingual_model.sr)

# If you want to synthesize with a different voice, specify the audio prompt
AUDIO_PROMPT_PATH = "YOUR_FILE.wav"
wav = model.generate(text, audio_prompt_path=AUDIO_PROMPT_PATH)
ta.save("test-2.wav", wav, model.sr)
```
See `example_tts.py` and `example_vc.py` for more examples.

## Supported Languages
The general-purpose Chatterbox Multilingual model supports the following languages:

Arabic (ar) • Danish (da) • German (de) • Greek (el) • English (en) • Spanish (es) • Finnish (fi) • French (fr) • Hebrew (he) • Hindi (hi) • Italian (it) • Japanese (ja) • Korean (ko) • Malay (ms) • Dutch (nl) • Norwegian (no) • Polish (pl) • Portuguese (pt) • Russian (ru) • Swedish (sv) • Swahili (sw) • Turkish (tr) • Chinese (zh)

## Single Language Pack

The Single Language Pack provides dedicated finetunes for priority languages and regional variants. Use these when you want stronger language-specific behavior, tighter quality control, or dialect-aware generation beyond the general multilingual model.

| Language | Model Card | Demo Space |
| --- | --- | --- |
| Chinese | [ResembleAI/Chatterbox-Multilingual-zh-cmn](https://huggingface.co/ResembleAI/Chatterbox-Multilingual-zh-cmn) | [Demo](https://huggingface.co/spaces/ResembleAI/Chatterbox-Multilingual-TTS-zh-cmn) |
| Latam Spanish | [ResembleAI/Chatterbox-Multilingual-es-mx-latam](https://huggingface.co/ResembleAI/Chatterbox-Multilingual-es-mx-latam) | [Demo](https://huggingface.co/spaces/ResembleAI/Chatterbox-Multilingual-TTS-es-mx-latam) |
| Brazilian Portuguese | [ResembleAI/Chatterbox-Multilingual-pt-br](https://huggingface.co/ResembleAI/Chatterbox-Multilingual-pt-br) | [Demo](https://huggingface.co/spaces/ResembleAI/Chatterbox-Multilingual-TTS-pt-br) |
| Spain Spanish | [ResembleAI/Chatterbox-Multilingual-es-es](https://huggingface.co/ResembleAI/Chatterbox-Multilingual-es-es) | [Demo](https://huggingface.co/spaces/ResembleAI/Chatterbox-Multilingual-TTS-es-es) |
| Portugal Portuguese | [ResembleAI/Chatterbox-Multilingual-pt-pt](https://huggingface.co/ResembleAI/Chatterbox-Multilingual-pt-pt) | [Demo](https://huggingface.co/spaces/ResembleAI/Chatterbox-Multilingual-TTS-pt-pt) |
| Hindi | [ResembleAI/Chatterbox-Multilingual-hi](https://huggingface.co/ResembleAI/Chatterbox-Multilingual-hi) | [Demo](https://huggingface.co/spaces/ResembleAI/Chatterbox-Multilingual-TTS-hi) |

## Original Chatterbox Tips
- **General Use (TTS and Voice Agents):**
- The default settings (`exaggeration=0.5`, `cfg_weight=0.5`) work well for most prompts.
- If the reference speaker has a fast speaking style, lowering `cfg_weight` to around `0.3` can improve pacing.
- **Expressive or Dramatic Speech:**
- Try lower `cfg_weight` values (e.g. `~0.3`) and increase `exaggeration` to around `0.7` or higher.
- Higher `exaggeration` tends to speed up speech; reducing `cfg_weight` helps compensate with slower, more deliberate pacing.

# Installation
```
python3.10 -m venv .venv
source .venv/bin/activate
pip install chatterbox-streaming
```

## Build for development
```
git clone https://github.com/davidbrowne17/chatterbox-streaming.git
pip install -e .
```

# Usage

## Basic TTS Generation
```python
import perth
import librosa

model = ChatterboxTTS.from_pretrained(device="cuda")
text = "Ezreal and Jinx teamed up with Ahri, Yasuo, and Teemo to take down the enemy's Nexus in an epic late-game pentakill."
wav = model.generate(text)
ta.save("test-1.wav", wav, model.sr)

# If you want to synthesize with a different voice, specify the audio prompt
AUDIO_PROMPT_PATH = "YOUR_FILE.wav"
wav = model.generate(text, audio_prompt_path=AUDIO_PROMPT_PATH)
ta.save("test-2.wav", wav, model.sr)
```

## Streaming TTS Generation
For real-time applications where you want to start playing audio as soon as it's available:

```python
import torchaudio as ta
import torch
from chatterbox.tts import ChatterboxTTS

model = ChatterboxTTS.from_pretrained(device="cuda")
text = "Welcome to the world of streaming text-to-speech! This audio will be generated and played in real-time chunks."

# Basic streaming
audio_chunks = []
for audio_chunk, metrics in model.generate_stream(text):
    audio_chunks.append(audio_chunk)
    # You can play audio_chunk immediately here for real-time playback
    print(f"Generated chunk {metrics.chunk_count}, RTF: {metrics.rtf:.3f}" if metrics.rtf else f"Chunk {metrics.chunk_count}")

# Combine all chunks into final audio
final_audio = torch.cat(audio_chunks, dim=-1)
ta.save("streaming_output.wav", final_audio, model.sr)
```

## Streaming with Voice Cloning
```python
import torchaudio as ta
import torch
from chatterbox.tts import ChatterboxTTS

model = ChatterboxTTS.from_pretrained(device="cuda")
text = "This streaming synthesis will use a custom voice from the reference audio file."
AUDIO_PROMPT_PATH = "reference_voice.wav"

audio_chunks = []
for audio_chunk, metrics in model.generate_stream(
    text, 
    audio_prompt_path=AUDIO_PROMPT_PATH,
    exaggeration=0.7,
    cfg_weight=0.3,
    chunk_size=25  # Smaller chunks for lower latency
):
    audio_chunks.append(audio_chunk)
    
    # Real-time metrics available
    if metrics.latency_to_first_chunk:
        print(f"First chunk latency: {metrics.latency_to_first_chunk:.3f}s")

# Save the complete streaming output
final_audio = torch.cat(audio_chunks, dim=-1)
ta.save("streaming_voice_clone.wav", final_audio, model.sr)
```

## Streaming Parameters
- `audio_prompt_path`: Reference audio path for voice cloning
- `chunk_size`: Number of speech tokens per chunk (default: 50). Smaller values = lower latency but more overhead
- `print_metrics`: Enable automatic printing of latency and RTF metrics (default: True)
- `exaggeration`: Emotion intensity control (0.0-1.0+)
- `cfg_weight`: Classifier-free guidance weight (0.0-1.0)
- `temperature`: Sampling randomness (0.1-1.0)

See `example_tts_stream.py` for more examples.

## Lora Fine-tuning
To fine-tune Chatterbox all you need are some wav audio files with the speaker voice you want to train, just the raw wavs. Place them in a folder called audio_data and run lora.py. You can configure the exact training params such as batch size, number of epochs and learning rate by modifying the values at the top of lora.py. You will need a CUDA gpu with at least 18gb of vram depending on your dataset size and training params. You can monitor the training metrics via the dynamic png created called training_metrics. This contains various graphs to help you track the training progress. If you want to try a checkpoint you can use the loadandmergecheckpoint.py (make sure to set the same R and Alpha values as you used in the training)

## GRPO Fine-tuning
Just like the lora fine-tuning for Chatterbox all you need are some wav audio files with the speaker voice you want to train, just the raw wavs. Place them in a folder called audio_data and run grpo.py. You can configure the exact training params such as batch size, number of epochs and learning rate by modifying the values at the top of grpo.py. You will need a CUDA gpu with at least 12gb of vram depending on your dataset size and training params. You can monitor the training metrics via the dynamic png created called grpo_training_metrics. This contains various graphs to help you track the training progress.

## Example metrics
Here are the example metrics for streaming latency on a 4090 using Linux
- Latency to first chunk: 0.472s
- Received chunk 1, shape: torch.Size([1, 24000]), duration: 1.000s
- Audio playback started!
- Received chunk 2, shape: torch.Size([1, 24000]), duration: 1.000s
- Received chunk 3, shape: torch.Size([1, 24000]), duration: 1.000s
- Received chunk 4, shape: torch.Size([1, 24000]), duration: 1.000s
- Received chunk 5, shape: torch.Size([1, 24000]), duration: 1.000s
- Received chunk 6, shape: torch.Size([1, 20160]), duration: 0.840s
- Total generation time: 2.915s
- Total audio duration: 5.840s
- RTF (Real-Time Factor): 0.499 (target < 1)
- Total chunks yielded: 6


## Official Discord

👋 Join us on [Discord](https://discord.gg/rJq9cRJBJ6) and let's build something awesome together!

## Evaluation
Chatterbox Turbo was evaluated using Podonos, a platform for reproducible subjective speech evaluation.

We compared Chatterbox Turbo to competitive TTS systems using Podonos' standardized evaluation suite, focusing on overall preference, naturalness, and expressiveness.

Evaluation reports:
- [Chatterbox Turbo vs ElevenLabs Turbo v2.5](https://podonos.com/resembleai/chatterbox-turbo-vs-elevenlabs-turbo)
- [Chatterbox Turbo vs Cartesia Sonic 3](https://podonos.com/resembleai/chatterbox-turbo-vs-cartesia-sonic3)
- [Chatterbox Turbo vs VibeVoice 7B](https://podonos.com/resembleai/chatterbox-turbo-vs-vibevoice7b)

These evaluations were conducted under identical conditions and are publicly accessible via Podonos.

## Acknowledgements
- [Podonos](https://podonos.com) — for supporting reproducible subjective speech evaluation
- [Cosyvoice](https://github.com/FunAudioLLM/CosyVoice)
- [Real-Time-Voice-Cloning](https://github.com/CorentinJ/Real-Time-Voice-Cloning)
- [HiFT-GAN](https://github.com/yl4579/HiFTNet)
- [Llama 3](https://github.com/meta-llama/llama3)
- [S3Tokenizer](https://github.com/xingchensong/S3Tokenizer)

# Built-in PerTh Watermarking for Responsible AI
Every audio file generated by Chatterbox includes [Resemble AI's Perth (Perceptual Threshold) Watermarker](https://github.com/resemble-ai/perth) - imperceptible neural watermarks that survive MP3 compression, audio editing, and common manipulations while maintaining nearly 100% detection accuracy.

# Disclaimer
Don't use this model to do bad things. Prompts are sourced from freely available data on the internet.

## Streaming Implementation Author
David Browne

## Support me
Support this project on Ko-fi: https://ko-fi.com/davidbrowne17
