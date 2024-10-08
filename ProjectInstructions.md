# Comprehensive Design Instructions and Project Structure for AFWI MAGE FineTune

---

## **Introduction**

This document provides a fully revised and comprehensive set of design instructions and project structure for the **AFWI MAGE FineTune** application. It incorporates all conclusions and final recommendations to ensure the application functions entirely offline in an air-gapped environment. The goal is to enable your AI-enabled IDE to understand the project's objectives, components, and interactions, facilitating a smooth development process.

---

## **1. Project Overview**

**Project Name**: AFWI MAGE FineTune

**Description**: A containerized, web-based application designed to:

- **Create Fine-Tuning Datasets**: Users can upload documents to extract content for fine-tuning datasets.
- **Review and Edit Extracted Content**: Users can review and modify extracted data within the application.
- **Fine-Tune Local Language Models (LLMs)**: Users can fine-tune pre-downloaded LLMs using the created datasets.
- **Interact with Fine-Tuned Models**: Users can chat with the fine-tuned models through a chat interface.

**Key Requirements**:

- **Air-Gapped Compatibility**: Must function entirely offline without internet access during runtime or installation.
- **Containerization**: Utilize Docker to package the application and all dependencies.
- **User-Friendly Interface**: Provide an intuitive UI for seamless user interaction. 
- **Robust Error Handling**: Implement comprehensive error handling and logging mechanisms.

---

## **2. High-Level Architecture**

The application consists of two main components:

- **Frontend**: A web-based user interface built with **React.js**, utilizing **Material-UI (MUI)** for consistent styling and components.
- **Backend**: A server-side application built with **FastAPI**, handling API requests, data processing, model training, and inference.

**Data Flow**:

1. **Document Upload**: Users upload documents via the frontend, which are sent to the backend for processing.
2. **Content Extraction**: The backend extracts content and generates datasets.
3. **Review and Edit**: The frontend displays the extracted content for user review and editing.
4. **Fine-Tuning**: Users initiate fine-tuning of LLMs through the frontend; the backend handles the process.
5. **Model Interaction**: Users interact with fine-tuned models via a chat interface.

---

## **3. Project Structure**

