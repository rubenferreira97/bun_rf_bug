# Bun `rm -rf` Symlink Traversal Bug Reproduction

Minimal reproduction demonstrating that `bun rm -rf` erroneously traverses symlinks and deletes target content on Windows.

## Bug Description

When using `bun rm -rf` to remove a directory containing a symlink (e.g., `node_modules` with linked packages), Bun incorrectly follows the symlink and deletes the contents of the target directory instead of just removing the symlink itself.

## Project Structure

```
├── project/              # Main project
│   ├── index.ts
│   ├── package.json      # Has "clean": "rm -rf node_modules"
│   ├── tsconfig.json
│   └── node_modules/
│       └── project_link/ # Symlink to ../project_link
└── project_link/         # Linked package (will be deleted!)
    ├── index.ts
    ├── package.json
    └── tsconfig.json
```

## Steps to Reproduce

1. Clone this repository
2. Navigate to the `project_link` directory and create link.
    ```cmd
    cd project_link
    bun link
    ```
3. Navigate to the `project/` directory:
   ```cmd
   cd ../project
   ```
4. Install dependencies (this creates a symlink in `node_modules`):
   ```cmd
   bun install
   ```
5. Run the clean script:
   ```cmd
   bun clean
   ```
6. Observe that `project_link/` directory contents are deleted!

## Expected Behavior

- The `node_modules` folder should be removed
- The symlink inside `node_modules/project_link` should be removed
- The original `project_link/` directory and its contents should remain intact

## Actual Behavior

- The `node_modules` folder is removed
- The contents of `project_link/` are also deleted (Bun follows the symlink)

## Environment

- **OS**: Windows
- **Bun Version**: 1.3.3

## Notes
This issue appears to be specific to Windows, in Ubuntu the correct behavior is observed. This bug is critical as it can lead to unintended data loss when using `bun rm -rf` on projects with symlinked dependencies. I lost important files due to this behavior. Please address this issue promptly.
