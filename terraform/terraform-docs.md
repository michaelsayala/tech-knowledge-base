# Updating Terraform Documentation

This procedure explains how to generate and update the Terraform documentation for the project using [`terraform-docs`](https://terraform-docs.io/).

The generated documentation provides an automatically maintained reference of the Terraform module, including:

* Terraform inputs and variables
* Outputs
* Providers
* Resources
* Modules
* Requirements

## Prerequisites

Before generating the documentation, make sure you have:

* Git installed
* Go installed
* The Terraform project available locally
* Access to the project repository
* A working Terraform configuration

Verify that Go is installed:

```bash
go version
```

## 1. Install terraform-docs

Install the latest version of `terraform-docs` using Go:

```bash
go install github.com/terraform-docs/terraform-docs@latest
```

The executable is normally installed in:

```text
$HOME/go/bin/terraform-docs
```

Verify the installation:

```bash
terraform-docs --version
```

If the command is not found, make sure `$HOME/go/bin` is included in your `PATH`.

For example:

```bash
export PATH="$PATH:$(go env GOPATH)/bin"
```

Then verify again:

```bash
terraform-docs --version
```

## 2. Generate the Terraform Documentation

From the root directory of the Terraform project, run:

```bash
terraform-docs markdown . > docs/terraform.md
```

This command:

* `terraform-docs` — runs the documentation generator
* `markdown` — generates Markdown-formatted documentation
* `.` — uses the current directory as the Terraform module
* `>` — redirects the generated output to a file
* `docs/terraform.md` — stores the generated documentation

### Example Project Structure

After generating the documentation, the repository may look like:

```text
terraform-aws-splunk-cluster/
├── .github/
├── docs/
│   ├── architecture.png
│   └── terraform.md
├── modules/
├── main.tf
├── variables.tf
├── outputs.tf
├── providers.tf
├── terraform.tf
├── terraform.tfvars.example
└── README.md
```

## 3. Review the Generated Documentation

Open the generated file:

```bash
cat docs/terraform.md
```

Or open it in your preferred editor:

```bash
code docs/terraform.md
```

Review the generated sections and verify that they accurately represent the current Terraform configuration.

Typical sections include:

```text
Requirements
Providers
Modules
Resources
Inputs
Outputs
```

## 4. Update the Documentation After Terraform Changes

Regenerate the **documentation whenever changes are made to the Terraform configuration that affect:**

* Variables
* Variable descriptions
* Variable types
* Default values
* Outputs
* Resources
* Modules
* Providers
* Terraform or provider requirements

For example, after adding a new variable to `variables.tf`, regenerate the documentation:

```bash
terraform-docs markdown . > docs/terraform.md
```

This keeps the documentation synchronized with the Terraform code.

## 5. Review Git Changes

After generating the documentation, check the changes:

```bash
git status
```

Review the generated documentation:

```bash
git diff -- docs/terraform.md
```

Make sure the changes are expected and that no unrelated files were modified.

## 6. Commit the Updated Documentation

If the generated documentation is correct, stage it:

```bash
git add docs/terraform.md
```

Commit the changes:

```bash
git commit -m "docs: update Terraform documentation"
```

Then push the changes:

```bash
git push
```

## Recommended Workflow

A simple workflow for maintaining the Terraform documentation is:

```text
Modify Terraform configuration
        │
        ▼
Review Terraform changes
        │
        ▼
Run terraform-docs
        │
        ▼
Review docs/terraform.md
        │
        ▼
git diff
        │
        ▼
Commit changes
        │
        ▼
Push to GitHub
```

## Quick Reference

For routine updates, the following commands are sufficient:

```bash
# Install/update terraform-docs
go install github.com/terraform-docs/terraform-docs@latest

# Verify installation
terraform-docs --version

# Generate documentation
terraform-docs markdown . > docs/terraform.md

# Review changes
git diff -- docs/terraform.md

# Commit
git add docs/terraform.md
git commit -m "docs: update Terraform documentation"
git push
```

## Optional: Use a terraform-docs Configuration File

For a more maintainable project, consider using a `.terraform-docs.yml` configuration file.

This allows the documentation format and generated sections to be standardized rather than relying entirely on command-line options.

For example:

```yaml
formatter: markdown

output:
  file: docs/terraform.md
  mode: replace

sections:
  show:
    - requirements
    - providers
    - modules
    - resources
    - inputs
    - outputs
```

Documentation can then be generated with:

```bash
terraform-docs .
```

Using a configuration file is recommended as the project grows because the documentation-generation settings are stored in the repository and can be consistently used by other contributors and CI/CD pipelines.

## Best Practice

Treat `docs/terraform.md` as **generated documentation**.

The Terraform source files remain the source of truth:

```text
variables.tf
outputs.tf
main.tf
modules/
providers.tf
terraform.tf
```

`terraform-docs` should be used to regenerate the documentation rather than manually editing the generated sections.

This helps prevent the documentation from becoming outdated as the Terraform infrastructure evolves.
