---
lab:
    title: 'Lab 09: Evaluate, publish and govern your agents'
    module: 'Module 5 - Ship it'
---

# Lab 09 - Evaluate, publish and govern your agents

## Lab introduction

You've built an agent that answers, acts, delegates and runs on its own. Is it ready for 5,000 employees?

"It worked when I tried it" isn't an answer. Production needs three things:

1. **Evidence** - an evaluation you can re-run after every change, so you know quality didn't drop.
2. **A controlled release** - publish, share with a pilot group, then ask the admin to approve it for the organization.
3. **Governance** - who can build, which connectors are allowed, what it costs, what it did.

This lab is the bridge between a demo and a service.

## Estimated timing: 45 minutes

## Lab scenario

The Service Desk manager wants to roll out **Contoso Service Desk** in Teams and Microsoft 365 Copilot. The CISO wants proof that it behaves, and the IT admin wants to approve it before anyone sees it in the Agent Store.

## Job skills

- Task 1: Create and run an evaluation
- Task 2: Improve and re-run
- Task 3: Publish to Teams and Microsoft 365 Copilot
- Task 4: Approve the agent as an admin
- Task 5: Monitor usage and cost
- Task 6: Review the governance controls
- Task 7: Clean up

---

## Task 1: Create and run an evaluation

1. Open **Contoso Service Desk** and select the **Evaluate** tab.

1. Select **Create a test set** (or **New evaluation**).

1. Add test cases. Three ways - try at least two:
   - **Upload a CSV**: download the template from the dialog, then copy the questions from `Allfiles/Labs/09/Contoso-ServiceDesk-TestSet.csv` into it and upload.
   - **Generate with AI**: let Copilot Studio create questions from your agent's description, instructions and knowledge.
   - **Write**: add one by hand, for example `Can I use my personal laptop on the office network?`

1. Name the evaluation `Service Desk - baseline`, keep the test method **General quality**, and select **Evaluate**.

1. Wait for the run to finish (a few minutes), then open **Recent results**:
   - the overall **score**,
   - each conversation and its result,
   - the **Copilot Credits used** (to generate and to grade responses).

   > 📸 **Screenshot:** `lab09-01-evaluation-results.png`

> **What does "General quality" check?** It's an AI grader that judges relevance and completeness. It does **not** compare the answer with your expected response. So read the failing cases yourself, using the expected answers in the CSV as your checklist.

> **Limitation:** evaluations run as the **signed-in user** and support agents using *No authentication* or *Authenticate with Microsoft*. Tools that need a connection use your connection during the run.

---

## Task 2: Improve and re-run

1. Pick the weakest case from the results. Typical culprits:
   - the joke about the CFO wasn't refused firmly enough,
   - the Wi-Fi password question produced a guess,
   - the monitor ticket didn't ask for confirmation.

1. Fix the **cause** - instructions, a knowledge description, or a tool description. Not the test.

1. On **Evaluate**, select **Evaluate test set again**. Compare the new score with the baseline.

1. Optional: switch the agent **Model** (Lab 04, Task 3), re-run, and compare quality **and** credits. Is the bigger model worth it for this agent?

> **The habit to build:** *change → evaluate → compare*. Every time. A prompt tweak that fixes one case can quietly break three others.

---

## Task 3: Publish to Teams and Microsoft 365 Copilot

1. Select **Publish**. Wait for the confirmation.

1. Open the channels for the agent and select **Teams and Microsoft 365 Copilot** (wording may be *Microsoft Teams and Microsoft Copilot*).

1. Make sure **Make agent available in Microsoft Copilot** is selected, then **Add channel**.

1. Select **Edit details**: short description, long description, icon, developer name (`Contoso IT`). **Save**.

1. **Pilot first:** share the agent with a small group (your table, or a security group) and open it in Teams with **See agent in Teams**. Test it in Teams and in the Microsoft 365 Copilot app.

1. When the pilot is happy, submit the agent **for admin approval** so it can appear in the organization's catalog (Agent Store > *Built by your org*).

   > 📸 **Screenshot:** `lab09-02-teams-channel.png`

> **Don't forget the dependencies:** the Service Desk agent calls the **Request software approval** workflow and the **License Advisor** connected agent. Both must be published. And users need access to the **Contoso IT Hub** SharePoint site, or the agent can't answer policy questions for them.

---

## Task 4: Approve the agent as an admin

