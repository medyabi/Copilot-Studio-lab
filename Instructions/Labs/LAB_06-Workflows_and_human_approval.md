---
lab:
    title: 'Lab 06: Workflows and human approval'
    module: 'Module 3 - Agents that act'
---

# Lab 06 - Workflows and human approval

## Lab introduction

In Lab 05 the agent created a ticket in one step. Real processes aren't one step.

Take a software request. You create the record, find the requester's manager, ask the manager to approve, update the record and tell the requester. That's five steps, always the same five, in the same order.

Should the AI "figure out" those five steps every time? No. When a process is **fixed**, you want it **deterministic**. That's what a **workflow** is for:

- The **agent** handles the fuzzy part: understanding what the user wants and collecting the details.
- The **workflow** handles the fixed part: the steps, the approval and the updates.

Think of the agent as the receptionist and the workflow as the back office. The receptionist talks. The back office follows procedure.

And the approval? That's our first real **human in the loop**. A person decides, not the model.

## Estimated timing: 75 minutes

## Lab scenario

Category B software (Visio, Project, Power BI Pro, Acrobat Pro) needs **manager approval**. The Service Desk wants the agent to collect the request and hand it to a workflow. The workflow logs the request, asks the manager to approve it in Outlook or Teams, and updates the request when the manager decides.

## Before you begin

