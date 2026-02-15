# System Design Document

## 1. System Architecture

The MediLedger system follows a microservices-based architecture to ensure scalability, maintainability, and separation of concerns.

```mermaid
graph TD
    User[User / Browser] -->|HTTPS| Frontend[Frontend (React + Vite)]
    Frontend -->|REST API| Backend[Backend API (Node.js + Express)]
    
    subgraph Data Layer
        Backend -->|Read/Write| DB[(MongoDB)]
        Backend -->|Store Files| Storage[Local/Cloud Storage]
    end
    
    subgraph Services
        Backend -->|HTTP POST| OCR[OCR Service (Python + FastAPI)]
        OCR -->|External API| Groq[Groq LLM API]
        Backend -->|JSON-RPC| Blockchain[Blockchain Service (Ethereum/Sepolia)]
    end
    
    subgraph Blockchain Layer
        Blockchain -->|Smart Contract| Ethereum[(Ethereum Network)]
    end
```

### Components
- **Frontend**: A React-based Single Page Application (SPA) for user interaction.
- **Backend**: A Node.js/Express server acting as the central orchestrator.
- **OCR Service**: A Python/FastAPI service dedicated to image processing and text extraction.
- **Blockchain Service**: A module for interacting with the Ethereum blockchain via Ethers.js.

---

## 2. Database Design (MongoDB)

### 2.1 User Collection (`users`)
Stores user authentication and profile information.

| Field | Type | Description |
| :--- | :--- | :--- |
| `_id` | ObjectId | Unique identifier |
| `name` | String | User's full name |
| `email` | String | Unique email address (Indexed) |
| `password` | String | Bcrypt hashed password |
| `role` | String | 'user', 'doctor', or 'admin' |
| `isActive` | Boolean | Account status |
| `createdAt` | Date | Registration timestamp |

### 2.2 Document Collection (`documents`)
Stores metadata of uploaded medical records.

| Field | Type | Description |
| :--- | :--- | :--- |
| `_id` | ObjectId | Unique identifier |
| `user` | ObjectId | Reference to `User` |
| `title` | String | Document title |
| `documentType` | String | e.g., 'prescription', 'lab-report' |
| `fileName` | String | Original file name |
| `filePath` | String | Path to stored file |
| `ocrText` | String | Raw text extracted by generic OCR |
| `patientName` | String | Extracted by LLM |
| `patientAge` | String | Extracted by LLM |
| `diagnosis` | String | Extracted by LLM |
| `medicines` | Array | List of extracted medicines |
| `blockchainHash` | String | SHA-256 hash stored on-chain |
| `blockchainTxHash` | String | Transaction hash of the registration |
| `blockchainVerified`| Boolean | Verification status |

---

## 3. Smart Contract Design

### Contract: `MedicalRecords.sol`
Deployed on Ethereum Sepolia Testnet.

#### Structs
```solidity
struct DocumentRecord {
    bytes32 documentHash; // SHA-256 hash of the document content
    address owner;        // Address of the user who registered it
    uint256 timestamp;    // Block timestamp
    bool exists;          // Flag to check existence
}
```

#### Key Functions
1.  **`registerDocument(bytes32 _documentHash)`**:
    -   Stores the document hash mapped to the sender's address.
    -   Emits `DocumentRegistered` event.
    
2.  **`verifyDocument(bytes32 _documentHash)`**:
    -   Returns `(bool exists, address owner, uint256 timestamp)` given a hash.
    -   Used to validate if a document has been tampered with.

3.  **`getUserDocuments(address _owner)`**:
    -   Returns an array of all document hashes registered by a specific address.

---

## 4. API Design (High-Level)

### Auth Routes
- `POST /api/auth/register`: Create new user.
- `POST /api/auth/login`: Authenticate and receive JWT.
- `GET /api/auth/me`: Get current user profile.

### Document Routes
- `POST /api/documents`: Upload file (multipart/form-data). Triggers OCR and Blockchain registration.
- `GET /api/documents`: Fetch all documents for the logged-in user.
- `GET /api/documents/:id`: Get specific document details.
- `POST /api/documents/verify/:id`: Manually trigger verification with blockchain.

---

## 5. Security Architecture

1.  **Data Privacy**:
    -   Actual medical files are **NEVER** stored on the blockchain.
    -   Only a purely mathematical hash (SHA-256) is stored on-chain.
    -   This ensures patient privacy (GDPR/HIPAA compliance consideration).

2.  **Authentication**:
    -   JWT (JSON Web Tokens) for stateless authentication.
    -   Passwords hashed using `bcryptjs` with salt rounds.

3.  **Integrity**:
    -   Any change to the file content will result in a different SHA-256 hash.
    -   The system compares current file hash vs. stored blockchain hash to detect tampering.
