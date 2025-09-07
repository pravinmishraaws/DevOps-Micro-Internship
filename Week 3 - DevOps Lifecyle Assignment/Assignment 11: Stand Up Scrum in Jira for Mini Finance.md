# Assignment 11: Stand Up Scrum in Jira for Mini Finance

## Objective
In this assignment, you will set up a **Scrum project in Jira Cloud (Free, Team-managed)** and practice:  
- Creating a project  
- Setting up Epics, Stories, and Subtasks  
- Planning and starting Sprint 1  
- Using filters and reports  

This exercise helps you understand **Scrum artifacts, events, and pillars** using **simple UI edits** on the Mini Finance project.

---

## Step 1 — Create the Project (Team-managed → Scrum)
1. In Jira: **Projects → (+) Create project → Use template → Scrum → Team-managed**  
2. Select **Team-managed project**  
3. Name:  
```

Mini Finance – <YourName>

```
(accept the generated project key)  
4. Access: **Private**  
5. Click **Create Project**  
6. Skip the “Bring your team along” step  

✅ **Expected Outcome:** You have a new Scrum project named *Mini Finance – YourName*.  

---

## Step 2 — Create Your First Epic
1. Open your project → click **Board → Go to Backlog**  
2. At the top of the backlog, click **Epic panel toggle** (to enable if hidden)  
3. Click **+ Create Epic**  
4. Epic Name:  
```

Polish Mini Finance UI & Deploy

```

---

## Step 3 — Seed the Product Backlog (Stories under Epic)
Create **6 user stories** under the Epic with **Fibonacci estimates (1, 2, 3)**.  

### Example Stories
- **S1 — Update site header text (2 pts)**  
- Change top header to: `Mini Finance — Simple Personal Budget Tracker`  
- Acceptance Criteria: Header updates correctly on EC2 URL  

- **S2 — Primary button color refresh (2 pts)**  
- Update primary side menu color to `#0d6efd`  
- Acceptance Criteria: Color applied, accessible (AA contrast)  

- **S3 — Improve hero subtitle copy (1 pt)**  
- Replace subtitle with: `Track expenses, spot trends, and stay on budget — fast.`  

- **S4 — Footer with version & date (2 pts)**  
- Add: `Mini Finance v1.0 — Deployed on <DD Mon YYYY>`  

- **S5 — Change “Name, Email, Phone” on Home page (2 pts)**  
- Add your own name, email, phone (optional if private)  

👉 Each story: Assign to yourself + add Story Point estimate.  

---

## Step 4 — Add Subtasks (Break Work Down)
For at least **2 stories** (e.g., S2 and S4), create subtasks:  
- Edit HTML/CSS  
- Test locally  
- Deploy to EC2  
- Verify & screenshot  

---

## Step 5 — Tag by Workstream (Labels)
Add labels to stories:  
- **frontend** → S1, S2, S3, S5  
- **devops** → S4  

---

## Step 6 — Create Sprint 1 (1 week) + Goal
1. In Backlog, click **Create sprint** → name it *Sprint 1*  
2. Drag 2–3 stories (~3–5 points) into Sprint 1  
3. Sprint Goal:  
```

Ship a visible Mini Finance UI polish (header, color, footer) to EC2 with screenshots.

```
4. Start the Sprint  

---

## Step 7 — Use Filters
Apply filters in your board to see:  
- Stories  
- Subtasks  
- Overall status  

---

## Step 8 — Open the Burndown Report
1. Go to **Reports → Burndown Chart**  
2. If not visible → enable in **Project settings → Features → Reports**  

---

## Troubleshooting
- Can’t see Backlog? Enable via **Project settings → Features → Backlog**  
- Can’t estimate? Add **Story point estimate** in **Issue types → Story → Fields**  
- Labels missing? Add via **Fields** settings  
- Can’t find Epics? In Backlog, toggle **Epic panel** on  

---

## How to Submit Your Assignment

### For Live Cohort Students
Follow submission guidelines explained during the session.  

DevOps Micro Internship [Assignment Master Sheets](https://docs.google.com/spreadsheets/d/1HnlenHEjytvLJMy84bBF-5B1RABaY_BjbfwCj-qnvHM/edit?gid=610294991#gid=610294991)

### For Self-Paced Udemy Students
Submit the assignment by clicking **“Next”** and answering the provided questions.  

---

## Submission Requirements
Submit the following screenshots:  
- ✅ Backlog with the Epic and all 5 stories  
- ✅ Story 5 with **Summary + Acceptance Criteria**  
- ✅ Story showing **Assignee**  
- ✅ Sprint 1 Goal  
- ✅ Board with at least **two Subtasks** under parent stories  
- ✅ Burndown Chart (Reports → Burndown)  
- ✅ One **Filter** applied  

### Short Answer (5–8 lines)
Explain how your setup reflects:  
- **Scrum Artifacts** (Epic → Product Backlog → Sprint Backlog)  
- **Scrum Events** (Sprint Planning)  
- **Scrum Pillars/Values** (give 1–2 examples, e.g., Transparency via backlog, Commitment via Sprint Goal)  
