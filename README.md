# Metadata Extraction for Rental Agreements

## Overview

This project extracts metadata from rental agreement documents (in `.docx` and `.png` formats) using a BERT-based question-answering model. It processes files in a test folder, extracts key fields like agreement value, start/end dates, renewal notice period, and parties involved, and computes per-field recall scores by comparing predictions against ground truth data.

## Features

- Extracts text from `.docx` files using `python-docx`.
- Extracts text from `.png` files using Tesseract OCR (`pytesseract`).
- Uses a BERT model (`bert-large-uncased-whole-word-masking-finetuned-squad`) for question-answering to extract metadata.
- Processes extracted data to meet specific formatting requirements:
  - Agreement Value: Integer (e.g., 12000)
  - Agreement Start/End Dates: `DD.MM.YYYY` format (e.g., 01.04.2008)
  - Renewal Notice: Number of days (e.g., 30)
  - Party One/Two: Names only (e.g., S.Sakunthala)
- Computes recall scores for each metadata field by comparing predictions with ground truth.

## Prerequisites

- Python 3.8 or higher
- Tesseract OCR installed on your system
  - Windows: Download and install from [Tesseract at UB Mannheim](https://github.com/UB-Mannheim/tesseract/wiki). Ensure the executable path is set to `Tesseract-OCR\tesseract.exe`.
  - Linux: `sudo apt-get install tesseract-ocr`
  - macOS: `brew install tesseract`

## Installation

1. Clone the repository or download the script:
   ```bash
   git clone <repository-url>
   cd <repository-directory>
   ```
2. Create a virtual environment (optional but recommended):
   ```bash
   python -m venv venv
   source venv/bin/activate  # Linux/macOS
   venv\Scripts\activate     # Windows
   ```
3. Install the required Python packages:
   ```bash
   pip install pandas python-docx pytesseract pillow transformers word2number regex
   ```

## File Structure

- `metadata_extraction.py`: Main script for metadata extraction and recall computation.
- `data/test/`: Directory containing test files (`.docx` and `.png` rental agreements).
  - Example files: `95980236-Rental-Agreement.png`, `156155545-Rental-Agreement-Kns-Home.pdf.docx`, `228094620-Rental-Agreement.pdf.docx`
- `data/test.csv`: Ground truth CSV file with expected metadata.
  - Expected columns: `File Name`, `Aggrement Value`, `Aggrement Start Date`, `Aggrement End Date`, `Renewal Notice (Days)`, `Party One`, `Party Two`
  - Example:
    ```
    File Name,Agreement Value,Agreement Start Date,Agreement End Date,Renewal Notice (Days),Party One,Party Two
    95980236-Rental-Agreement.pdf,9000,01.04.2008,31.03.2009,60,S.Sakunthala,V.V.Ravi Kian
    ```
- `predictionspi1.csv`: Output CSV file containing predicted metadata.

## Usage

1. Ensure your test files are placed in the `data/test/` directory and `test.csv` is in the `data/` directory.
2. Run the script:
   ```bash
   python metadata_extraction.py
   ```
3. The script will:
   - Process all files in the `data/test/` directory.
   - Extract metadata using the BERT QA model.
   - Format the extracted metadata as per requirements.
   - Save predictions to `predictionspi1.csv`.
   - Compute and print per-field recall scores.

### Example Output

```
{'File Name': '95980236-Rental-Agreement', 'Aggrement Value': 12000, 'Aggrement Start Date': '01.04.2008', 'Aggrement End Date': '01.04.2010', 'Renewal Notice (Days)': 330, 'Party One': 'S.Sakunthala', 'Party Two': ''}
Per-field Recall Scores:
Aggrement Value: 1.00
Aggrement Start Date: 1.00
Aggrement End Date: 0.50
Renewal Notice (Days): 0.75
Party One: 0.25
Party Two: 0.00
```

## Notes

- The script assumes Tesseract OCR is installed and the path is correctly set.
- The ground truth CSV (`test.csv`) must match the file names in the test folder (excluding extensions for comparison).
- There may be discrepancies in `test.csv` (e.g., `95980236-Rental-Agreement.pdf` has a ground truth value of 9000, but the document states 12000), which can affect recall scores.
- The BERT model may struggle with OCR-extracted text from `.png` files due to noise; consider improving OCR preprocessing for better accuracy.

