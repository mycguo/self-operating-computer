# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Core Architecture

This is a Self-Operating Computer Framework that enables multimodal models to operate computers through screen vision and automated actions. The system:

- Takes screenshots, analyzes them with AI models, and performs mouse/keyboard actions
- Supports multiple AI models: GPT-4o, GPT-4.1, o1, Gemini Pro Vision, Claude 3, Qwen-VL, and LLaVa
- Operates through a main loop that iterates up to 10 times per session
- Has three main interaction modes: OCR-based (default), Set-of-Mark (SoM), and vanilla vision

Key architectural components:
- `operate/main.py` - CLI entry point and argument parsing
- `operate/operate.py` - Main execution loop and operation dispatcher  
- `operate/models/apis.py` - AI model integrations and API calls
- `operate/utils/` - Screen capture, OCR, labeling, and OS interaction utilities
- `operate/config.py` - Configuration management and model validation

## Development Commands

**Installation & Setup:**
```bash
pip install self-operating-computer
# OR for development:
pip install -r requirements.txt
pip install -r requirements-audio.txt  # For voice mode
```

**Running the System:**
```bash
operate                           # Default (gpt-4-with-ocr)
operate -m gpt-4.1-with-ocr      # Latest GPT-4.1 model  
operate -m o1-with-ocr           # OpenAI o1 model
operate -m claude-3              # Anthropic Claude 3
operate -m gemini-pro-vision     # Google Gemini
operate -m llava                 # Local LLaVa via Ollama
operate --voice                  # Voice input mode
operate --verbose                # Debug output
operate --prompt "task here"     # Direct prompt
```

**Evaluation:**
```bash
python evaluate.py -m MODEL_NAME  # Run test cases against a model
```

## Key Implementation Details

**Operation Types:**
The system supports these atomic operations defined in `operate/operate.py:operate()`:
- `click` - Mouse clicks with x,y coordinates  
- `write` - Text input
- `press`/`hotkey` - Keyboard shortcuts
- `done` - Task completion signal

**Model Integration Patterns:**
Each model type has its own handler in `operate/models/apis.py`:
- OCR models extract text coordinates for enhanced clicking accuracy
- SoM models use YOLO object detection with visual labels
- Models receive screenshots and return structured operation responses

**Screenshot & Vision Pipeline:**
- `operate/utils/screenshot.py` captures screens with cursor
- `operate/utils/ocr.py` extracts text elements and coordinates  
- `operate/utils/label.py` adds visual markers for SoM mode
- Images are base64 encoded for model APIs

**Configuration:**
- API keys stored in `.env` file  
- Model validation in `operate/config.py`
- Session limits and verbose logging available

## Testing

The codebase includes an evaluation framework (`evaluate.py`) that:
- Runs predefined test cases against any model
- Takes screenshots after completion
- Uses GPT-4o to evaluate if objectives were met
- Currently includes tests for Github.com and YouTube.com navigation