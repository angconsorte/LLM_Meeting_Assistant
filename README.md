# LLM Meeting Assistant
**An AI system I designed and built that turns meeting recordings into transcripts, executive summaries, and action items, using open-source AI models on free cloud GPUs at zero cost.**

```
Meeting audio/video ──► ffmpeg ──► Whisper large-v3 ──► Transcript ──► Mistral-7B LLM ──► Structured report
                                   (speech-to-text)                    (4-bit, free T4 GPU)   summary · tone · topics
                                                                                              action items · decisions
```

## The Problem
As the CDO of the **AU Investment Club**, I saw valuable financial analysis and strategy discussions disappear the moment meetings ended. Absent members couldn't catch up, and the marketing team had nothing to build content from. Paid tools like Otter.ai weren't an option.

## What I Built
An end-to-end AI pipeline that runs entirely in **Google Colab**, with no paid APIs and no local hardware:

1. **Speech-to-text:** Whisper large-v3 via `faster-whisper` (CTranslate2, FP16). It's about **4x faster** than the standard OpenAI library, so a 1-hour meeting fits inside the free Colab runtime.
2. **LLM summarization:** **Mistral-7B-Instruct** loaded with **4-bit quantization** (`bitsandbytes`), which fits a 7B-parameter model into the 16 GB VRAM of a free T4 GPU.
3. **Prompt engineering:** a custom minute-taker prompt that returns a consistent, structured report:
   - **Summary** of how the meeting progressed
   - **Tone** of the discussion
   - **Key topics**
   - **Action items** with owners and due dates
   - **Decisions** and approvals

## Engineering Decisions
| Challenge | Decision |
|---|---|
| Zero budget | Open-source models only, hosted on free Colab GPUs |
| 1-hour audio vs. runtime limits | `faster-whisper` instead of the reference Whisper implementation |
| 7B LLM vs. 16 GB GPU | 4-bit quantization with `bitsandbytes` + `accelerate` device mapping |
| Unstructured LLM output | Instruction-tuned model + fixed report schema in the prompt |
| GPU memory between stages | Free the speech model (`gc`, `torch.cuda.empty_cache`) before loading the LLM |

## Impact
- **Absent members stay aligned:** every meeting produces an executive summary.
- **Content engine:** transcripts give the marketing team raw material for social posts.
- **Searchable archive:** all club decisions are documented, with no manual note-taker.

## Tech Stack
**Python** · **Whisper large-v3** (`faster-whisper`) · **Mistral-7B-Instruct** (Hugging Face `transformers`) · `bitsandbytes` · `accelerate` · PyTorch · ffmpeg · Google Colab (NVIDIA T4)

## Run It
Open [`meetingTnS.ipynb`](meetingTnS.ipynb) in Google Colab, set the runtime to **T4 GPU**, mount Google Drive with your meeting recording, and run all cells.

---
*Developed with an AI-assisted workflow for dependency setup and prompt iteration.*