```
AFWI_MAGE_FineTune/                # Root directory of the project
├── README.md                      # Project overview, setup instructions, and usage information
├── Dockerfile                     # Docker configuration for building the backend container
├── docker-compose.yml             # Docker Compose file to manage multi-container deployment
├── requirements.txt               # List of Python dependencies for the backend
├── .gitignore                     # Specifies intentionally untracked files to ignore by Git
├── backend/                       # Backend application directory
│   ├── __init__.py                # Marks the backend directory as a Python package
│   ├── app.py                     # Main entry point for the FastAPI backend application
│   ├── config.py                  # Configuration settings and environment variables for the backend
│   ├── models/                    # Directory for storing machine learning models
│   │   ├── base_models/           # Pre-downloaded base models for fine-tuning (stored locally)
│   │   └── fine_tuned_models/     # Fine-tuned models generated by the application
│   ├── routes/                    # API route definitions for different application features
│   │   ├── __init__.py            # Initializes the routes module
│   │   ├── upload_routes.py       # Endpoints for handling document uploads
│   │   ├── extraction_routes.py   # Endpoints for initiating content extraction from documents
│   │   ├── review_routes.py       # Endpoints for data retrieval and updates during review
│   │   ├── question_gen_routes.py # Endpoints for generating questions from answers
│   │   ├── fine_tune_routes.py    # Endpoints for starting and monitoring fine-tuning processes
│   │   └── chat_routes.py         # Endpoints for chat interactions with fine-tuned models
│   ├── services/                  # Core business logic and processing modules
│   │   ├── __init__.py            # Initializes the services module
│   │   ├── extractor.py           # Logic for extracting text from uploaded documents
│   │   ├── question_gen.py        # Logic for generating hypothetical questions using LLMs
│   │   ├── fine_tuner.py          # Logic for fine-tuning LLMs with the prepared datasets
│   │   ├── chat_service.py        # Logic for generating responses in the chat interface
│   │   └── resource_manager.py    # Manages system resources during intensive operations
│   └── utils/                     # Utility modules and helper functions
│       ├── __init__.py            # Marks the utils directory as a Python package
│       ├── validators.py          # Input validation and data integrity checks
│       ├── logger.py              # Configures logging for the backend application
│       └── config.py              # Additional configuration utilities
├── frontend/                      # Frontend application directory (React.js)
│   ├── public/                    # Static public assets accessible by the browser
│   │   ├── index.html             # Main HTML file that loads the React app
│   │   ├── favicon.ico            # Favicon for the application
│   │   └── manifest.json          # Web app manifest file
│   ├── src/                       # Source code for the React application
│   │   ├── index.js               # Entry point for the React application
│   │   ├── App.js                 # Root component that defines the application's routes and layout
│   │   ├── components/            # Reusable React components for different features
│   │   │   ├── UploadComponent.js     # Component for uploading documents and initiating extraction
│   │   │   ├── ReviewComponent.js     # Component for reviewing and editing extracted content
│   │   │   ├── FineTuneComponent.js   # Component for configuring and starting fine-tuning
│   │   │   └── ChatComponent.js       # Component for interacting with the fine-tuned models via chat
│   │   ├── services/              # Modules for handling API communications
│   │   │   └── api.js             # Functions for making API calls to the backend endpoints
│   │   ├── utils/                 # Utility functions and helpers for the frontend
│   │   │   └── errorHandler.js    # Centralized error handling for frontend operations
│   │   └── styles/                # CSS stylesheets for styling React components
│   │       └── App.css            # Main stylesheet for the application
│   ├── package.json               # Lists npm dependencies and scripts for the frontend
│   └── .babelrc                   # Babel configuration for JavaScript transpilation
├── data/                          # Directory for storing user data and application outputs
│   ├── uploads/                   # Stores uploaded documents from users
│   ├── datasets/                  # Stores generated CSV datasets for fine-tuning
│   ├── outputs/                   # Stores outputs such as logs and model checkpoints
│   └── logs/                      # Log files generated by the application for debugging
├── tests/                         # Automated tests to ensure code quality and functionality
│   ├── backend/                   # Tests for backend components and services
│   │   ├── test_extractor.py          # Unit tests for extractor.py
│   │   ├── test_fine_tuner.py         # Unit tests for fine_tuner.py
│   │   └── test_chat_service.py       # Unit tests for chat_service.py
│   └── frontend/                  # Tests for frontend components and utilities
│       ├── test_components.js         # Unit and integration tests for React components
│       └── test_api.js                # Tests for API communication and error handling
└── scripts/                       # Auxiliary scripts for setup and maintenance
    └── setup_env.sh               # Script to set up the environment and install dependencies
```

---

## **4. Detailed Component Design and Interactions**

### **4.1 Frontend Components**

#### **4.1.1 UploadComponent.js**

- **Purpose**: Allow users to upload or drag-and-drop documents.
- **Features**: 
  - **File Validation**: Checks file types (PDF, DOCX, TXT) and sizes before upload.
  - **Drag-and-Drop**: Implements intuitive drag-and-drop functionality using `react-dropzone`.
  - **File List Display**: Shows uploaded files with options to preview or remove.
- **Interactions**:
  - **API Calls**: Communicates with `upload_routes.py` to send files.
- **Dependencies**:
  - React.js
  - Material-UI components
  - `react-dropzone`

**Considerations**:

- **Offline Assets**: Ensure all scripts and styles are bundled locally.
- **Error Handling**: Provide user-friendly messages for upload failures.

#### **4.1.2 ReviewComponent.js**

- **Purpose**: Enable users to review and edit extracted content.
- **Features**:
  - **Data Grid Display**: Uses Material-UI DataGrid for displaying content. 
  - **Inline Editing**: Allows direct editing of cells.
  - **Validation**: Implements client-side validation using `Yup`.
  - **Bulk Actions**: Supports adding/removing rows and bulk edits.
