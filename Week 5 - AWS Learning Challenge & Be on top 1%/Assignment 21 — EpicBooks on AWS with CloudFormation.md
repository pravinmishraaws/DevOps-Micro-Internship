# Assignment 21 — EpicBooks on AWS with CloudFormation

## Learning Outcomes

* Author a parameterized CloudFormation template for a simple 2-tier web + DB stack
* Use **Parameters, Outputs, Tags
* Compare **manual vs. IaC** time, repeatability, and reliability

## What You’ll Build

VPC, one **public subnet** (EC2 web), **two private subnets** (RDS MySQL), SGs, EC2 with **UserData** (nginx placeholder page), and **RDS**.

## Starter Files 

* CloudFormation template
* Parameters (dev)
* Parameters (prod)
* Assignment - [Watch Epicbook deployment here](https://www.youtube.com/watch?v=LMlSmHM8QlU&t=15961s) 

---

## Tasks

### Part A — Estimate & Plan

* Estimate how many **hours** the manual deployment took.
* List **3 errors/risks** IaC reduces (e.g., wrong SG ports, missing tags, drift).

### Part B — Build & Validate


### Part C — Deploy


### Part D — Promote to **prod**

* Deploy a **prod** stack with `params-prod.json`

### Part E — Reflection

* Manual vs IaC: **time, repeatability, safety** (2–3 paragraphs).
* Upload screenshots: **Stack Outputs**, **Web page**

---

## Submission (Google Form fields)

* Full name, email, **region**
* GitHub/Gist link to your **template** + **params**
* **Dev** & **Prod** stack names and **Outputs** (paste values)
* All required **screenshots**
* Reflection answers
