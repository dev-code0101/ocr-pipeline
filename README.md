## Batch OCR (PaddleOCR) — Docker + macOS Native  

A fast, reliable PDF‑to‑text pipeline that extracts embedded text with PyMuPDF and falls back to PaddleOCR when needed. It processes folders recursively, filters low‑quality output, and mirrors the original directory structure under `ocr_results`.

---

### Quick Start  

```bash
# Build the Docker image (Windows/Linux/macOS)
docker build -t batch-ocr:latest -f Dockerfile .

# Run the container
# Windows
.\run-d.bat
# Linux/macOS
sh run.sh
```

The Gradio UI will be available at `http://localhost:7888`.

---

### Features  

| Feature | Description |
|---------|-------------|
| **Recursive PDF scan** | Walks any root folder, extracts embedded text, OCR‑fallback when needed |
| **Quality filter** | Removes noisy OCR output |
| **Mirrored output** | Writes `_ocr.txt` files under `ocr_results/` preserving folder hierarchy |
| **GPU/CPU toggle** | Selectable from the UI; GPU only on Windows/Linux with NVIDIA CUDA |
| **CLI mode** | Run batch OCR without the UI (see *Headless CLI* below) |
| **PP‑Structure (app2.py)** | Optional document layout parsing, JSON/Markdown export |

---

### System Requirements  

- Docker 24+  
- Windows 11, Linux (Ubuntu/Debian recommended), or macOS 12+ (CPU‑only)  
- Optional NVIDIA GPU + drivers + NVIDIA Container Toolkit for GPU acceleration (Windows/Linux)

---

### Building & Running  

#### Docker (all platforms)

```bash
docker build -t batch-ocr:latest -f Dockerfile .
```

- **Windows** – `.\run-d.bat`  
- **Linux/macOS** – `sh run.sh`

#### macOS native (CPU‑only)

```bash
bash batch-ocr/osx_setup_run_test.sh   # creates venv, installs deps, runs a smoke test
source batch-ocr/.venv/bin/activate
python batch-ocr/app.py                # or app2.py for PP‑Structure
```

---

### Using the Gradio UI  

1. Open `http://localhost:7888`.  
2. Set **Root PDF Folder** to a path inside `/workspace` (e.g., `/workspace/pdfs`).  
3. Toggle **Use GPU** as needed and click **Initialize OCR**.  
4. Click **List PDFs** to verify detection.  
5. Click **OCR All PDFs** to start processing.  

Outputs appear in `/workspace/ocr_results`, preserving the input tree. Errors generate `_ERROR.txt` files alongside the OCR results.

---

### Headless CLI (no UI)

```bash
# Classic OCR pipeline
python batch-ocr/ocr_cli.py --mode classic --root /path/to/pdfs

# PP‑Structure + OCR pipeline
python batch-ocr/ocr_cli.py \
  --mode structure \
  --root /path/to/pdfs \
  --lang en \
  --render-scale 2.0 \
  --export-txt --export-json --export-md \
  --force-ocr
```

Add `--no-gpu` or `--use-gpu` to control GPU usage.

---

### Project Layout  

- `app.py` – Gradio UI + classic OCR pipeline  
- `app2.py` – UI with PP‑StructureV3 (layout parsing)  
- `Dockerfile` – Image with PaddleOCR and dependencies  
- `run-d.bat` / `run.sh` – Scripts to launch the container  
- `osx_setup_run_test.sh` – macOS native setup script  
- `ocr_cli.py` – Command‑line interface  
- `assets/` – Images (optional)  

---

### Troubleshooting  

- **GPU not detected** – Ensure NVIDIA drivers and the Container Toolkit are installed; otherwise uncheck “Use GPU”.  
- **No PDFs found** – Verify the folder is inside `/workspace` and contains `.pdf` files; use “List PDFs” to confirm.  
- **Permission errors** – Make sure the host directory is writable.  
- **Slow CPU performance** – Reduce the render scale in `app.py` (e.g., change `fitz.Matrix(2, 2)` to `fitz.Matrix(1, 1)`).  

---

### License & Credits  

Uses PaddleOCR, PyMuPDF, Gradio, OpenCV, and Pillow—refer to each project's license for details.