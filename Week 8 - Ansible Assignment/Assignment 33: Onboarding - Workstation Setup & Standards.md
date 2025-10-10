# Assignment 33: **Onboarding — Workstation Setup & Standards**

## Objective

Set up a **production-ready** Ansible dev workstation the way a real team expects: clean installs, editor tooling, security basics, and repo hygiene. By the end you’re ready to pull any company repo and be productive on day one.

---

## Real-World Scenario

You’re joining a team that runs Ansible at scale. They expect consistent environments, clean commits, and zero “works on my machine.” Your task is to bring your laptop/VM up to the team’s standards.

---

## Learning Goals

1. Install Ansible in an **isolated Python environment** (no system pollution).
2. Configure **VS Code** for YAML + Ansible with linting and snippets.
3. Prepare **SSH** for enterprise use (keys, agent, config, known_hosts).
4. Set up **Git** identity/signing and repo hooks (pre-commit).
5. Add a minimal, reusable **ansible.cfg** that teams commonly expect.
6. Capture everything in a reproducible **README + checklist**.

---

## Constraints & Hints

* Single workspace folder: `ansible-onboarding/`
* No sudo installs unless required by OS package manager.
* Prefer **`python -m venv`** or **pyenv**; avoid global pip.
* If on Windows, use **WSL2 Ubuntu** for the control node.
* Corporate network? Note proxy/CACert steps you needed.

---

## Steps

### 1) Environment & Ansible install

Create and activate an isolated Python env, then install Ansible + lint tools.

```bash
mkdir -p ~/ansible-onboarding && cd ~/ansible-onboarding
python3 -m venv .venv && source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install ansible ansible-lint yamllint
ansible --version
ansible-lint --version
```

Save dependencies:

```bash
python -m pip freeze > requirements.txt
```

### 2) VS Code setup (tooling that teams expect)

Install extensions:

* **Red Hat Ansible**
* **YAML (Red Hat)**
* **Python**
* **EditorConfig** (recommended)

Create:

```
.vscode/settings.json
.editorconfig
```

**.vscode/settings.json**

```json
{
  "python.defaultInterpreterPath": "${workspaceFolder}/.venv/bin/python",
  "ansible.python.interpreterPath": "${workspaceFolder}/.venv/bin/python",
  "ansibleLint.enabled": true,
  "yaml.validate": true,
  "files.trimTrailingWhitespace": true,
  "editor.formatOnSave": true
}
```

**.editorconfig (opinionated but common)**

```
root = true
[*]
charset = utf-8
end_of_line = lf
indent_style = space
indent_size = 2
insert_final_newline = true
trim_trailing_whitespace = true
```

### 3) Baseline `ansible.cfg` (team-friendly defaults)

Create `ansible.cfg` in the project root:

```ini
[defaults]
inventory            = inventories/     # placeholder folder (used later)
roles_path           = roles:./.ansible/roles
host_key_checking    = True
retry_files_enabled  = False
interpreter_python   = auto_silent
forks                = 10
timeout              = 30
stdout_callback      = yaml
bin_ansible_callbacks = True

[ssh_connection]
pipelining = True
ssh_args   = -o ControlMaster=auto -o ControlPersist=60s
```

> We won’t use inventory yet—this just reflects real repo structure.

### 4) SSH readiness (enterprise basics)

* Generate a fresh key (if you don’t have one for work):

  ```bash
  ssh-keygen -t ed25519 -C "you@company" -N "" -f ~/.ssh/id_ed25519
  eval "$(ssh-agent -s)" && ssh-add ~/.ssh/id_ed25519
  ```
* Create `~/.ssh/config` with safe defaults:

  ```
  Host *
    ServerAliveInterval 30
    ServerAliveCountMax 4
    AddKeysToAgent yes
    IdentityFile ~/.ssh/id_ed25519
    StrictHostKeyChecking ask
  ```

### 5) Git identity, signing & hooks

```bash
git config --global user.name  "Your Name"
git config --global user.email "your.email@company.com"
git config --global init.defaultBranch main
```

**(Optional but appreciated by teams)** Enable commit signing (GPG or SSH) and note the steps you used.

Install **pre-commit** and set standard hooks:

```bash
python -m pip install pre-commit
cat > .pre-commit-config.yaml <<'YAML'
repos:
  - repo: https://github.com/adrienverge/yamllint
    rev: v1.35.1
    hooks:
      - id: yamllint
  - repo: https://github.com/ansible/ansible-lint
    rev: v24.6.1
    hooks:
      - id: ansible-lint
YAML

pre-commit install
```

> This gives immediate feedback when you eventually add YAML/playbooks—exactly how mature teams work.

### 7) README + Checklist

Create `README.md` describing:

* OS + version, Python version, and how you installed Ansible
* Extensions installed and why
* SSH key location + config (no private keys in repo)
* Git config (and signing if used)
* How to activate the venv and run `ansible-lint` / `yamllint`
* Any corporate/proxy/CACert notes

Include a **“New Machine? Do This”** checklist (10–12 bullet points).

---

## Final Output (What success looks like)

* A clean repo `ansible-onboarding/` containing:

  * `.venv/` (local), `requirements.txt`
  * `.vscode/settings.json`, `.editorconfig`
  * `ansible.cfg`
  * `.pre-commit-config.yaml` (installed)
  * `README.md` with your machine details + checklist
* Commands that work without errors:

  * `ansible --version`
  * `ansible-lint --version`
  * `pre-commit run --all-files` (should pass—no playbooks yet, so it’s a smoke test)
* SSH agent running and your key added (`ssh-add -l` shows it)

---

## Submission Guidelines (Single Document)

Name: `Assignment33_Ansible_Onboarding_<YourName>.pdf`

Include screenshots:

1. `ansible --version`
2. VS Code extensions panel (Ansible/YAML/Python installed)
3. `.pre-commit` installed output
4. `ssh-add -l` showing your key loaded
5. Your repo tree showing the files above

Add a short reflection (4–6 lines) as comment on [this Video](https://youtube.com/live/XqqlV1wbVQ8?feature=share). 

* One thing that makes this setup **team-friendly**
* One pitfall you avoided (e.g., global pip, missing SSH agent)
* Anything you had to do for **proxy/CA** in a corporate setup

