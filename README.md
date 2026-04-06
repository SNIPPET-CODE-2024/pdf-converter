# PDF Forge

PDF Forge is a full-stack PDF and image tools platform inspired by iLovePDF. It includes merge, split, compress, convert, edit, watermark, sign, resize, compress-image, and enhance-image workflows with a React frontend and Express backend.

## Features

- Merge multiple PDFs
- Split PDFs by page range JSON
- Compress PDFs with Ghostscript
- Convert PDF to Word or Excel using text extraction plus OCR fallback
- Convert PDF pages to JPG with Ghostscript
- Convert JPG or PNG images to PDF
- Resize JPG and PNG images
- Compress JPG and PNG images
- Enhance image brightness, contrast, and sharpness
- Convert Word and Excel files to PDF with LibreOffice
- Edit PDFs by adding basic text and optional image overlays
- Add watermark text to every page
- Sign PDFs with a signature image
- Upload progress, preview cards, and download links
- Auto-delete uploaded and generated files after 10 minutes
- File size limit of 10MB per file
- Unique filenames and access logging

## Project Structure

- `client` - React + Vite + Tailwind frontend
- `server` - Express API and file processing logic
- `uploads` - temporary incoming files
- `outputs` - processed result files

## Required Installations

### 1. Node.js

Use Node.js 18 or newer.

### 2. LibreOffice

Required for:
- Word to PDF
- Excel to PDF

Install LibreOffice and make sure the `soffice` executable is available in your system PATH.

Windows default path is usually similar to:
- `C:\Program Files\LibreOffice\program`

### 3. Ghostscript

Required for:
- Compress PDF
- PDF to JPG

Install Ghostscript and do one of these:
- add Ghostscript `bin` folder to system PATH
- or set `GHOSTSCRIPT_PATH` to the full `gswin64c.exe` path

Windows default path is usually similar to:
- `C:\Program Files\gs\gs10.05.1\bin\gswin64c.exe`

The backend now also tries to auto-detect common Windows Ghostscript install folders.

### 4. Tesseract.js language data

Tesseract.js downloads OCR language data the first time OCR runs. That means the first scanned PDF to Word or Excel request may take longer and needs network access in the runtime environment.

## Setup

### Install all dependencies

```bash
npm run install:all
```

Or install them separately:

```bash
npm install
npm install --prefix server
npm install --prefix client
```

### Configure environment variables

Backend example: [server/.env.example](/D:/New%20folder/convert/server/.env.example)
Frontend example: [client/.env.example](/D:/New%20folder/convert/client/.env.example)

## Run locally

### Start frontend and backend together

```bash
npm run dev
```

### Start backend only

```bash
npm run dev --prefix server
```

### Start frontend only

```bash
npm run dev --prefix client
```

Backend runs on `http://localhost:5000`.
Frontend runs on `http://localhost:5173`.

## API Endpoints

- `POST /merge-pdf`
- `POST /split-pdf`
- `POST /compress-pdf`
- `POST /pdf-to-word`
- `POST /pdf-to-excel`
- `POST /pdf-to-jpg`
- `POST /jpg-to-pdf`
- `POST /resize-image`
- `POST /compress-image`
- `POST /enhance-image`
- `POST /word-to-pdf`
- `POST /excel-to-pdf`
- `POST /edit-pdf`
- `POST /watermark`
- `POST /sign`
- `GET /downloads/:fileName`
- `GET /health`

## Request Notes

### `POST /merge-pdf`
- multipart field: `files`
- accepts multiple PDFs

### `POST /split-pdf`
- multipart field: `file`
- optional text field: `ranges`
- example `ranges`: `[{"start":1,"end":2},{"start":3,"end":4}]`

### `POST /resize-image`
- multipart field: `file`
- text fields: `width`, `height`, `fit`, `quality`

### `POST /compress-image`
- multipart field: `file`
- optional text fields: `quality`, `maxWidth`

### `POST /enhance-image`
- multipart field: `file`
- optional text fields: `brightness`, `contrast`, `sharpen`, `quality`

### `POST /edit-pdf`
- multipart field: `file`
- optional multipart field: `image`
- optional text fields: `text`, `x`, `y`, `fontSize`

### `POST /watermark`
- multipart field: `file`
- optional text field: `text`

### `POST /sign`
- multipart fields: `file`, `signature`

## Deployment

### Frontend on Vercel

- Set `VITE_API_BASE_URL` to your deployed backend URL
- Set `VITE_SITE_URL` to your public frontend domain so canonical tags, route HTML files, robots.txt, and sitemap.xml use the correct absolute URLs
- Build command: `npm run build`
- Output directory: `dist`
- Root directory: `client`

### Backend on Render or Railway

- Root directory: `server`
- Start command: `npm start`
- Add `CLIENT_URL` with your deployed frontend URL
- Ensure LibreOffice and Ghostscript are installed in the runtime image
- If Ghostscript is not on PATH, set `GHOSTSCRIPT_PATH`

## Important Implementation Notes

- `pdf-to-word` and `pdf-to-excel` primarily extract text from PDFs. If no text is embedded, the app falls back to OCR using Tesseract.js.
- `word-to-pdf` and `excel-to-pdf` use LibreOffice conversion.
- `pdf-to-jpg` and `compress-pdf` depend on Ghostscript being installed and available in PATH or via `GHOSTSCRIPT_PATH`.
- `resize-image`, `compress-image`, and `enhance-image` use Jimp and do not require Ghostscript.
- Processed files are exposed through `/downloads/:fileName` and are scheduled for deletion after 10 minutes.
- The frontend uses one generic tool page to keep the experience consistent across all tools.

## Suggested Improvements

- Add ZIP download support for multi-file outputs like split PDF and PDF to JPG
- Add queueing for heavier OCR jobs
- Add real PDF page preview rendering with a dedicated viewer
- Add user accounts and saved history
- Add persistent object storage for production