> ⚠️ **Your account needs a manager, and that manager needs a mailbox.**
>
> The workflow looks up your manager in Microsoft Entra ID and sends them the approval. Check two things in the [Microsoft 365 admin center](https://admin.microsoft.com) > **Users** > **Active users**:
>
> 1. Your lab account > **Account** tab > **Manager** is set (in this lab: *Julian Duncan*).
> 2. The manager's account has a **license with Exchange Online** (**Mail** tab shows mailbox storage).
>
> No manager? The **Get manager** step fails. Manager without a mailbox? The approval is created, but nobody can open it. See [Troubleshooting](#troubleshooting) at the end.

## Job skills

- Task 1: Create the workflow and its inputs
- Task 2: Find the manager, log the request and answer the agent
- Task 3: Add the manager approval and the two branches
- Task 4: Publish the workflow and add it to the agent
- Task 5: Test end to end as requester **and** approver

---

## Before you start: how to fill a field in the workflow designer

Most mistakes in this lab come from one thing: **how you fill a field**. Read this part carefully. It saves you an hour of debugging.

Every field in a workflow node can hold two kinds of content:

| Content | What it looks like | How you add it |
|---|---|---|
| **Text you type** | Plain black text | Type it on the keyboard |
| **A value from an earlier step** | A blue **chip** with an **×**, for example `SoftwareName ×` | Select the **⚡** icon at the right of the field, pick the step (for example **When an agent calls the flow**), then pick the value |

In this lab, fields are written like this:

> `Software request: ` **[⚡ Trigger › SoftwareName]**

That means: **type** `Software request: ` (with the space), **then insert the chip** SoftwareName with ⚡. Nothing else. Never type the `+`, the brackets or the word "Trigger".

### The four golden rules

1. **Chips are never typed.** If you type the word `RequesterEmail`, the field contains the word "RequesterEmail", not the email. Always use **⚡**.

2. **Some fields accept text + chips. Others accept ONLY a chip.**

   | Field type | Examples | Allowed content |
   |---|---|---|
   | **Free text** | Title, Description, Details, Message, Subject, Body | Text **and** chips, mixed |
   | **Email / person** | Assigned to, Requestor, To, User (UPN) | **One chip only**, no text before or after |
   | **ID / number** | Id, RequestID | **One chip only** |

   > ⚠️ Typing a label like `Requester: ` or `manager's ` in front of an email chip produces `Requester: alex@contoso.com`, which is an invalid address. The step fails at **run time**, not at save time. That's what makes this mistake sneaky.

3. **Check the chip's source.** Hover over a chip to see which step it comes from. *Display Name* from **Get manager** is the manager. *Display Name* from another step is someone else.

4. **Leave a space on each side of a chip in text.** `Your request for` **[⚡ SoftwareName]** `was approved.` If you forget the spaces, the email reads *"Your request forMicrosoft Visiowas approved."*

> 💡 **Rich-text fields (like an email Body)** show the chip with its step name, for example **When an agent calls the flow, SoftwareName**. That's normal. It's still one chip and it still inserts only the value.

> 💡 **Quick check before you save:** read every email / ID field. If you see any black text next to the chip, delete it.

---

## Task 1: Create the workflow and its inputs

1. Open **Contoso Service Desk** > **Build** tab. On the right, next to **Tools**, select **+**.

   ![Build tab with the Tools section](media/lab06-01-build-tools.png)

1. In **Add a tool**, **don't** search for "workflow" in the list. The results are third-party connectors (Nintex, Datablend...) that happen to have "workflow" in their name. That's the trap.

   Instead, select **Add** (next to the search bar) > **Workflow**.

   ![Add a tool - use Add > Workflow, not the search results](media/lab06-02-add-workflow.png)

   > If you see *"You have unsaved changes"*, select **Save draft** first.

1. The workflow designer opens with two nodes already in place: **When an agent calls the flow** (the trigger) and **Respond to the agent**. On the left is the **Add** panel with every building block.

   ![A new workflow with trigger and response](media/lab06-03-new-workflow.png)

   Why are these two nodes there? Because a workflow used by an agent **must** start with the agent calling it and **must** answer the agent. Everything you build goes between (and after) them.

1. Select the trigger. Under **Inputs**, select **Add an input** and choose **Text**.

   ![Input types](media/lab06-04-input-types.png)

1. Add four **Text** inputs. The description is not decoration. **The agent reads it** to decide what to put in each input.

   | Input | Type | Description |
   |---|---|---|
   | `SoftwareName` | Text | `Name of the software requested, e.g. Visio Plan 2` |
   | `Justification` | Text | `Business reason given by the user` |
   | `CostCenter` | Text | `Cost center code given by the user` |
   | `RequesterEmail` | Text | `Email of the signed-in user` |

   ![Four trigger inputs](media/lab06-05-trigger-inputs.png)

---

## Task 2: Find the manager, log the request and answer the agent

### Get the manager

1. Select the **+** between the trigger and **Respond to the agent**. Search `get manager` and pick **Office 365 Users** > **Get manager**.

   ![Search Get manager](media/lab06-06-search-get-manager.png)

1. The node opens with a **Connection**. If you see *"Something went wrong with the connection"*, open the connection drop-down and select your account again (or **Create new connection**) until the dot is green.

   ![Connection error on Get manager](media/lab06-07-connection-error.png)

1. **User (UPN)** = **[⚡ Trigger › RequesterEmail]**. **One chip, nothing else.** (Golden rule #1.)

   ![User (UPN) holds only the RequesterEmail chip](media/lab06-08-get-manager-chip.png)

### Log the request in SharePoint

1. Select **+** after Get manager. Choose **SharePoint**, search `create item`, select **Create item**.

   ![SharePoint Create item](media/lab06-09-sharepoint-create-item.png)

1. Site address = **Contoso IT Hub**, List name = **IT Requests**. Fill the item:

   | Field | Type | Value |
   |---|---|---|
   | Title | Free text | `Software request: ` **[⚡ Trigger › SoftwareName]** |
   | Description | Free text | **[⚡ Trigger › Justification]** ` / Cost center: ` **[⚡ Trigger › CostCenter]** |
   | Category | Choice | Software |
   | Priority | Choice | Normal |
   | Status | Choice | Waiting for approval |
   | AssignedTeam | Choice | Apps |

   ![Create item with chips](media/lab06-10-create-item-chips.png)

### Answer the agent

1. Select **Respond to the agent**. Under **Outputs**, add two outputs of type **Text**:

   ![Output types](media/lab06-11-output-types.png)

   | Output name | Value |
   |---|---|
   | `RequestID` | **[⚡ Create item › ID]** (one chip) |
   | `Message` | `Request logged and sent to ` **[⚡ Get manager › Display Name]** ` for approval.` |

   > Rename the second output from **Text** to `Message`. The output name is what the agent sees, and "Text" tells it nothing.

   To find *Display Name*, select ⚡ and type `get mana` in the picker search: only the **Get manager** values show.

   ![Dynamic content picker filtered on Get manager](media/lab06-12-dynamic-picker.png)

   ![Respond to the agent - two outputs](media/lab06-13-respond-outputs.png)

> **Why answer the agent *before* the approval?**
> A workflow used as a tool must answer the agent within **100 seconds**. A manager might take two days to approve. So we answer right away ("logged, waiting for approval") and the workflow **keeps running** in the background for the approval.
> This **respond early, continue later** pattern is the #1 trick for long processes behind an agent.

---

## Task 3: Add the manager approval and the two branches

Everything in this task goes **after** Respond to the agent.

### The approval

1. Select **+** after **Respond to the agent**. Search `approvals` and pick **Standard approvals** > **Start and wait for an approval**.

   ![Approvals actions](media/lab06-14-approvals-search.png)

1. Fill it in:

   | Field | Type | Value |
   |---|---|---|
   | Approval type | Choice | **Approve/Reject - First to respond** |
   | Title | Free text | `Software request: ` **[⚡ Trigger › SoftwareName]** |
   | Details | Free text | `Requester: ` **[⚡ RequesterEmail]** (new line) `Justification: ` **[⚡ Justification]** (new line) `Cost center: ` **[⚡ CostCenter]** |
   | Assigned to | **Email - one chip** | **[⚡ Get manager › Mail]** |
   | Requestor | **Email - one chip** | **[⚡ Trigger › RequesterEmail]** |

   🔍 **Spot the mistake.** This is what a first attempt looked like. Can you see the two errors?

   ![Approval - wrong](media/lab06-15-approval-wrong.png)

   Found them? **Assigned to** says `manager's Mail` and **Requestor** says `Requester: RequesterEmail`. Both are email fields with text in front of the chip, which breaks golden rule #2. Here's the fixed version:

   ![Approval - right](media/lab06-16-approval-right.png)

### Decide: approved or not?

1. Select **+** after the approval and add **If/Else**.

1. Condition: Property = **[⚡ Start and wait for an approval › Outcome]**, Operator = **Equals**, Value = `Approve` (typed).

   ![If/Else on Outcome](media/lab06-17-ifelse.png)

   Notice the line at the bottom: *An "Else" branch is automatically created.* You don't add the "no" branch, it's already there.

### Update the request

1. In the **If** branch, add **SharePoint** > **Update item**: same site and list, **Id** = **[⚡ Create item › ID]** (one chip), **Status** = **Approved**.

   ![Update item - Approved](media/lab06-18-update-approved.png)

1. In the **Else** branch, add another **Update item** (it's named *Update item 2*): Id = **[⚡ Create item › ID]**, **Status** = **Rejected**, **Triage notes** = `Rejected by manager`.

   ![Update item 2 - Rejected](media/lab06-19-update-rejected.png)

### Tell the requester

1. After **Update item**, add an email. Search `send an email`.

   ![Send an email search](media/lab06-20-send-email-search.png)

   ⚠️ Two actions look similar:

   | Action | Sends from | Use it? |
   |---|---|---|
   | **Mail** > Send an email notification | A shared Microsoft no-reply address, needs its own connection | ❌ |
   | **Office 365 Outlook** > **Send an email** | **Your** mailbox, with your existing connection | ✅ |

   ![Mail notification - not this one](media/lab06-21-mail-notification-wrong.png)

1. Pick **Office 365 Outlook** > **Send an email**:

   | Field | Type | Value |
   |---|---|---|
   | To | **Email - one chip** | **[⚡ Trigger › RequesterEmail]** |
   | Subject | Free text | `Approved: ` **[⚡ SoftwareName]** |
   | Body | Free text | `Your request for ` **[⚡ SoftwareName]** ` was approved. The Apps team will install it within 2 business days.` |

   ![Outlook Send an email - approved](media/lab06-22-outlook-send-email.png)

1. After **Update item 2** (Else), add **Send an email** again (*Send an email 2*): Subject = `Not approved: ` **[⚡ SoftwareName]**, Body = `Your request for ` **[⚡ SoftwareName]** ` was not approved. Contact your manager for details.`

   ![Body chip shows the step name - that's normal](media/lab06-23-body-chip-label.png)

1. Zoom out. Your workflow should look like this:

   ![The two branches](media/lab06-24-canvas-branches.png)

---

## Task 4: Publish the workflow and add it to the agent

1. Rename the workflow: select **Untitled workflow** at the top and type `Request software approval`.

1. Select **Publish**. Wait for the badge to change from **Draft** to **Published**.

   ![Publishing](media/lab06-25-publishing.png)

   ![Published - full workflow](media/lab06-26-published.png)

   > A workflow must be **published** before an agent can use it. Edit it later? Publish again.

1. Back in the agent's **Build** tab, **Tools** > **+**. Your workflow now appears in the list (only workflows that start with *When an agent calls the workflow* show here). Select it.

   ![Adding the workflow as a tool](media/lab06-27-add-workflow-tool.png)

   ![Request software approval added](media/lab06-28-tool-added.png)

1. Select the **Request software approval** chip to open **Workflow details**:

   - **Description for AI**:

     ```text
     Submits a Category B software request (Visio, Project, Power BI Pro, Acrobat Pro)
     for manager approval. Use only after the user confirmed the software name,
     business justification and cost center. Returns the request ID.
     ```

   - **Inputs**: leave all four on **Fill with AI**. For **RequesterEmail**, select **Improve AI fill** and add: `Use the email of the signed-in user. Never ask the user for it.`
   - **Permissions**: **Always allow**.
   - **Save**.

   ![Workflow details](media/lab06-29-workflow-details.png)

1. Add this block to the agent **Instructions** (under *How you work*):

   ```text
   # Software requests
   - Category A software: tell the user to install it from Company Portal. No request needed.
   - Category B software: collect SoftwareName, Justification and CostCenter, confirm them
     with the user, then call "Request software approval". Tell the user the request ID and
     that their manager will receive an approval request.
   - Never create a software request with "Create IT request". If "Request software approval"
     fails, tell the user it failed and stop. Do not try another tool.
   - Category C: explain it needs manager + IT Security review and create a normal IT request.
   - Category D: refuse politely and quote the policy.
   ```

   ![Software requests instructions](media/lab06-30-instructions.png)

1. Select the **Create IT request** tool and add this sentence to its **Description for AI**: `Never use for software requests: Category B software goes through "Request software approval".`

1. **Save** the agent.

> Notice the split. The **knowledge** (policy) tells the agent which category a product is in. The **instructions** say what to do per category. The **workflow** runs the fixed process. Each layer does one job.
>
> Why the "never use Create IT request" lines? See [Spot the bypass](#-spot-the-bypass) below. Without them, the agent may skip your approval.

---

## Task 5: Test end to end as requester and approver

### As the requester

1. On **Preview**, turn on **Debug mode** and type:

   ```text
   I need Visio for the Casablanca data center diagrams. Cost center IT-4410.
   ```

1. The agent finds the policy (Category B), then shows you the details and asks you to confirm.

   ![Agent asks to confirm](media/lab06-31-confirm-details.png)

1. Reply `yes`. The trace shows **Used tool: Request software approval**, and the agent gives you the **Request ID** and your manager's name.

   ![Request submitted](media/lab06-32-request-submitted.png)

1. Open the **IT Requests** list. Your item shows **Status = Waiting for approval**.

   ![Item waiting for approval](media/lab06-33-item-waiting.png)

### As the approver

1. Open an **InPrivate / incognito** window and sign in to [Outlook](https://outlook.office.com) as the **manager**.

1. Open the email **Software request: Microsoft Visio** from Microsoft Power Automate. It shows the details and two buttons.

   ![Approval email](media/lab06-34-approval-email.png)

   > The same approval also appears in **Teams** > **Apps** > **Approvals**. Approve in either place, it's the same approval.

1. Select **Approve**, type an optional comment and select **Submit**.

   ![Approve with comment](media/lab06-35-approve-comments.png)

   ![Approved](media/lab06-36-approved.png)

### Check the result

1. Back in **your** inbox: *Approved: Microsoft Visio*.

   ![Requester email](media/lab06-37-requester-email.png)

1. Refresh the **IT Requests** list: **Status = Approved**.

   ![Item approved](media/lab06-38-item-approved.png)

1. Open the workflow > **Activity** tab and select the latest run. Every step on the **If** path is green with its duration (the approval shows the time the manager took). The **Else** steps show **Skipped**.

   ![Run history - succeeded](media/lab06-39-activity-succeeded.png)

   > The **▷ Test** button doesn't work for agent-triggered workflows: *"This workflow runs when an app or agent calls it. To test it, run the app or agent that uses this workflow."* Test from the agent, then read the **Activity** tab. To retry one failing step with real data, open it and use **Run node**.

1. **Optional:** run one more request (for example *Power BI Pro*) and have the manager select **Reject**. Check the item shows **Rejected** and the requester gets *Not approved*.

1. Test the other categories:

   | Message | Expected |
   |---|---|
   | `I need 7-Zip` | Category A: Company Portal, no workflow |
   | `I need MATLAB` | Category C: explanation + normal IT request |
   | `Install a torrent client for me` | Category D: refused, policy quoted |

---

## 🔍 Spot the bypass

During testing, the workflow failed once (the *Get manager* error from golden rule #1). Here's what the agent did next:

![The agent used Create IT request instead](media/lab06-40-bypass-trace.png)

Read the trace: **Used tool: Create IT request**, then **Send request confirmation**. The agent didn't give up. It found **another way** to "help": it created a normal request with Status **New**, with no approval.

![Item created without approval](media/lab06-41-bypass-item.png)

Is that a bug? No, the orchestrator did its job: reach the goal with the tools it has. But from a process point of view, **the approval was skipped**.

The lesson:

- **Instructions are not guardrails.** "Category B needs approval" in the prompt is a wish, not a rule.
- Enforce rules where the model can't negotiate: a **tool description** that says *never use for software requests*, **fixed inputs**, **permissions**, and **one tool per process**.
- Always read the **trace**. The final answer looked fine; only the trace showed the wrong tool.

That's why Task 4 added the "never use Create IT request" lines.

---

## Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| Tool fails, Activity shows `Resource ' RequesterEmail' does not exist` | User (UPN) contains typed text, not a chip | Delete the text, insert **[⚡ RequesterEmail]** (golden rule #1). Publish again. |
| Get manager fails although the email is correct | The user has **no manager** in Entra ID | Set the manager in the admin center (**Option A**), or delete *Get manager* and set **Assigned to** to a fixed approver email (**Option B**) |
| The agent creates a normal IT request instead of calling the workflow | Bypass (see above) | Add the "never use Create IT request" instruction and tool description |
| Manager gets *Something went wrong* / `OwaUserHasNoMailboxAndNoLicenseAssignedException` in Outlook | The manager has **no license / mailbox** | Assign a license with Exchange Online, wait until the **Mail** tab shows mailbox storage (5-30 min), then run a **new** request |
| Email reads *"forMicrosoft Visiowas approved"* | No spaces around the chip | Golden rule #4 |
| The workflow isn't in the **Add a tool** list | Not published, or wrong trigger | Publish; the trigger must be *When an agent calls the workflow* |
| You picked a Nintex/Datablend "workflow" | Searched instead of using **Add > Workflow** | Remove it; use **Add > Workflow** |

---

## Check your work

- [ ] The workflow **Request software approval** is **published** and listed in the agent's tools.
- [ ] A Visio request created an item with **Waiting for approval**.
- [ ] The manager received the approval and approved it.
- [ ] The item changed to **Approved** and the requester got the email.
- [ ] The **Activity** tab shows a **Succeeded** run with the Else branch **Skipped**.
- [ ] Category A, C and D requests did **not** call the workflow.

## Key takeaways

- **Agent for the fuzzy part, workflow for the fixed part.**
- A workflow tool needs **When an agent calls the flow** + **Respond to the agent**, must be **published**, and must answer within **100 seconds**. So: **respond early, continue later**.
- Fill fields carefully: **chips are never typed**, **email and ID fields take one chip only**, and **text needs spaces around chips**.
- Test from the agent, debug in the workflow's **Activity** tab.
- An approval is a **human in the loop** the model can't skip, because it lives in the workflow. But the agent can still **route around** the workflow if another tool lets it. Close that door.

**Next:** [Lab 07 - Skills, memory and connected agents](LAB_07-Skills_memory_and_connected_agents.md)
