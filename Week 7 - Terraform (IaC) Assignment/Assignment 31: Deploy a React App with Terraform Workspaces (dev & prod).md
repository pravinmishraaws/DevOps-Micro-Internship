# Assignment 31: **Deploy a React App with Terraform Workspaces (dev & prod)**

## Objective

Use **one Terraform root module** to create **two separate environments** (dev & prod) by leveraging:

* **Dynamic variables** (variables + locals + per-env maps)
* **Terraform workspaces** (`dev`, `prod`) to isolate names and resources
* Create all require resource to deploy our **[React Application](https://github.com/pravinmishraaws/my-react-app)**
* Prove that switching workspaces **does not replace** the other environment

---

## Real-World Scenario

Your team serves a React Application from a either **AWS or Azure (you decide)**. You are asked to:

1. Stand up **two identical stacks** (dev & prod) with **separate state** via workspaces.
2. Use **variables** (maps/locals) so names, tags, and settings adapt by environment.
3. Build the React app locally and **publish** the build artifacts to each environment.

---

## Learning Goals

By the end you’ll be able to:

1. Use **`terraform workspace`** to create and target `dev` vs `prod` with the **same code**.
2. Drive **names/config** from **variables + locals**, e.g., `locals`.
3. Understand **idempotency** across workspaces: running `apply` in `dev` must not affect `prod`.
4. Deploy a React application.

---

## Constraints & Hints

* One root module; **no separate folders** per env.
* Use **locals** + **maps** to vary names/regions/tags:

---

## Very High-Level Steps (you fill in with Registry reading)

1. **Fork & build the React app**

   * Repo: `github.com/pravinmishraaws/my-react-app`
   * `npm install && npm run build` → creates `build/`.

2. **Initialize workspaces**

   * `terraform workspace new dev`
   * `terraform workspace new prod`
   * Keep `dev` selected to start: `terraform workspace select dev`

3. **Deploy dev**

   * `terraform init`
   * `terraform plan` / `apply`
   * **Deploy React build**:
   * Open the site URL and verify.

5. **Deploy prod (separate state via workspace)**

   * `terraform workspace select prod`
   * `terraform plan` / `apply`
   * Publish the **prod** build the same way to the prod origin.
   * Verify the **prod** URL.
   * Show that `terraform workspace select dev` → `plan` shows **no changes** to prod, and vice-versa.

6. **Prove isolation**

   * Change a **dev-only** tag or object and apply in `dev`.
   * Switch to `prod` and run `plan`: it should **not** attempt to change dev resources.

---

## Final Output (What success looks like)

* Two **independent URLs** (dev & prod) serving the React Application.
* **`terraform state`** exists once per workspace (remote or local backend—your choice).
* Re-running `plan` in one workspace does **not** affect the other.

---

## Submission Guidelines (Single Google doc)

Name: `Assignment31_Workspaces_React_<YourName>.pdf`

1. **Workspaces Evidence**

   * Screenshot: `terraform workspace list` showing `* dev` and `prod`.
   * Screenshot: `terraform plan` for `dev` (clean after first apply).
   * Screenshot: `terraform plan` for `prod` (clean after first apply).

2. **Reflection (5–8 lines)**

---

## Evaluation Criteria

* **Correct workspace usage** (dev & prod isolated).
* **Dynamic variables** correctly influence names/regions/tags per env.
* **Working React Application** in both environments.
* Clear evidence and concise reflections.
