You help users publish their project to a public GitHub repository while keeping personal/private files separate.

## Task

Publish the current project's public-facing files to a separate public GitHub repo, using a `.public` whitelist to control what gets shared.

## Steps

### 1. Check for `.public` whitelist

Look for a `.public` file in the project root.

- **If it exists**: read it and proceed to Step 3.
- **If it doesn't exist**: proceed to Step 2 to create one.

### 2. Create `.public` whitelist (interactive)

List all files in the project (excluding `.git/`, `node_modules/`, `.env`, and other obvious private files).

For EACH file or directory, ask the user whether it should be **Public** or **Private**. Present files in batches of 5-10 for efficiency.

When asking, flag potential concerns:
- Files containing local paths (e.g., `c:\Projects\...`) → flag as "contains local paths"
- Files with personal info (career goals, milestones, networking strategies) → flag as "contains personal info"
- Config files with credentials → flag as "may contain secrets"
- Code files and generic documentation → suggest "likely safe to publish"

After the user decides on all files, create `.public` in the project root:

```
# Public files whitelist
# Only files listed here will be published to the public GitHub repo
# Run /publish again to update

repo: <github-username>/<repo-name>
description: <one-line description>

# Files
README.md
src/
linkedin.md
```

The `repo:` and `description:` fields will be used when creating the GitHub repo.
Ask the user for the repo name and description if creating for the first time.

### 3. Check public GitHub repo

Read the `repo:` field from `.public`.

- **If repo doesn't exist on GitHub**: create it with `gh repo create <name> --public --description "<desc>"`
- **If repo exists**: proceed to update it.

### 4. Sync files to public repo

For each file listed in `.public`:
- If the file is a private file with a public counterpart noted (e.g., `PRD.md -> README.md`), generate the public version:
  - Strip personal info (career goals, local paths, networking strategies, private references)
  - Keep only tool/project documentation relevant to other users
- Otherwise, copy as-is.

Use a temporary directory or the repo's local clone to stage files, then commit and push.

### 5. Report results

Show the user:
- Which files were published
- Which files were skipped (private)
- The public repo URL

## `.public` file format

```
# Public files whitelist
repo: username/repo-name
description: Short description for GitHub

# Files to publish (one per line, supports directories)
README.md
src/
lib/utils.js

# File mappings (private file -> public version)
# Format: private-file -> public-file
# The public version will be auto-generated with personal info stripped
internal-prd.md -> README.md
```

## Rules

- NEVER publish files not listed in `.public`
- ALWAYS ask the user file-by-file when creating `.public` for the first time
- NEVER include in public repos: career goals, personal milestones, local file paths (c:\...), networking strategies, salary info, or private project references
- If `$ARGUMENTS` contains a message, use it as the commit message
- If `.public` already exists and user runs `/publish` again, ask if they want to review/update the whitelist or just sync

## Integration with /linkedin

When `/linkedin` skill needs a GitHub link:
- Check if `.public` exists and has a `repo:` field
- If yes, use that repo URL in the post
- If no, suggest: "Run `/publish` first to create a public repo for this project"
