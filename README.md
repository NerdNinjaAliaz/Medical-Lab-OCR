<div align="center">
<h1>🩺 خانواده من (My Family) 
 Medical OCR & Clinical Triage Vault</h1>
<p><b>A high-performance, CPU-optimized document analysis pipeline for digital health ecosystems.</b></p>
</div>

⚠️ The Problem: Extracting medical data from mixed Persian/English lab reports usually results in severe column-shifting, scrambled reference ranges, and dangerous clinical hallucinations due to Right-to-Left (RTL) and Left-to-Right (LTR) alignment conflicts.

💡 The Solution: Built for the "خانواده من" platform, this OCR engine bypasses heavy Deep Learning layout models in favor of a specialized Zonal Anchor Algorithm. It guarantees perfect mapping of clinical results to their complex multi-line reference ranges, operating at blazing speeds on resource-constrained local servers.

✨ Key Features

⚬ ⚓ Zonal Anchor Extraction: Mathematically anchors to test names (e.g., "Cholesterol") and casts an isolated horizontal extraction zone. This perfectly absorbs multi-line reference brackets (like <200 or 200-239) without spilling into adjacent rows or swapping columns.
⚬ ⚡ Hardware-Accelerated CPU Inference: Built on a strictly pinned version of PaddleOCR (v2.7.3), forcing C++ hardware acceleration via Intel MKLDNN. Drops inference time from ~15 seconds to under 2 seconds per page on a standard 2-core CPU.
⚬ 🗂️ Unified Multi-Format Routing:
  ⚬ Images (JPG/PNG): Employs OpenCV-based dynamic downscaling (max 1600px width) for massive smartphone photos to prevent Out-Of-Memory (OOM) server crashes.
  ⚬ PDFs: Dynamically renders multi-page reports into processable frames via pdfplumber.
  ⚬ DOCX: Bypasses OCR entirely for flawless native text extraction via python-docx.
⚬ 🧠 Automated Clinical Triage: Parses extracted reference ranges (handling <, >, - operators) to instantly classify patient metrics as Normal ✅, HIGH 🔺, or LOW 🔻, creating a reliable data foundation for drug interaction engines.
⚬ 🎨 Interactive "دکتر هوشا" UI: Features a responsive Gradio web interface helmed by the platform's digital assistant, allowing users to verify tabulated health records before database commitment.

⚙️ Installation & Setup

Critical Note on Dependencies: To prevent silent backend crashes on local servers, this system must be installed with strictly pinned versions of numpy and paddleocr. PaddlePaddle's C++ core is incompatible with NumPy 2.x, and newer versions of PaddleOCR remove necessary CPU optimization flags.

Prerequisites

⚬ Python 3.8 - 3.11
⚬ A local server or machine (optimized for 2-core CPU deployments)

1. Install Core Dependencies

Run the following command to enforce NumPy 1.x compatibility and install the stable vision engine:

pip install "numpy<2.0" paddlepaddle "paddleocr==2.7.3" rapidfuzz gradio pandas opencv-python-headless pdfplumber python-docx


2. Prevent Pillow Conflicts

pip install "pillow<12.0" --no-deps


🚀 Usage

Save the main pipeline script as app.py and run it via your terminal:

python app.py


This will initialize the PaddleOCR engine, load the clinical dictionary, and launch the Gradio web interface on 0.0.0.0:7860.

Using the Interface

1. Navigate to the local URL provided in the terminal.
2. Upload a laboratory report (Image, PDF, or Word Document).
3. Click "🔍 پردازش هوشمند توسط دکتر هوشا".
4. The system will output a verified Pandas DataFrame with the extracted Test Name, Result, Reference Range, and Clinical Status.

🧬 How the Zonal Anchor Algorithm Works

Standard Y-axis coordinate sorting shatters when encountering medical reports due to overlapping vertical text blocks. Our algorithm operates differently:

1. Left-Margin Anchoring: Scans the left 45% of the page using rapidfuzz to identify canonical medical tests from our database.
2. Dynamic Y-Slicing: Calculates the exact pixel distance between the current test and the next test below it to create a strict vertical boundary.
3. Left-to-Right Binning: Scans the created "zone" left-to-right. The first valid number found is locked as the Result. Every text block found to the right of that result is concatenated into the Reference Range string.

📋 Canonical Database

The engine currently supports high-accuracy mapping for over 30 critical blood and urine metrics, including:

⚬ Complete Blood Count (CBC, WBC, RBC, Hb, PLT)
⚬ Basic Metabolic Panel (FBS, Urea, BUN, Creatinine, Uric Acid)
⚬ Lipid Profile (Cholesterol, Triglycerides, HDL, LDL)
⚬ Liver & Thyroid Panels (AST, ALT, ALP, Bilirubin, TSH, T3, T4)
⚬ Vitamins & Minerals (Calcium, Phosphorus, Iron, Ferritin, Vit B12, Vit D)

🤝 Contributing

Contributions are welcome, particularly in expanding the canonical KNOWN_TESTS dictionary and refining the rapidfuzz scoring thresholds for complex acronyms.