- **Interactions**:
  - **API Calls**: Fetches and saves data via `review_routes.py`.  
- **Dependencies**:
  - Material-UI DataGrid
  - `Yup` for validation
  - `Formik` for form management

**Considerations**:

- **Performance**: Use pagination and virtualization for large datasets.
- **Error Handling**: Validate inputs to prevent invalid data submission.

#### **4.1.3 FineTuneComponent.js**

- **Purpose**: Allow users to fine-tune LLMs.
- **Features**:
  - **Model Selection**: Lists available base models from `models/base_models/`.
  - **Parameter Configuration**: Lets users adjust fine-tuning parameters with explanations.
  - **Progress Monitoring**: Displays real-time training progress and logs.
- **Interactions**: 
  - **API Calls**: Initiates fine-tuning via `fine_tune_routes.py`.
- **Dependencies**:
  - React.js
  - Material-UI components

**Considerations**:

- **Resource Checks**: Inform users of resource requirements and availability.  
- **Error Handling**: Provide clear feedback on parameter validation.

#### **4.1.4 ChatComponent.js**

- **Purpose**: Facilitate interaction with fine-tuned models.
- **Features**:
  - **Conversation Interface**: Displays message history and supports multi-turn conversations.
  - **Model Selection**: Allows switching between different fine-tuned models.
  - **Input Sanitization**: Ensures user inputs are safe and appropriate.
- **Interactions**:
  - **API Calls**: Sends and receives messages via `chat_routes.py`.
- **Dependencies**:
  - React.js
  - Material-UI components

**Considerations**:

- **Timeout Handling**: Manage delayed responses gracefully.
- **Security**: Sanitize inputs to prevent injection attacks.

### **4.2 Backend Components**

#### **4.2.1 app.py**

- **Purpose**: Initialize the FastAPI application.
- **Features**:
  - **Route Registration**: Includes all API endpoints.
  - **Middleware**: Implements logging and error handling.
- **Dependencies**:
  - FastAPI
  - Uvicorn

**Considerations**:

- **CORS Configuration**: Adjust settings as needed for frontend-backend communication.
- **Offline Mode**: Ensure no external calls are made during initialization.

#### **4.2.2 config.py**

- **Purpose**: Store configuration settings.
- **Features**:
  - **Environment Variables**: Set variables like `TRANSFORMERS_OFFLINE=1`.
  - **Paths**: Define directories for uploads, datasets, models, and logs.

**Considerations**:

- **Security**: Keep sensitive configurations secure.
- **Documentation**: Clearly document all settings for maintainability.

#### **4.2.3 Routes**

Each route module handles specific API endpoints.

- **upload_routes.py**: Manages file uploads.
- **extraction_routes.py**: Initiates content extraction. 
- **review_routes.py**: Handles data retrieval and updates for review.
- **question_gen_routes.py**: Starts the question generation process.
- **fine_tune_routes.py**: Manages fine-tuning requests.
- **chat_routes.py**: Handles chat interactions.

**Common Features**:

- **Error Handling**: Return meaningful HTTP status codes and messages. 
- **Data Validation**: Use `validators.py` to validate requests.

**Considerations**:

- **Offline Operation**: Ensure no external API calls are made.
- **Security**: Sanitize and validate all incoming data.

#### **4.2.4 Services**

Core business logic for each feature.

##### **extractor.py**

- **Purpose**: Extract text from documents.
- **Features**:
  - **Format Support**: Handles PDF (with OCR if needed), DOCX, and TXT.
  - **OCR Integration**: Uses Tesseract OCR for scanned PDFs.  
- **Dependencies**:
  - `pdfplumber`, `pytesseract`, `python-docx`, `textract`
  - Tesseract OCR engine and language data (included in the container)

**Considerations**:

- **Error Handling**: Log and skip unprocessable sections.
- **Performance**: Process large documents efficiently.

##### **question_gen.py**

