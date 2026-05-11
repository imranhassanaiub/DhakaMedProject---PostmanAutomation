[README_DhakaMed.md](https://github.com/user-attachments/files/27587833/README_DhakaMed.md)
# 🏥 DhakaMed — Full API Test Automation Suite

![Postman](https://img.shields.io/badge/Postman-FF6C37?logo=postman&logoColor=white)
![Newman](https://img.shields.io/badge/CLI-Newman-43853D?logo=npm&logoColor=white)
![Domain](https://img.shields.io/badge/Domain-MedTech-blue)
![Coverage](https://img.shields.io/badge/Coverage-End--to--End-brightgreen)
![Collection](https://img.shields.io/badge/Collection-1.4MB%20%7C%2037K%2B%20lines-orange)
![License](https://img.shields.io/badge/License-MIT-lightgrey)

A **complete end-to-end Postman API automation suite** for **DhakaMed**, a MedTech domain web application. This project covers the full application API surface — from authentication through all core business modules — using dynamic environment variables, automated test assertions, and response chaining across request flows.

> *"This is a full automation project for DhakaMed, a MedTech domained application. The automation project is done through Postman and it covers the whole application API and a complete end-to-end API testing suite."*

---

## 📋 Table of Contents

- [About the Project](#-about-the-project)
- [Project Scope](#-project-scope)
- [Project Structure](#-project-structure)
- [Environment Variables](#-environment-variables)
- [Key Automation Patterns](#-key-automation-patterns)
- [Getting Started](#-getting-started)
  - [Run in Postman](#run-in-postman)
  - [Run via Newman (CLI)](#run-via-newman-cli)
  - [Generate HTML Report](#generate-html-report)
- [CI/CD Integration](#-cicd-integration)
- [Contributing](#-contributing)
- [Author](#-author)

---

## 📖 About the Project

DhakaMed is a MedTech application, and this repository contains its **complete Postman-based API automation project**. Unlike a partial or feature-specific collection, this suite is designed to test the **entire application API** in a single, structured, runnable collection.

Key characteristics of this project:

- **Full coverage** — the collection spans 37,000+ lines (1.4 MB), covering every application module end-to-end
- **Rich environment** — a 2.97 MB environment file manages all variables, test data, and dynamic values including any binary payloads (e.g., file upload scenarios)
- **Dynamic variable chaining** — IDs, tokens, and state values created in earlier requests are automatically injected into later ones via `pm.environment.set()`
- **Automated assertions** — every request includes Postman test scripts validating status codes, response schema, and business logic
- **Reusable & portable** — the collection and environment can be imported into any Postman workspace or executed headlessly via Newman

---

## 🎯 Project Scope

As a full-coverage API test suite for a MedTech application, this collection is structured around the core functional modules of the DhakaMed platform. Typical coverage for an application of this type includes:

| Module                  | HTTP Methods Used         | Description                                        |
|-------------------------|---------------------------|----------------------------------------------------|
| **Authentication**      | `POST`                    | User login, token generation, session management  |
| **User Management**     | `GET`, `POST`, `PUT`, `DELETE` | User registration, profile, roles            |
| **Patient Management**  | `GET`, `POST`, `PUT`, `DELETE` | Patient records, profiles, medical history   |
| **Doctor / Provider**   | `GET`, `POST`, `PUT`, `DELETE` | Doctor profiles, specialisations, availability |
| **Appointments**        | `GET`, `POST`, `PUT`, `DELETE` | Booking, rescheduling, cancellation          |
| **Medical Records**     | `GET`, `POST`             | Diagnosis, prescriptions, reports                 |
| **File Uploads**        | `POST` (multipart)        | Document and image upload endpoints               |
| **Admin / Config**      | `GET`, `PUT`              | System settings, lookup data                      |

> The collection follows a logical E2E execution order: authentication → entity creation → operations → validation → cleanup.

---

## 📁 Project Structure

```
DhakaMedProject---PostmanAutomation/
├── DhakaMed.postman_collection.json    # Full API collection — 37,807 lines / 1.4 MB
│                                       # Contains all requests, folder structure,
│                                       # pre-request scripts, and test assertions
│
├── DhakaMED.postman_environment.json   # Environment configuration — 2.97 MB
│                                       # Base URL, credentials, dynamic IDs,
│                                       # test data, and file upload payloads
│
└── README.md
```

### Why are the files so large?

The **collection** (1.4 MB) is large because it contains the full application coverage — not just a few endpoints, but every module, positive and negative scenarios, and embedded test scripts for each request.

The **environment** (2.97 MB) is large because it stores not just config variables but also complex test data — potentially including base64-encoded file payloads used for document/image upload test cases, which is standard practice for Postman multipart upload testing.

---

## 🔧 Environment Variables

The `DhakaMED.postman_environment.json` file contains all variables that drive the collection. While the full variable list is extensive, the key categories are:

| Category           | Examples                                              |
|--------------------|-------------------------------------------------------|
| **Base Config**    | `baseUrl`, `apiVersion`                               |
| **Auth**           | `username`, `password`, `token`, `refreshToken`       |
| **Dynamic IDs**    | `patientId`, `doctorId`, `appointmentId`, `userId`    |
| **Test Data**      | Request body values set dynamically during test runs  |
| **File Payloads**  | Base64-encoded binary data for upload test scenarios  |

> ⚠️ **Security Note:** Never commit production credentials or sensitive patient data to version control. Ensure environment files used in real environments are excluded via `.gitignore` and managed through a secrets manager or Postman Vault.

---

## ⚙️ Key Automation Patterns

### 1. Auth Token Chaining
The login request captures the token from the response and sets it as an environment variable — automatically authorising all subsequent requests:

```javascript
// Example test script on login request
var jsonData = pm.response.json();
pm.environment.set("token", jsonData.access_token);
pm.test("Login successful", function () {
    pm.response.to.have.status(200);
});
```

### 2. Dynamic ID Propagation
After a resource is created (e.g., a patient), the new record's ID is extracted and saved — so downstream requests (GET, PUT, DELETE) reference the correct resource without hardcoding:

```javascript
// Example: save created resource ID
var jsonData = pm.response.json();
pm.environment.set("patientId", jsonData.data.id);
```

### 3. Automated Assertions
Every request includes `pm.test()` assertions to validate:
- HTTP status codes (200, 201, 400, 401, 404, etc.)
- Response body structure and required fields
- Business rule validation (e.g., data matches what was sent)
- Error message correctness for negative test cases

### 4. End-to-End Flow Execution
Requests are ordered within folders to represent real user journeys — e.g., *Create Appointment → Fetch Appointment → Update Appointment → Cancel Appointment* — so the Collection Runner executes them as a complete, stateful workflow.

---

## 🚀 Getting Started

### Prerequisites

- [Postman Desktop App](https://www.postman.com/downloads/) (latest version recommended)
- [Node.js 16+](https://nodejs.org/) — required for Newman CLI
- [Newman](https://www.npmjs.com/package/newman) — for command-line execution

---

### Run in Postman

**Step 1 — Import the files:**

1. Open Postman
2. Click **Import** (top-left)
3. Select and import both files:
   - `DhakaMed.postman_collection.json`
   - `DhakaMED.postman_environment.json`

**Step 2 — Select the environment:**

In the top-right corner dropdown, select **DhakaMED**.

**Step 3 — Run the collection:**

1. In the Collections sidebar, click **▶ Run** next to `DhakaMed`
2. In the Collection Runner, ensure the **DhakaMED** environment is selected
3. Set iteration count (default: 1) and click **Run DhakaMed**

> **Execution order matters.** The collection is designed to run top-to-bottom. Authentication must execute first so the token is available for all other requests.

---

### Run via Newman (CLI)

Newman executes the full collection headlessly — ideal for CI pipelines and scheduled runs.

**Install Newman:**

```bash
npm install -g newman
```

**Run the full suite:**

```bash
newman run DhakaMed.postman_collection.json \
  --environment DhakaMED.postman_environment.json
```

**Run with verbose output:**

```bash
newman run DhakaMed.postman_collection.json \
  --environment DhakaMED.postman_environment.json \
  --verbose
```

**Run a specific folder only:**

```bash
newman run DhakaMed.postman_collection.json \
  --environment DhakaMED.postman_environment.json \
  --folder "Appointments"
```

---

### Generate HTML Report

The `newman-reporter-htmlextra` plugin produces a detailed, interactive HTML report after each run.

**Install the reporter:**

```bash
npm install -g newman-reporter-htmlextra
```

**Run with HTML report:**

```bash
newman run DhakaMed.postman_collection.json \
  --environment DhakaMED.postman_environment.json \
  --reporters cli,htmlextra \
  --reporter-htmlextra-export ./reports/dhakamed-test-report.html \
  --reporter-htmlextra-title "DhakaMed API Test Report"
```

The report will be generated at `./reports/dhakamed-test-report.html` and includes:
- Total requests, pass/fail counts
- Per-request test results and response times
- Response body previews for failed assertions
- Environment variable snapshots

---

## 🔄 CI/CD Integration

The collection can be integrated into a CI/CD pipeline using Newman. Below is a sample **GitHub Actions** workflow:

```yaml
# .github/workflows/api-tests.yml
name: DhakaMed API Tests

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
  schedule:
    - cron: '0 6 * * *'   # Run daily at 6 AM UTC

jobs:
  api-test:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: Install Newman and HTML reporter
        run: |
          npm install -g newman
          npm install -g newman-reporter-htmlextra

      - name: Run API Tests
        run: |
          newman run DhakaMed.postman_collection.json \
            --environment DhakaMED.postman_environment.json \
            --reporters cli,htmlextra \
            --reporter-htmlextra-export ./reports/test-report.html

      - name: Upload Test Report
        uses: actions/upload-artifact@v3
        if: always()
        with:
          name: api-test-report
          path: ./reports/test-report.html
```

> **Note:** For CI environments, store credentials (base URL, username, password) as **GitHub Secrets** and inject them as environment variable overrides using Newman's `--env-var` flag rather than committing them in the environment JSON.

```bash
newman run DhakaMed.postman_collection.json \
  --environment DhakaMED.postman_environment.json \
  --env-var "baseUrl=${{ secrets.API_BASE_URL }}" \
  --env-var "password=${{ secrets.API_PASSWORD }}"
```

---

## 🤝 Contributing

Contributions are welcome. To add new test coverage or update existing requests:

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/add-prescription-tests`
3. Export the updated collection from Postman and replace `DhakaMed.postman_collection.json`
4. Commit with a clear message: `git commit -m "Add: prescription create/update/delete test scenarios"`
5. Push and open a Pull Request

**Contribution guidelines:**
- Every new request must include at least one `pm.test()` assertion
- Use environment variables — never hardcode IDs, tokens, or base URLs
- Follow the existing folder naming convention
- Ensure the full collection still passes after your changes via a Newman dry-run

---

## 👤 Author

**Md. Imran Hassan**
QA Lead | Postman | API Testing | Selenium | Java | Playwright

[![GitHub](https://img.shields.io/badge/GitHub-imranhassanaiub-181717?logo=github)](https://github.com/imranhassanaiub)

---

> 💡 *This project is a real-world demonstration of full-coverage API automation for a MedTech application using Postman best practices — dynamic chaining, environment-driven configuration, automated assertions, and Newman-based CI execution.*
