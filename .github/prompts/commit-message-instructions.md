# Commit message guidelines

Write clear, descriptive commit messages following the Conventional Commits format:

  type(scope): short description

Where:

- type is one of: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`.
- scope is optional (e.g., `api`, `ui`, `auth`) and narrows the change area.
- description is imperative, concise, and ≤ 50 characters (no trailing period).

## Required outputs

When generating commit messages (e.g., via a prompt), produce these three outputs in order and without extra commentary:

1. Header — single-line Conventional Commit header: `type(scope): short subject`
2. Message — full commit message:
   - Header on the first line
   - Blank line
   - Body wrapped at 72 characters describing *what* changed and *why*
   - Tests: a line describing tests added or manual steps
   - Footer: `BREAKING CHANGE: ...` if applicable, and `Refs: #<issue>`
3. Summary — terse one-line git-log summary (~72 characters), imperative

## Body guidance

- Explain the motivation and consequences, not just what you changed.
- Use bullet points for notable details.
- Mention side effects and migration steps if any.

## When to ask questions

If required context is missing (intent, key files, whether tests were added, breaking impact), ask up to two short clarifying questions before producing the outputs.

## Template

Header:

```
type(scope): short imperative description
```

Full message structure:

```
<Header>

<Body lines wrapped at 72 chars explaining what and why.
Use bullet points for details when helpful.

Tests: describe tests run or added.
BREAKING CHANGE: describe migration steps (if applicable)
Refs: #123
```

## Examples

Good

```
Header: feat(api): add file upload endpoint

Message: feat(api): add file upload endpoint

Add a POST /upload endpoint with multipart handling and size
validation. Store uploaded files in the configured storage backend
and return a JSON payload with file metadata.

Tests: unit tests for validation and an integration test for upload.
Refs: #123
Summary: add file upload endpoint with validation and tests
```

Bad

```
Header: added endpoint for uploads
Message: added endpoint for uploads

I wrote some code to upload files.
Summary: added uploads
```

## Checklist for risky changes

- [ ] Add or update unit tests
- [ ] Add or update integration tests
- [ ] Update documentation or migration notes
- [ ] Run the full test suite locally and on CI

## Usage hint

When used from a prompt (for example `/generate-commit-message`), follow these rules exactly. If information is missing, ask up to two targeted clarifying questions. Otherwise, emit the Header, Message, and Summary in that order with no extra commentary.