- **Purpose**: Generate questions from extracted answers.
- **Features**:  
  - **Local Model Usage**: Uses pre-downloaded models.
  - **Offline Mode**: Sets `TRANSFORMERS_OFFLINE=1`.
- **Dependencies**:
  - Hugging Face Transformers (included models)

**Considerations**:

- **Resource Management**: Optimize batch processing.
- **Error Handling**: Handle model loading failures gracefully.

##### **fine_tuner.py**

- **Purpose**: Fine-tune LLMs with user datasets.  
- **Features**:
  - **Parameter-Efficient Techniques**: Implements PEFT or LoRA.
  - **Checkpointing**: Allows resuming interrupted training.
- **Dependencies**:  
  - PyTorch
  - PEFT (included in the container)

**Considerations**:

- **Resource Checks**: Verify system capabilities before starting.
- **Error Handling**: Capture and report training exceptions.

##### **chat_service.py**

- **Purpose**: Generate responses in the chat interface.
- **Features**:
  - **Model Loading**: Loads fine-tuned models from local storage.
  - **Inference**: Generates responses efficiently.
- **Dependencies**:
  - Same as `fine_tuner.py`

**Considerations**:

- **Security**: Implement output filters for inappropriate content.
- **Performance**: Ensure quick response times.

##### **resource_manager.py**

- **Purpose**: Monitor and manage system resources.
- **Features**:
  - **Resource Monitoring**: Tracks CPU, memory, and GPU usage. 
  - **Process Management**: Adjusts workloads to prevent overloads.
- **Dependencies**:
  - `psutil`  

**Considerations**:

- **User Notifications**: Inform users of resource constraints.
- **Scalability**: Handle multiple simultaneous tasks gracefully.

#### **4.2.5 utils/**

Common utilities for the backend.

##### **validators.py**

- **Purpose**: Validate inputs and data integrity.
- **Features**:
  - **File Validation**: Check file types and sizes.
  - **Data Validation**: Ensure datasets are correctly formatted.

**Considerations**:

- **Security**: Prevent malicious data from entering the system.
- **Maintainability**: Keep validation rules updated.

##### **logger.py**

- **Purpose**: Centralized logging configuration.
- **Features**:  
  - **Log Levels**: DEBUG, INFO, WARNING, ERROR, CRITICAL.
  - **Log Handlers**: Write logs to files in `data/logs/`.

**Considerations**:

- **Privacy**: Exclude sensitive information from logs.
- **Log Rotation**: Implement log rotation and archival policies.

---

## **5. Libraries and Dependencies**

All libraries and dependencies must be included within the container and configured to operate offline.

### **5.1 Backend Dependencies**

- **FastAPI**: Web framework for API development.
- **Uvicorn**: ASGI server for FastAPI.
- **Hugging Face Transformers**: 
  - **Usage**: Model loading, fine-tuning, and inference.
  - **Configuration**: Set `TRANSFORMERS_OFFLINE=1`.
  - **Models**: Pre-download models and tokenizers; include in `models/base_models/`.
- **PyTorch**: Deep learning framework.
- **PEFT**: Parameter-efficient fine-tuning.
  - **Installation**: Include in the container.
- **Tesseract OCR**:
  - **Installation**: Install the Tesseract engine and language data in the container.
- **pdfplumber**, **pytesseract**, **python-docx**, **textract**: For document parsing.
- **psutil**: For resource monitoring.
- **spaCy**: 
  - **Installation**: Include language models (e.g., `en_core_web_sm`) in the container.
  - **Configuration**: Install models from local files.
- **Celery** and **Redis** (optional for asynchronous tasks):
  - **Configuration**: Operate entirely within the container network.

### **5.2 Frontend Dependencies**

- **React.js**: Frontend library.
- **Material-UI (MUI)**: UI components.
- **MUI DataGrid**: Data grid component.
- **react-dropzone**: For file upload interface.
- **Formik** and **Yup**: For form handling and validation.
- **Webpack**: Module bundler.

**Considerations**:

- **Local Assets**: Ensure all JavaScript, CSS, fonts, and images are bundled locally.
- **No External Calls**: Audit the frontend build to remove any external CDN references.

