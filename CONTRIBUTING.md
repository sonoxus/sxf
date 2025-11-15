# Contributing to SXF

First off, thank you for considering contributing to the Sonoxus Lyrics Format! We're excited to build a new standard with the community.

## How Can I Contribute?

There are many ways to help, from improving the documentation to finding bugs and proposing new features.

### 1. Reporting Bugs

If you find a bug in the specification, schema, or examples, please [open an issue](https://github.com/sonoxus/sxf/issues)

Please include:
* A clear title.
* A description of the bug.
* If possible, a snippet of `.sxf` code that causes the issue.

### 2. Proposing New Features (The Right Way)

The SXF specification is versioned for a reason. We can't add new fields every day, as it would break parsers.

**If you have an idea for a new feature (e.g. a new key in `meta`):**

1.  **Do NOT** open a Pull Request with the changes immediately.
2.  **DO** [open an issue](https://github.com/sonoxus/sxf-format/issues) first.
3.  In the issue, describe the feature, why it's needed (the "use case"), and how it could be implemented.
4.  Let's discuss it! If the community and maintainers agree it's a good idea for a future version (e.g. v1.1), we will "accept" the proposal.

### 3. Pull Requests

We welcome Pull Requests for:
* Fixing typos in `README.md` or `specification.md`.
* Adding or improving `.sxf` files in the `/examples` folder.
* Improving the `sxf.v1.schema.json` (e.g. adding clearer descriptions).

**Before you submit a Pull Request:**
* Make sure your changes are consistent with the `specification.md`.
* For any new feature, make sure an issue was opened and approved first.

## Style Guide

* All JSON examples (in `.sxf` files or documentation) must be clean, readable, and validated against the schema.
* Use 2-space indentation for JSON files.
* Use Markdown for documentation.

## License

By contributing, you agree that your contributions will be licensed under the **Apache 2.0 License** that covers this project.