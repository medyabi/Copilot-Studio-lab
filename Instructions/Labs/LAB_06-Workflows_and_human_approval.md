---
lab:
    title: 'Lab 06: Workflows and human approval'
    module: 'Module 3 - Agents that act'
---

# Lab 06 - Workflows and human approval

## Lab introduction

In Lab 05 the agent created a ticket in one step. Real processes aren't one step.

Take a software request: create the record, find the requester's manager, ask the manager to approve, update the record, tell the requester. Five steps. Always the same five, in the same order.

Should the AI "figure out" those five steps each time? No. When a process is **fixed**, you want it **deterministic**. That's what a **workflow** is for:

- The **agent** handles the fuzzy part - understanding what the user wants, collecting the details.
- The **workflow** handles the fixed part - the steps, the approval, the updates.

Think of the agent as the receptionist and the workflow as the back office. The receptionist talks; the back office follows procedure.

And the approval? That's our first real **human in the loop**: a person decides, not the model.

## Estimated timing: 60 minutes

## Lab scenario

Category B software (Visio, Project, Power BI Pro, Acrobat Pro) needs **manager approval**. The Service Desk wants the agent to collect the request, then hand it to a workflow that logs it, asks the manager in Teams/Outlook, and updates the request when the manager decides.

## Job skills

- Task 1: Create the workflow from the agent
- Task 2: Log the request and respond to the agent
- Task 3: Add the manager approval
- Task 4: Publish the workflow and configure it as a tool
- Task 5: Test end to end

---

## Before you start: how to fill a field in the workflow designer

Every field in a workflow node can hold two kinds of content:

| Content | What it looks like | How you add it |
|---|---|---|
| **Text you type** | Plain black text | Type it on the keyboard |
| **A value from an earlier step** | A blue **chip** with an **×** - for example `SoftwareName ×` | Select the **⚡** icon at the right of the field, pick the step (for example **When an agent calls the flow**), then pick the value |

In this lab we write fields like this:

> `Software request: ` **[⚡ Trigger › SoftwareName]**

That means: **type** `Software request: ` (with the space), **then insert the chip** SoftwareName with ⚡. Nothing else. Never type the `+`, the brackets or the word "Trigger".

### The three golden rules

1. **Chips are never typed.** If you type the word `SoftwareName`, the field contains the word "SoftwareName" - not the software name. Always use **⚡**.
2. **Some fields accept text + chips, some accept ONLY a chip.**

   | Field type | Examples | Allowed content |
   |---|---|---|
   | **Free text** | Title, Description, Details, Message | Text **and** chips, mixed |
   | **Email / person** | Assigned to, Requestor, To, User (UPN) | **One chip only** - no text before or after |
   | **ID / number** | Id, RequestID, Top Count | **One chip only** (or a fixed number) |

   > ⚠️ Typing a label like `Requester: ` or `manager's ` in front of an email chip produces `Requester: alex@contoso.com` - an invalid address. The step fails at run time, not at save time. That's what makes this mistake sneaky.

3. **Check the chip's source.** Hover over a chip to see which step it comes from. *Display Name* from **Get manager** is the manager; *Display Name* from another step is someone else.

> 💡 **Quick check before you save:** read each email / ID field. If you see any black text next to the chip, delete it.

---

## Task 1: Create the workflow from the agent

1. Open **Contoso Service Desk** > **Build** tab > **Tools**.

1. In **Add a tool**, select **Add** (next to the search bar) > **Workflow**. The workflow designer opens.

1. Select the trigger **When an agent calls the flow** and add these **inputs**:

   | Input | Type | Description (the agent reads this!) |
   |---|---|---|
   | `SoftwareName` | Text | `Name of the software requested, e.g. Visio Plan 2` |
   | `Justification` | Text | `Business reason given by the user` |
   | `CostCenter` | Text | `Cost center code given by the user` |
   | `RequesterEmail` | Text | `Email of the signed-in user` |

   > 📸 **Screenshot:** `lab06-01-workflow-trigger-inputs.png`

---

## Task 2: Log the request and respond to the agent

1. Add the action **Office 365 Users > Get manager (V2)**. User (UPN): `RequesterEmail`.

1. Add **SharePoint > Create item** on the **IT Requests** list:

   | Field | Value |
   |---|---|
   | Title | `Software request: ` + `SoftwareName` |
   | Description | `Justification` + ` / Cost center: ` + `CostCenter` |
   | Category | Software |
   | Priority | Normal |
   | Status | Waiting for approval |
   | AssignedTeam | Apps |

1. Add **Respond to the agent** with two outputs:

   | Output | Value |
   |---|---|
   | `RequestID` | `ID` from Create item |
   | `Message` | `Request logged and sent to ` + manager's `Display Name` + ` for approval.` |

