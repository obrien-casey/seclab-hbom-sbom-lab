# UIUC CS 460/ECE 419 - Fall 2026  
## Module 5: IoT/Hardware Security Introduction: Inside the Stack - Mapping Hardware and Software Bill of Materials (HBOM + SBOM)

### **HBOM vs. SBOM Explained**

| **Aspect** | **SBOM** | **HBOM** |
|-------------|-----------|-----------|
| **Purpose** | Tracks software components, versions, and known CVEs. | Tracks hardware components, part numbers, and suppliers. |
| **Focus** | Software supply-chain risk (vulnerable libraries). | Hardware provenance and tampering risk (counterfeit or insecure chips). |
| **Contents** | Package names, versions, dependencies, licenses, CVEs. | Chipsets, sensors, SoCs, communication modules, firmware IDs. |
| **Common Formats** | SPDX, CycloneDX, Syft JSON. | IPC-1752A, IPC-1754, or vendor-specific CSV/XML formats. |
| **Key Users** | Developers, vulnerability managers, software assurance teams. | OEMs, supply-chain analysts, hardware security engineers. |
| **Tagline** | *“What’s in the code.”* | *“What’s on the board.”* |

---

## **Assignment Objective**

Students will learn to generate, analyze, and interpret both SBOM and HBOM artifacts.  
By the end of this lab, you should be able to:

- Generate an SBOM from a software repository using **Syft** and **Trivy**.  
- Compare SBOM results across tools and formats (SPDX vs. CycloneDX).  
- Identify potential HBOM components and describe how hardware provenance affects software risk.  
- Perform a vulnerability analysis using **Grype**.  
- Interpret and document CVE findings in the context of a specific hardware device (e.g., M5Stack Cardputer, M5Stamp Fly, M5StampPLC).

---

## **Firmware Targets**

Choose one of the following devices:

- [M5Stack Official Cardputer Kit (M5StampS3 v1.1)](https://shop.m5stack.com/products/m5stack-cardputer-kit-w-m5stamps3?srsltid=AfmBOoqYlj6xwdijrXebQvER4xcF4CFlfx0xOF53qY1oaUlZ7jcJa945)
- [Mini-Drone: M5Stamp Fly](https://docs.m5stack.com/en/app/Stamp%20Fly)
- [M5StampPLC (Industrial Controller)](https://shop.m5stack.com/products/m5stamp-plc-controller-with-m5stamps3)

---

## **Resources**

- [Idaho National Lab (INL) SBOM Portal](https://sbom.inl.gov)  
- [National Vulnerability Database (NVD)](https://nvd.nist.gov/vuln/search)

---

## **Tools Overview**

### **Syft**
- SBOM generator that inspects a filesystem, container image, or repository.  
- Reports packages, versions, and dependencies.  
- Open source (by Anchore).  
- Produces rich metadata; integrates with CI/CD pipelines.

### **Trivy**
- Open-source scanner (by Aqua Security).  
- Scans repos, filesystems, and container images.  
- Detects OS packages, app libraries, and misconfigurations.  
- Fast and broadly applicable.

### **Grype**
- Vulnerability scanner (by Anchore).  
- Works directly with SBOMs or images.  
- Focuses on known vulnerabilities (CVEs).  
- Pairs well with Syft in CI pipelines.

---

## **Part 1: SBOM Generation (in Codespaces)**

All commands can be executed directly inside your GitHub Codespace using this repo:  
👉 [https://github.com/obrien-casey/seclab-hbom-sbom-lab](https://github.com/obrien-casey/seclab-hbom-sbom-lab)

### **Steps**

1. **Clone a firmware repository:**

   ```bash
   git clone https://github.com/m5stack/M5Cardputer.git
   
   # or
   
   git clone https://github.com/m5stack/M5StampFly.git
   
   # or
   
   git clone https://github.com/m5stack/M5StamPLC.git
   ```

2. **Navigate to the firmware folder:**

   ```bash
   cd M5Cardputer
   ```

3. **Generate an SBOM in SPDX format (Syft):**

   ```bash
   syft . -o spdx-json > ../deliverables/sbom_syft_spdx.json
   ```

4. **Generate a CycloneDX SBOM (Trivy):**

   ```bash
   trivy fs . --format cyclonedx --output ../deliverables/sbom_trivy_cdx.json
   ```

5. **Record in your report:**
   - Number of components each tool reports.  
   - One key difference (format, fields, or component count).  
   - Screenshots of terminal output.

6. **Confirm SBOM files exist:**

   ```bash
   ls ../deliverables/
   ```

---

## **Part 2: SBOM Vulnerability Analysis**

1. **Feed the SBOM into Grype:**

   ```bash
   grype sbom:../deliverables/sbom_syft_spdx.json -o table > ../deliverables/vuln_analysis_grype.txt
   ```

   > ✅ If Grype reports zero vulnerabilities, that’s fine - include an explanation of why.

2. **In your report, include a Top 5 Vulnerabilities table:**

   | **CVE** | **Severity** | **Component** | **Version** | **Comment** |
   |----------|---------------|----------------|--------------|--------------|
   | CVE-2023-0286 | High | OpenSSL | 3.0.2 | TLS certificate validation bypass |

3. **Preview output:**

   ```bash
   head -20 ../deliverables/vuln_analysis_grype.txt
   ```

4. **Select one CVE** and summarize its cause or impact using NVD.

---

## **Part 3: HBOM Exploration**

Use the device documentation to identify **three (3)** key hardware components (e.g., microcontroller, IMU, LCD).

For each component, provide:
- **Manufacturer** (e.g., Espressif, Epson)  
- **Part number or chip family** (e.g., ESP32-S3, MPU6886)  
- **Known vulnerabilities or risks** (from NVD or functional reasoning)  
- **Summary** of how hardware dependencies influence software risk.

---

## **Part 4: Deliverables**

Submit a **2–3 page report** including:

- SBOM generation results  
- Vulnerability analysis (top 5 CVEs or rationale for zero matches)  
- HBOM component summary  
- Reflection on insights and process  

### **Upload to `/deliverables/` and push to GitHub:**

```
deliverables/
│
├── sbom_syft_spdx.json
├── sbom_trivy_cdx.json
├── vuln_analysis_grype.txt
├── hbom_summary.md
└── reflection.md
```

---

## **Grading Rubric (25 Points Total)**

| **Criterion** | **Excellent (Full Points)** | **Partial Credit** | **Points** |
|----------------|-----------------------------|--------------------|------------|
| **SBOM Generation** | Both SBOMs generated (Syft SPDX + Trivy CycloneDX); includes discussion of differences and screenshots. | One SBOM missing, misnamed, or lacking discussion. | 8 |
| **Vulnerability Analysis** | Grype scan performed; top 5 CVEs or rationale for 0 findings; NVD context explained. | Scan missing or minimal discussion. | 7 |
| **HBOM Exploration** | Three components documented with vendor, part #, and vulnerability context. | Fewer than three components or minimal discussion. | 6 |
| **Reflection & Professionalism** | Clear, concise reflection; correct filenames; properly pushed to GitHub. | Minor clarity or submission issues. | 4 |
