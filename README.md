# SpeakerAI

SpeakerAI converts meeting recordings into searchable transcripts, enriches each speaker with reusable voice profiles, and captures psychometric insights that help identify returning participants across sessions.

## Features
- Whisper-based speech-to-text transcription with diarization-driven speaker labeling.
- Acoustic feature extraction (pitch, speaking rate, energy, spectral statistics) for long-term voice signatures.
- Psychometric scoring of each speaker turn to highlight emotional tone.
- SQLite persistence layer for reusing speaker profiles and confirming future matches.
- FastAPI web application (served on port 7883) and CLI workflow for batch processing.
- Dual output artifacts: a detailed transcript and a speaker intelligence report.

## Automated Environment Setup
Run the bootstrap script once to create the virtual environment, install dependencies, and download an FFmpeg binary when the host system does not already provide one:

```bash
./scripts/bootstrap.sh
```

After the script succeeds, activate the environment for subsequent commands:

```bash
source .venv/bin/activate
```

### Manual Setup (Optional)
If you prefer manual steps, ensure Python 3.10+ is available and run:

```bash
python -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip wheel
python -m pip install -r requirements.txt
```

The project depends on FFmpeg for audio decoding. Installing `imageio-ffmpeg` (already listed in `requirements.txt`) automatically downloads a compatible binary when needed. You can also install the system package (e.g., `sudo apt-get install ffmpeg`).

## Running the Web App
Start the FastAPI server on port 7883:

```bash
uvicorn speakerai.web.app:app --host 0.0.0.0 --port 7883 --reload
```

Open `http://localhost:7883` in a browser, upload an MP3 recording, and follow the prompts to review transcripts, review or confirm speaker matches, and export both the transcript and speaker report artifacts.

## CLI Usage
Process a meeting from the command line:

```bash
python -m speakerai.cli /path/to/meeting.mp3 --team "Alice Example" "Bob Example"
```

Outputs are stored under `data/outputs/transcripts/` and `data/outputs/reports/`. The CLI prompts for speaker confirmation when prior matches are available and updates the SQLite database (`data/speaker_profiles.sqlite3`).

## Testing
Run the test suite with:

```bash
pytest
```

## Project Structure
- `speakerai/audio/`: Transcription, diarization, acoustic features, and psychometric scoring modules.
- `speakerai/pipeline.py`: Orchestrates the end-to-end meeting analysis.
- `speakerai/web/`: FastAPI app, templates, and static assets for the web experience.
- `speakerai/database.py`: SQLite models and persistence helpers.
- `data/`: Default location for outputs and the speaker profile database.
- `tests/`: Automated tests covering the pipeline.

## Troubleshooting
- **Whisper model downloads**: The first run downloads the selected Whisper model (default `small`). Subsequent executions reuse the cached model.
- **GPU acceleration**: Install a CUDA-enabled PyTorch wheel before running the bootstrap script to take advantage of GPU inference.
- **Missing FFmpeg**: Re-run `./scripts/bootstrap.sh` or install FFmpeg via your OS package manager if audio decoding fails.