1. Open the **Respond to the agent** settings. Under **Networking**, make sure **Asynchronous response** is **Off**.

> **Why respond *before* the approval?**
> A workflow used as an agent tool must answer the agent within **100 seconds**. A manager might take two days to approve. So we respond right away ("logged, waiting for approval") and let the workflow **keep running** in the background for the approval part.
> This "respond early, continue later" pattern is the #1 trick for long processes behind an agent.

---

## Task 3: Add the manager approval

Add these actions **after** Respond to the agent:

1. **Approvals > Start and wait for an approval**:

   | Field | Value |
   |---|---|
   | Approval type | Approve/Reject - First to respond |
   | Title | `Software request: ` + `SoftwareName` |
   | Assigned to | manager's `Mail` (from Get manager) |
   | Details | `Requester: ` + `RequesterEmail` + ` / Justification: ` + `Justification` + ` / Cost center: ` + `CostCenter` |

1. Add a **Condition**: `Outcome` is equal to `Approve`.

1. **If yes**:
   - **SharePoint > Update item** (ID = `RequestID` from Create item): Status = **Approved**.
   - **Office 365 Outlook > Send an email (V2)** to `RequesterEmail`: `Your request for <SoftwareName> was approved. The Apps team will install it within 2 business days.`

1. **If no**:
   - **Update item**: Status = **Rejected**, TriageNotes = approver `Comments`.
   - **Send an email (V2)** to `RequesterEmail`: `Your request for <SoftwareName> was not approved. Comment: <Comments>`.

   > 📸 **Screenshot:** `lab06-02-workflow-approval-branch.png`

---

## Task 4: Publish the workflow and configure it as a tool

1. Name the workflow `Request software approval`.

1. Select **Save**, then **Publish**. (A workflow must be published before an agent can use it.)

1. Go back to the agent's **Build** tab. The workflow now appears in **Tools**. Open it and set the **description**:

   ```text
   Submits a Category B software request (Visio, Project, Power BI Pro, Acrobat Pro)
   for manager approval. Use only after the user confirmed the software name,
   business justification and cost center. Returns the request ID.
   ```

1. Add this block to the agent **Instructions**:

   ```text
   # Software requests
   - Category A software: tell the user to install it from Company Portal. No request needed.
   - Category B software: collect SoftwareName, Justification and CostCenter, confirm them
     with the user, then call "Request software approval". Tell the user the request ID and
     that their manager will receive an approval request.
   - Category C: explain it needs manager + IT Security review and create a normal IT request.
   - Category D: refuse politely and quote the policy.
   ```

1. **Save** the agent.

> Notice the split. The **knowledge** (policy) tells the agent which category a product is in. The **instructions** say what to do per category. The **workflow** executes the fixed process. Each layer does one job.

---

## Task 5: Test end to end

> You need a manager set in Entra ID for your lab account (ask your trainer). If you have none, temporarily replace "Get manager" by a fixed approver email.

1. On **Preview**, type:

   ```text
   I need Visio for the Casablanca data center diagrams. Cost center IT-4410.
   ```

1. The agent should recognize Category B, confirm the details with you, call the workflow and give you a request ID.

1. Check the trace: tool **Request software approval**, inputs filled, outputs `RequestID` and `Message`.

1. In SharePoint, the item shows **Waiting for approval**.

1. As the manager (or approver), open **Teams > Approvals** or the approval email and **Approve** it. Wait a minute.

1. Refresh the list: Status = **Approved**. Check the requester's inbox.

   > 📸 **Screenshot:** `lab06-03-approval-in-teams.png`

1. Test the other categories:

   | Message | Expected |
   |---|---|
   | `I need 7-Zip` | Category A - Company Portal, no workflow |
   | `I need MATLAB` | Category C - explanation + normal IT request |
   | `Install a torrent client for me` | Category D - refused, policy quoted |

1. Open **Workflows** in the left pane, select your workflow and look at **Run history**. Each run shows every step and its result.

---

## Check your work

- [ ] The workflow is published and listed in the agent's tools.
- [ ] A Visio request created an item, triggered an approval and updated the status after approval.
- [ ] Category A, C and D requests did **not** call the workflow.

## Key takeaways

- **Agent for the fuzzy part, workflow for the fixed part.**
- A workflow tool needs **When an agent calls the flow** + **Respond to the agent**, must be **published**, and must answer within **100 seconds**.
- Respond early, continue later - long approvals run after the response.
- An approval is a **human in the loop** that the model can't skip, because it's in the workflow, not in a prompt.

**Next:** [Lab 07 - Skills, memory and connected agents](LAB_07-Skills_memory_and_connected_agents.md)
