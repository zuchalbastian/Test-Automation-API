# Test‑Automation‑API

A robust API test automation framework built with **Katalon Studio**, **Groovy** scripts, and **Gradle** for CI‑friendly executions. Designed for maintainability, reusability, and clear reporting.

---

## Table of Contents

1. [Overview](#overview)
2. [Features](#features)
3. [Prerequisites](#prerequisites)
4. [Installation & Setup](#installation--setup)
5. [Project Structure](#project-structure)
6. [Configuration & Profiles](#configuration--profiles)
7. [Test Cases & Suites](#test-cases--suites)
8. [Custom Keywords](#custom-keywords)
9. [Execution](#execution)

   * [Katalon Studio GUI](#katalon-studio-gui)
   * [Gradle CLI](#gradle-cli)
10. [Reporting & Logs](#reporting--logs)
11. [Contributing](#contributing)
12. [License](#license)

---

## Overview

This repository contains automated tests for validating RESTful APIs. It leverages Katalon Studio’s built‑in WebService keywords, custom Groovy scripts, and externalized data files to cover functional, negative, and data‑driven test scenarios.

---

## Features

* **REST API Testing**: GET, POST, PUT, DELETE operations.
* **Data‑Driven**: Parameterize requests using Excel, CSV, or JSON.
* **Environment Profiles**: Easily switch base URLs and credentials.
* **Custom Keywords**: Reusable components for authentication, response validation, etc.
* **Gradle Integration**: Headless execution for CI pipelines.

---

## Prerequisites

* **Java JDK 8 or 11** (set `JAVA_HOME`)
* **Katalon Studio 8.x+**
* **Gradle** (wrapper included)
* **Git**

---

## Installation & Setup

1. **Clone the repository**

   ```bash
   git clone https://github.com/zuchalbastian/Test-Automation-API.git
   cd Test-Automation-API
   ```
2. **Verify Java & Gradle**

   ```bash
   java -version
   ./gradlew -v
   ```
3. **Open in Katalon Studio**

   * Launch Katalon Studio → **File → Open Project** → select this folder.
4. **Import Dependencies** (if any) via **Project → Settings → External Libraries**.

---

## Project Structure

```plaintext
Test-Automation-API/
├── .cache/ Keywords           # Cached custom keyword outputs
├── Include/ config           # Global variables & configurations
├── Object Repository/        # Request objects & test object definitions
├── Profiles/                 # Environment-specific variables (e.g., dev, staging)
├── Scripts/                  # Custom Groovy keywords and utilities
├── Test Cases/               # Individual API test cases
├── Test Suites/              # Grouped test suites
├── settings/                 # Katalon project settings
├── build.gradle              # Gradle build and plugin configuration
├── console.properties        # CLI override properties
└── Test Automation API.prj   # Katalon project file
```

---

## Configuration & Profiles

* Profiles located in **Profiles/** directory (e.g., `default`, `staging`, `production`).
* Variables include `baseURL`, authentication tokens, timeouts.
* Activate via Katalon: **Profiles → Activate Profile**.
* Override in CLI: `-Pprofile=staging`.

---

## Test Cases & Suites

* **Naming Convention**: `TC_[Module]_[Action]_[ExpectedResult]` (e.g., `TC_User_GET_Success`).
* **Request Objects**: defined under **Object Repository/**, contain endpoint URLs, HTTP methods, headers, and body templates.
* **Assertions**: use Katalon’s WebService keywords, for example:

  ```groovy
  import static com.kms.katalon.core.testobject.ObjectRepository.findTestObject
  import com.kms.katalon.core.webservice.keyword.WSBuiltInKeywords as WS

  ResponseObject response = WS.sendRequest(findTestObject('User/GET User by ID', [('userId') : 123]))
  WS.verifyResponseStatusCode(response, 200)
  WS.verifyElementPropertyValue(response, 'data.id', 123)
  ```

---

## Custom Keywords

Reusable methods located in **Scripts/**. Example for bearer token authentication:

```groovy
package customKeywords

import com.kms.katalon.core.annotation.Keyword
import com.kms.katalon.core.webservice.keyword.WSBuiltInKeywords as WS
import static com.kms.katalon.core.testobject.ObjectRepository.findTestObject

class AuthKeywords {
    @Keyword
    def String getAuthToken(String username, String password) {
        ResponseObject resp = WS.sendRequest(findTestObject('Auth/POST Login', [('user') : username, ('pass') : password]))
        WS.verifyResponseStatusCode(resp, 200)
        return WS.getElementPropertyValue(resp, 'token')
    }
}
```

---

## Execution

### Katalon Studio GUI

1. Open **Test Suites** in the Test Explorer.
2. Right‑click a suite → **Run** → select target environment and profile.
3. Review results in the **Log Viewer** and **Reports** panels.

### Gradle CLI

Execute tests headlessly:

```bash
./gradlew katalonExecute \
  -PkatalonProjectPath="${PWD}" \
  -PkatalonDistributionUrl="https://release-katalon-studio.s3.amazonaws.com/Katalon_Studio_8.3.0/Katalon_Studio_8.3.0.zip" \
  -PbrowserType="Chrome" \
  -Pprofile="default"
```

*Even though browserType isn’t used for API tests, the property is required by the plugin.*

---

## Reporting & Logs

* Generated reports are stored under:

  ```
  Reports/<TestSuiteName>/<yyyyMMdd_HHmmss>/
  ```
* Available formats: `HTML`, `JUnit XML`.
* Detailed request/response logs can be found in the `.log` files within the report folder.

---

## Contributing

1. Fork the repo.
2. Create a feature branch: `git checkout -b feature/YourFeature`.
3. Commit your changes and push: `git push origin feature/YourFeature`.
4. Open a Pull Request describing your updates.

---

## License

Distributed under the [MIT License](LICENSE).

---

*Generated with ❤️ by Zuchal Ari Bastian*
