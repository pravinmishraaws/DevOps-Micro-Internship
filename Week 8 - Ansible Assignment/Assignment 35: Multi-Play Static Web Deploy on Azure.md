# Assignment 35: **Multi-Play Web Deploy on Azure**

## Objective

Use a **single playbook with multiple plays** to:

* prepare web servers (packages/services),
* deploy index.html content from your control machine using **`copy`**,
* and **verify** the result from the controller.

---

## What you’ll practice

* Structuring a playbook with **multiple plays**
* Separating responsibilities (**install** vs **deploy**)
* Using **`copy`** to push static content (from the controller) to web servers
* Verifying via **`uri`** or `curl`

---

## Pre-reqs / Assumptions

* You have **4 Azure VMs** from the previous assignment (or at least one), grouped under `[web]` in `inventory.ini`.
* OS: Ubuntu 22.04 (or similar).
* Public IP access to each VM.
* Your SSH key works (passwordless).

Repo & demo app:

* Live site example to match: `https://ebstaticwebsite.z29.web.core.windows.net`
* Source: `https://github.com/pravinmishraaws/Azure-Static-Website`
  (We’ll use its `index.html` as our static payload.)

---

## Folder layout (suggested)

```
static-web/
├─ inventory.ini
├─ site.yml                   # multi-play
├─ files/
│  └─ index.html             # downloaded from the GitHub repo
└─ README.md
```

> Put the downloaded `index.html` into `files/` so `copy` can send it to targets.

---

## Playbook: `site.yml` (multi-play, production-style)

Refer Ansible [**Module documentation**](https://docs.ansible.com/ansible/2.9/modules/list_of_all_modules.html)

### Notes

* **Play 1** handles **installation** only.
* **Play 2** handles **deployment** only.
* **`copy`** pulls from controller’s `files/` directory (not from the internet).

---

## How to get the content

Download `index.html` from the repo and place it under `/files/index.html`:

* Repo: `https://github.com/pravinmishraaws/Azure-Static-Website`

> You asked specifically for **`copy`**; so we stage the file locally, then push.

---


**Manual verification**

```bash
# Pick one IP from [web]
http://<web_ip>
```

---

## Success criteria (what to submit)

* Screenshot: `ansible-playbook ...` ending with OK/changed (no failures)
* One browser screenshot of the site loading from any `web` IP
* Short reflection (4–6 lines): why split **install vs deploy** into separate plays; comment on [**this Video**](https://youtube.com/live/XqqlV1wbVQ8?feature=share). 

