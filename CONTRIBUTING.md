# Contributing to Hogwarts ERP Documentation

Thank you for your interest in contributing to the Hogwarts School ERP documentation! This document provides guidelines and best practices for contributing.

## How to Contribute

### Reporting Issues

- Use GitHub Issues to report documentation gaps, errors, or suggestions
- Include the module name and file path when reporting issues
- Provide specific details about what is incorrect or missing

### Submitting Changes

1. **Fork** the repository
2. **Create a branch** from `main` for your changes
3. **Follow** the documentation standards below
4. **Submit** a Pull Request with a clear description of changes

## Documentation Standards

### Module README Structure

Every module README must follow the template in [`docs/templates/MODULE_TEMPLATE.md`](docs/templates/MODULE_TEMPLATE.md) and include:

1. **Module Overview** — Purpose, scope, and key features
2. **Outbound Connections** — Data sent to other modules (with Mermaid flowcharts)
3. **Inbound Connections** — Data received from other modules (with Mermaid flowcharts)
4. **Summary** — Business logic, trigger events, and dependency table

### Submodule README Structure

Each submodule README must include:

1. **Overview** — Purpose and description
2. **Key Features** — With pseudocode examples
3. **Data Fields** — SQL schema definitions
4. **Business Rules** — Validation and processing rules
5. **Integration Points** — Connections with other modules
6. **User Workflows** — Step-by-step processes
7. **Edge Cases** — Error handling and boundary conditions
8. **Configuration Parameters** — Configurable settings

### Formatting Guidelines

- Use **Markdown** for all documentation
- Use **Mermaid** for flowcharts and diagrams
- Use **Indian context** for examples (₹ currency, CBSE/ICSE boards)
- Include **pseudocode** for business logic
- Define **SQL schemas** for data field specifications
- Target **1,200–1,500 lines** per module README

### Naming Conventions

- Module directories: `NN_module_name/` (e.g., `01_student_management/`)
- Submodule directories: `NN_submodule_name/` (e.g., `01_student_registration/`)
- All documentation files: `README.md`

## Code of Conduct

Please read and follow our [Code of Conduct](CODE_OF_CONDUCT.md).

## Questions?

Open a GitHub Issue for any questions about contributing.
