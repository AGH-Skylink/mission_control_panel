# Mission Control – Umbrella Repository

This repository groups all Mission Control components in one place
using Git submodules (an *umbrella repo* / monorepo-style setup).

Directory structure (example):

```text
mission_control/
├─ manager/   → https://github.com/AGH-Skylink/mission_control_manager
├─ audio/     → https://github.com/AGH-Skylink/mission_control_audio
├─ server/    → https://github.com/AGH-Skylink/mission_control_server
└─ ui/        → https://github.com/AGH-Skylink/mission_control_ui
```

Each subdirectory is a **separate Git repository** (submodule) with its own history,
remote and workflow. The umbrella repo only remembers **which commit** of each
submodule is used in a given version of the whole system.

---

## 1. First-time setup (for new team members)

### 1.1. Requirements

- Git (ideally configured with an SSH key for GitHub)
- Runtime dependencies (Python / Node / etc.) – see README in each submodule

### 1.2. Cloning the whole system (recommended)

```bash
git clone --recurse-submodules https://github.com/AGH-Skylink/mission_control.git
cd mission_control
```

The `--recurse-submodules` flag automatically fetches the contents of `manager/`,
`audio/`, `server/` and `ui/` at the correct commits.

### 1.3. If you already cloned without submodules

If someone ran a plain:

```bash
git clone https://github.com/AGH-Skylink/mission_control.git
```

then later they can do:

```bash
cd mission_control
git submodule update --init --recursive
```

This command:

- initializes all submodules,
- checks them out at the commits recorded in the umbrella repo.

---

## 2. Working on a specific project (submodule)

Each submodule is a normal Git repo. Example for the **UI** project.

### 2.1. Enter the submodule and work as usual

```bash
cd mission_control/ui

# standard Git workflow
git status
git add .
git commit -m "Describe your changes"
git push
```

The `ui/` repository keeps its own remote on GitHub
(`AGH-Skylink/mission_control_ui`), so you push there as usual.

### 2.2. Update the submodule reference in the umbrella repo

After committing and pushing changes in a submodule (e.g. `ui/`):

```bash
cd mission_control/ui
# ... commit & push your work ...

cd ..                  # go back to umbrella repo root
git status             # you'll see that 'ui' points to a new commit
git add ui
git commit -m "Update ui submodule to latest"
git push
```

The umbrella repo now records:

> “In version X of the overall system, `ui` is at commit Y”.

Exactly the same rules apply to `manager/`, `audio/` and `server/`.

---

## 3. Updating submodules to the latest recorded commits

If someone else updated submodules and pushed the umbrella repo:

```bash
git pull
git submodule update --init --recursive
```

This will move all local submodules to the commits recorded in the current state
of the umbrella repository.

---

## 4. Example end-to-end workflow

1. **Clone the entire project**:

   ```bash
   git clone --recurse-submodules git@github.com:AGH-Skylink/mission_control.git
   cd mission_control
   ```

2. **Work in your submodule** (example: `audio`):

   ```bash
   cd audio
   git checkout -b feature/new-magic
   # ... implement stuff ...
   git commit -m "Add new magic"
   git push
   ```

3. **Update the umbrella repo**:

   ```bash
   cd ..
   git add audio
   git commit -m "Update audio submodule to feature/new-magic"
   git push
   ```

4. Other people then run:

   ```bash
   git pull
   git submodule update --init --recursive
   ```

and they get the same versions of all components.

---

## 5. Useful Git commands (cheat sheet)

- Clone umbrella + all submodules:

  ```bash
  git clone --recurse-submodules https://github.com/AGH-Skylink/mission_control.git
  ```

- Initialize / update submodules:

  ```bash
  git submodule update --init --recursive
  ```

- Enter a module and work like in a normal repo:

  ```bash
  cd ui          # or manager / audio / server
  git status
  git add .
  git commit -m "..."
  git push
  ```

- Record a new submodule version in the umbrella:

  ```bash
  cd ..
  git add ui     # or manager / audio / server
  git commit -m "Update ui submodule"
  git push
  ```

---

## 6. Mental model – how to think about submodules

If submodules feel confusing, keep these three rules in mind:

1. **Each folder (`manager`, `audio`, `server`, `ui`) is a separate Git repo.**
2. When you change code inside a submodule:
   - commit & push inside that submodule first,
   - *then* commit & push the updated submodule reference in the umbrella repo.
3. After `git pull` in the umbrella repo, always run:

   ```bash
   git submodule update --init --recursive
   ```

   so that your local submodules move to the correct commits recorded by the umbrella.
