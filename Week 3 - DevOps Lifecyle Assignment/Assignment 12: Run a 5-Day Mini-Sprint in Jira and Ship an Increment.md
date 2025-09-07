# Assignment 12: Run a 5-Day Mini-Sprint in Jira and Ship an Increment

## Objective
Execute a **5-day mini-sprint** in Jira and deliver a **real increment** to your existing **Mini Finance EC2 deployment**.  
You will plan, implement, improve, and demonstrate progress daily, following Scrum practices.  

---

## Sprint Goal (Paste into Jira Sprint Goal)
```

Ship a visible Mini Finance footer (version + date + author) to EC2 and document progress daily.

```

---

## Story (Paste into Jira)
**Summary:** Add footer with version and deploy date  

**Description:**  
Show `Mini Finance v1.0 — Deployed on <DD Mon YYYY> — By <Student Name>` in the footer of all pages.  

**Acceptance Criteria (Gherkin):**
```

Scenario: Footer shows version and deploy date
Given I open the homepage
When the page loads
Then I see the exact text "Mini Finance v1.0 — Deployed on <DD Mon YYYY> — By <Student Name>"
And this is visible on the public EC2 URL

```

- Story point estimate: **1**  
- Labels: **frontend**  

---

## Subtasks (Create Under the Story)

### Day 1 – Implement & Deploy
- Implement footer (HTML/CSS)  
- Commit & push  
- Deploy to EC2  
- Verify on EC2 & screenshot  

### Day 2 – Make Date Dynamic
- Update footer with today’s date (e.g., `26 Aug 2025`)  
- Commit & push  
- Deploy & screenshot (footer + code snippet in README)  

### Day 3 – Polish & Accessibility
- Adjust footer font size/spacing, ensure **contrast ≥ AA**  
- Test on **mobile (narrow)** and **desktop**  
- Deploy & screenshot both views  

### Day 4 – Provenance / Health
- Add commit hash or env tag in footer (e.g., `rev: abc123`)  
- OR add `/healthz` page returning `OK`  
- Verify with **browser and curl**  
- Deploy & screenshot (footer and/or health check)  

### Day 5 – Review & Retro
- Record a **2–3 min demo** of EC2 URL  
- Write a short **Retro comment** (What went well, Improvements, Scrum pillar/value seen)  
- Capture **end-of-sprint Burndown screenshot**  

---

## Daily Scrum (Add as Comments in Jira Story)
Each day, comment on the Story:
```

Yesterday: ...
Today: ...
Blockers: ...

```

---

## What to Start in Jira on Day 1
- Move the Story into **Sprint 1** and start the sprint.  
- Use Jira board columns (**To Do / In Progress / Done**).  
- Update Story/Subtasks daily.  
- Add Daily Scrum comments.  
- Track progress via **Reports → Burndown chart**.  

---

## How to Submit Your Assignment

### For Live Cohort Students
Follow the submission guidelines explained during the session.  

DevOps Micro Internship [Assignment Master Sheets](https://docs.google.com/spreadsheets/d/1HnlenHEjytvLJMy84bBF-5B1RABaY_BjbfwCj-qnvHM/edit?gid=610294991#gid=610294991)

### For Self-Paced Udemy Students
Submit the assignment by clicking **“Next”** and answering the provided questions.  

---

## Questions for This Assignment
Submit the following:  

1. **Board screenshots**: Day 1 (start) and Day 5 (end)  
2. **EC2 screenshots**: footer visible (Day 1) + improvements (Day 2–4)  
3. **Browser or curl evidence** (Day 4) if commit hash or `/healthz` added  
4. **Burndown Chart screenshot** (end of sprint)  
5. **Story link or screenshot**: Acceptance criteria, subtasks, and daily comments visible  
6. **Short demo video** (2–3 min) showing the EC2 URL in action  
7. **README snippet** showing what changed and when  

---

## Why This Change?
Each day represents **real progress**:  
- **Day 1:** Basic implementation & deployment  
- **Day 2:** Add dynamism (date)  
- **Day 3:** Improve quality & accessibility  
- **Day 4:** Add provenance or ops signal  
- **Day 5:** Demo, retro, and metrics  

This mirrors the **Scrum cycle** of incrementally improving software, not just repeating the same change.  
