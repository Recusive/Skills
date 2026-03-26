# File Structure, Naming, and Module Organization

## Authoritative Source

The repo's CLAUDE.md (or equivalent conventions file) has the exact rules for this codebase — module patterns, naming conventions, import style, test file locations. **Read CLAUDE.md first.** What follows here is the universal process for figuring out where things go in ANY repo.

## Before Creating Any File

1. **Find where similar files live.** Don't guess. Grep or glob for files that do similar things. If you're adding a models client and the repo has `src/clients/openai.rs`, yours goes in `src/clients/anthropic.rs` — not in a new top-level directory.

2. **Check the module depth pattern.** Does the repo use flat files or subdirectories? One convention or the other — match what's there.

3. **Check the visibility pattern.** How does the repo export types? Private by default with selective re-exports? Or public modules? Follow the existing pattern.

## The Universal Rules

### Source Files
- New functionality in an existing package → new file in that package's directory
- New functionality that spans packages → consider which package owns the responsibility
- Never create a new top-level package/crate/module unless the plan explicitly calls for it

### Test Files
- Look at how existing tests are organized — sibling files, inline modules, separate directories
- **Never mix conventions.** If the package uses sibling test files, yours is a sibling file.
- Integration tests follow the existing integration test structure

### Naming
- Check what patterns exist for the type of thing you're adding and match them exactly
- Type names, function names, file names, package names — all have conventions discoverable by looking at existing code

### Imports
- Follow the exact import style of the file you're modifying
- If the repo has a formatter that handles import ordering, let it do its job

## Red Flags

- You're creating a new directory when similar files are flat in the parent
- You're using a different visibility pattern than the rest of the module
- Your file name doesn't match the pattern of its siblings
- You're putting test utilities in the test file instead of the shared test directory
- You're adding a dependency without following the repo's dependency management pattern (workspace deps, lockfile updates, etc.)
