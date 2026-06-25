# Changelog

All notable project documentation changes are recorded here.

## Unreleased

### Added

- Professional architecture, data-flow, and Mermaid documentation
- Safe Wazuh and Sysmon configuration examples
- Consistent formatting across the original four project phases
- Validation command reference
- Custom rule explanations, MITRE mapping, and rule-validation guide
- Five repeatable SOC validation test cases
- Incident-report template and sample FIM incident report
- Screenshot placeholders for empty evidence folders

### Changed

- Rewritten README and project summary for portfolio use
- Standardized lab guide names and structure
- Replaced concrete manager addresses and credentials with placeholders
- Moved the legacy project DOCX into `reports/`
- Restored detection validation as the completed Phase 04

### Security

- Removed `note.txt`, which contained cleartext credentials and unrelated sensitive lab notes
- Added secret and local-artifact patterns to `.gitignore`
- Retained legacy screenshots and DOCX as authorized simulated-lab evidence
