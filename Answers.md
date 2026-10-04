# Answers to Part 3

Add your answers to the questions in Part 3, Step 2 below. 

## Vulernability Remediation:
### Vulnerability 1: 
1. Which package or library are you addressing?
Pillow, version 9.4.0(listed in requirements.txt). Severity: CRITICAL, as per the Trivy scan(CVSS v3.1 base score 8.1 per NVD)while the NVD  scores it as High.
2. Which CVE is linked to this vulnerability?
CVE-2023-50447. Pillow through version 10.1.0 allows arbitrary code execution through `PIL.ImageMath.eval` via its `environment` parameter. If an attacker can influence the values passed to this function, they can run code of their choosing on the server. It is a different flaw from CVE-2022-22817, which concerned the `expression` parameter.
3. What remediation steps do you suggest?

- Upgrade Pillow to 10.2.0 or later (the fixed version listed in the scan) by updating the Pillow line in requirements.txt.
   - Rebuild the Docker image and rerun the pipeline to confirm the finding no longer appears.
   - Test the application after upgrading, since moving from 9.x to 10.x removed some older Pillow APIs.
   - As defense in depth, never pass untrusted user input to `ImageMath.eval`.

### Vulnerability 2:
1. Which vulnerability are you addressing?
PyYAML, version 5.1 (listed in requirements.txt). Severity: CRITICAL, per the Trivy scan(CVSS v3.1 base score 9.8 per NVD). It is an unsafe deserialization vulnerability.
2. Which CVE is linked to this vulnerability?
CVE-2020-1747. PyYAML versions before 5.3.1 can execute arbitrary code when they parse untrusted YAML with `full_load` or the `FullLoader` loader. An attacker can craft a YAML document that abuses the `python/object/new` constructor to make the parser create Python objects and run code.
3. What remediation steps do you suggest? 

- Upgrade PyYAML to 5.3.1 or later (the fixed version listed in the scan). A newer release, such as 5.4 or later, is better because further FullLoader issues, such as CVE-2020-14343, were fixed after 5.3.1.
   - Update requirements.txt, rebuild the Docker image, and rerun the pipeline to confirm the finding is gone.
   - In the code, use `yaml.safe_load()` instead of `yaml.load()`, `full_load` or `FullLoader` for any untrusted input.