> Trainer or admin account required.

1. Go to the [Microsoft 365 admin center](https://admin.microsoft.com) > **Copilot** controls > **Agents** > **All agents** > **Requests**.

1. Select **Contoso Service Desk**. Review the **Data & tools** tab: knowledge sources, tools, connected agents. Ask yourself: *would I approve this connector list in production?*

1. Select **Publish** and choose the availability: **specific users/groups** (recommended for a first wave) or everyone.

1. As a user, open the **Agent Store** in Microsoft 365 Copilot and find the agent under **Built by your org**.

---

## Task 5: Monitor usage and cost

1. Back in Copilot Studio, open the agent's **Monitor** area (production data appears once people use the published agent).

1. Look for: sessions, tool success/failure, unanswered questions, user feedback.

1. For **Contoso Triage Agent** (Lab 08), open **Activity** and filter for **failed** runs. Open one and find the step that failed.

1. Check the Copilot Credits consumption of your environment (Power Platform admin center > **Licensing** / capacity, depending on your tenant). Which agent costs the most? The autonomous one usually does - it runs whether anyone watches or not.

---

## Task 6: Review the governance controls

Walk through these with your trainer. You don't need to change anything - just know where each control lives.

| Control | Where | What it does |
|---|---|---|
| **Data policies (DLP)** | Power Platform admin center | Allow/block connectors per environment. A blocked tool shows as unavailable in **Add a tool**. |
| **Knowledge sources for agents** | Power Platform admin center | Allow or block file upload, SharePoint/OneDrive, public websites as knowledge |
| **Environment strategy** | Power Platform admin center | Separate Dev / Test / Prod environments; move agents with **solutions** and pipelines |
| **Agent approval & availability** | Microsoft 365 admin center > Agents | Who sees which agent; approve/reject requests; agent inventory |
| **Agent Builder sharing** | Microsoft 365 admin center | Who can share Agent Builder agents and how widely |
| **Copilot Credits** | Power Platform admin center | Capacity, consumption per agent, pay-as-you-go billing |
| **Audit** | Microsoft Purview | Agent interactions and admin actions in the audit log |

> **Question:** A maker built an amazing agent in the **default** environment with a connector to Dropbox. What could go wrong?
> **Answer:** Everyone has access to the default environment, the connector may move company data outside the tenant, and there's no Dev/Test/Prod path. That's why admins set **DLP policies** and give makers dedicated environments.

---

## Task 7: Clean up

If you used a shared or production tenant:

1. In Copilot Studio, open **Contoso Triage Agent** > **Agent management** and turn it **off** (it keeps consuming credits otherwise).
1. Delete the test agents you don't need: Contoso IT Helper (copy), Contoso Service Desk, Contoso License Advisor, Contoso Triage Agent.
1. Delete the **Request software approval** workflow.
1. In Microsoft 365 Copilot, delete the Agent Builder version of **Contoso IT Helper**.
1. Delete the **Contoso IT Hub** SharePoint site.

---

## Check your work

- [ ] A baseline evaluation and at least one re-run exist; you can explain the score difference.
- [ ] The agent works in Teams and in Microsoft 365 Copilot for a pilot user.
- [ ] You know where an admin approves the agent and where DLP policies live.

## Key takeaways

- **Evaluate → change → re-evaluate.** Treat agent quality like code quality.
- Release in waves: **publish → pilot → admin approval → org catalog**.
- Publishing an agent means publishing its **dependencies** too (workflows, connected agents, data access).
- Governance is shared: **Power Platform admin center** for building and connectors, **Microsoft 365 admin center** for availability.
- Autonomous agents cost money while nobody watches. Monitor them.

---

## 🎓 You made it

You went from a chat helper with three paragraphs of instructions to a governed, multi-agent, autonomous Service Desk:

```
Lab 01  instructions          ->  it talks
Lab 02  knowledge             ->  it knows (and cites)
Lab 03  code + skills         ->  it computes, the same way every time
Lab 04  Copilot Studio        ->  it plans
Lab 05  connectors + MCP      ->  it acts
Lab 06  workflow + approval   ->  it follows process, a human decides
Lab 07  skills, memory, A2A   ->  it delegates and remembers
Lab 08  triggers              ->  it starts on its own, within guardrails
Lab 09  evaluate + govern     ->  it's ready for production
```

**Back to:** [Lab list](../../README.md)