---

## **6. Deployment and Air-Gapped Environment Considerations**

### **6.1 Containerization**

- **Docker**:
  - **Purpose**: Package the application with all dependencies.
  - **Dockerfile**: Build the image to include all required libraries, models, and data.
- **Docker Compose**:
  - **Usage**: Define services, such as the web server and message broker.

### **6.2 Air-Gapped Deployment**

- **Include All Dependencies**: 
  - Ensure no components attempt to download resources at runtime.
- **Testing**:
  - Simulate an air-gapped environment during testing to catch any external calls.
- **Documentation**: 
  - Provide detailed setup instructions for deploying in an air-gapped environment.

### **6.3 Configuration Adjustments**
* **Environment Variables**:
   * Set variables like `TRANSFORMERS_OFFLINE=1` and `HF_DATASETS_OFFLINE=1`.
* **Library Configurations**:
   * Disable telemetry or auto-update features in all libraries.
* **Pre-Download Resources**:
   * Models, datasets, language packs, and other resources must be included.

## **7. Error Handling and Logging**

### **7.1 Error Handling**
* **Frontend**:
   * **User Feedback**: Display clear error messages without technical jargon.
   * **Validation**: Prevent invalid data submission.
* **Backend**:
   * **Exception Handling**: Catch and log exceptions; return appropriate HTTP responses.
   * **Data Validation**: Use `validators.py` to ensure data integrity.

### **7.2 Logging**
* **Centralized Logging**:
   * Use `logger.py` to configure logging across the application.
* **Log Management**:
   * Store logs in `data/logs/`.
   * Implement log rotation and archival policies.

## **8. Security Considerations**
* **Input Sanitization**:
   * Sanitize all user inputs to prevent injection attacks.
* **Output Filtering**:
   * Ensure models do not produce inappropriate content.
* **Dependency Auditing**:
   * Verify the security of all included libraries.
* **Compliance**:
   * Adhere to organizational policies for operating in secure environments.

## **9. Testing and Quality Assurance**
* **Unit Tests**:
   * Write tests for all critical components in the `tests/` directory.
* **Integration Tests**:
   * Test interactions between components.
* **Air-Gapped Simulation**:
   * Test the application without internet access to ensure full functionality.
* **Static Code Analysis**:
   * Use tools like **Bandit** and **ESLint** to detect potential security issues.

## **10. Documentation**
* **User Guide**:
   * Provide instructions on how to use each feature of the application.
* **Setup Guide**:
   * Detail the steps for installing and deploying the application in an air-gapped environment.
* **Developer Guide**:
   * Document code structure, design decisions, and contribution guidelines.
* **API Documentation**:
   * Use tools like **Swagger** to document API endpoints.

## **11. Conclusion**
By integrating all the conclusions and recommendations, this comprehensive design ensures that the **AFWI MAGE FineTune** application:
* Functions entirely offline in air-gapped environments.
* Provides a user-friendly interface with robust error handling.
* Is secure and complies with organizational policies. 
* Is well-documented to aid both users and developers.

### **Next Steps**:
* **Development**: Proceed with implementing the components as described.
* **Testing**: Rigorously test each component, especially in offline conditions.
* **Deployment**: Prepare the application for deployment in the target environment.

## **Appendix: Key Points for AI-Enabled IDE**
* **Offline Operation**: Ensure all code and dependencies do not require internet access at any point.
* **Modular Design**: Components are organized logically to facilitate understanding and navigation.
* **Explicit Dependencies**: All libraries are specified, and their versions are pinned in `requirements.txt`.
* **Configuration Files**: Use `config.py` and environment variables for settings, emphasizing offline modes.
* **Documentation**: Include docstrings and comments explaining the purpose and functionality of modules and functions.
* **Error Handling**: Implement try-except blocks and input validation to catch and handle errors gracefully.
* **Security Practices**: Sanitize inputs, avoid hard-coded secrets, and follow best practices.
* **Testing**: Provide tests for each component to ensure reliability and aid in debugging.