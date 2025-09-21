# Assignment 26: Host a Static Website on Azure Storage

**Goal:** Deploy a static site to **Azure Storage (Static website hosting)** and make it publicly reachable.

---

## What to do

1. **Clone the repo**

```bash
git clone https://github.com/pravinmishraaws/Azure-Static-Website
cd Azure-Static-Website
```

2. **Personalize**

* Open `index.html` and edit the footer to read:
  **Hosted by \[Your Name]**

3. **Create a Storage Account**

* In Azure Portal → **Storage accounts** → **Create**

  * Performance: Standard
  * Redundancy: LRS (ok for demo)
  * Public access: Enabled

4. **Enable Static Website**

* Storage account → **Settings > Static website** → **Enable**
* Set **Index document name**: `index.html`
* (Optional) **Error document path**: `404.html`
* Note the **Primary endpoint** (this is your public URL).

5. **Upload site files**

* Go to **Containers > \$web** (created automatically)
* Upload all files from the repo (e.g., `index.html`, assets)

6. **Test**

* Open the **Primary endpoint** in a browser. You should see your page with your name in the footer.

7. **Submit via Google Form**

* Provide: live URL, screenshot, LinkedIn post link, and (optional) 1–2 lines on what you learned.
  *(Use your course’s submission form link.)*

---

## What you must submit

* ✅ **Live Website Link:** the **Primary endpoint** of the static website (e.g., `https://<account>.z13.web.core.windows.net/`)
* ✅ **Screenshot:** full-page screenshot of the running site showing your **“Hosted by \[Your Name]”** footer
* ✅ **LinkedIn Post Link:** proof you shared it publicly (use the caption template below)
* ✅ *(Optional)* 1–2 lines about what you learned

---

## LinkedIn caption (copy–paste & edit)

I just deployed my first **Azure Static Website** 🚀

This wasn’t just about uploading files — I got hands-on with:
✅ Azure static website hosting setup
✅ Configuring permissions & public access
✅ Deploying a live website in minutes

Here’s my hosted page 👉 **\[Insert your Azure static website link]**

#Azure #DevOps #Cloud #LearningInPublic

*(Note: use your **Azure** static website link, not S3.)*

## Cleanup

* Keep the storage account for now (**It will not cost you**).


**Estimated time:** 20–30 minutes.
