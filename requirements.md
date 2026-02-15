# Project Requirements Specification

## 1. Project Overview
**MediLedger** is a secure, AI-powered medical history management system. It digitizes physical medical documents using OCR, structures the data using LLMs, and verifies document integrity using Blockchain technology.

---

## 2. Functional Requirements

### 2.1 User Authentication
*   **Registration/Login**: Users must be able to create an account and log in securely.
*   **Authentication**: The system must use JWT (JSON Web Tokens) for secure API access.

### 2.2 Document Management
*   **Upload**: Users can upload medical documents (images/PDFs) via the web interface.
*   **Storage**: Documents are stored securely (local filesystem or Cloudinary).
*   **View**: Users can view their uploaded documents in a timeline format.

### 2.3 Optical Character Recognition (OCR) & AI Analysis
*   **Text Extraction**: The system must use **PaddleOCR** to extract text from uploaded images.
*   **Data Structuring**: The system must use **Groq (Llama 3)** to parse raw text into structured JSON (Patient Name, Diagnosis, Medicines, etc.).
*   **Editability**: Users should be able to review and simple-edit extracted data before saving.

### 2.4 Blockchain Verification
*   **Hashing**: The system must generate a SHA-256 hash for every uploaded document.
*   **Registration**: The simple document hash is stored on the Ethereum (Sepolia) blockchain to ensure immutability.
*   **Verification**: Users can verify the authenticity of a document by checking its hash against the blockchain registry.

---

## 3. Non-Functional Requirements
*   **Security**: Patient data must be encrypted at rest and in transit.
*   **Performance**: OCR processing should complete within reasonable time limits (e.g., < 30s).
*   **Scalability**: The architecture uses microservices (Frontend, Backend, OCR, Blockchain) to allow independent scaling.
*   **Usability**: The frontend should be responsive and user-friendly.

---

## 4. System Requirements

### 4.1 Prerequisites
*   **Node.js**: v18 or higher
*   **Python**: v3.9 or higher
*   **MongoDB**: v6.0+ (Local or Atlas)
*   **Metamask**: Browser extension for blockchain interaction
*   **Git**: For version control

---

## 5. Technical Dependencies

### 5.1 Frontend (`/frontend`)
*   **Core**: React (v18), Vite
*   **Styling**: Tailwind CSS, Autoprefixer, PostCSS
*   **Routing**: React Router DOM (v6)
*   **State/API**: Axios, React Query (optional), React Hot Toast
*   **Web3**: Ethers.js (v6), Web3.js
*   **UI Components**: Lucide React, Framer Motion, React Dropzone

### 5.2 Backend (`/backend`)
*   **Runtime**: Node.js, Express
*   **Database**: Mongoose (MongoDB ODM)
*   **Authentication**: JSONWebToken (JWT), BcryptJS
*   **File Handling**: Multer, Multer-Storage-Cloudinary, Sharp
*   **Blockchain**: Ethers.js, Web3.js
*   **Utilities**: Dotenv, Cors, Helmet, Morgan, Express-Validator, Compression

### 5.3 OCR Service (`/ocr-service`)
*   **Framework**: FastAPI, Uvicorn
*   **AI/ML**: PaddlePaddle, PaddleOCR, Numpy, OpenCV-Python
*   **LLM Integration**: Groq SDK
*   **Image Processing**: Pillow (PIL)
*   **Utilities**: Python-Multipart, Python-Dotenv, HTTPX

### 5.4 Blockchain Service (`/blockchain-service`)
*   **Runtime**: Node.js
*   **Interaction**: Ethers.js (v6), Web3.js
*   **Utilities**: Dotenv, Crypto, Axios
*   **Smart Contracts**: Solidity (deployed via scripts)

---

## 6. Environment Configuration (.env)
Each service requires specific environment variables as detailed in their respective directories or `QUICK_START.md`.
