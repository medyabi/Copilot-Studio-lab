---
lab:
    title: 'Lab 00: Prepare the lab environment'
    module: 'Module 0 - Setup'
---

# Lab 00 - Prepare the lab environment

## Lab introduction

Before we build anything, we need a place for our agents to live and some data for them to work with.

Think of it like a kitchen. Before you cook, you check the oven works and you put the ingredients on the counter. That's this lab. No agents yet - just the **ingredients**:

- a SharePoint site with four Contoso IT policies,
- a SharePoint list where employees log IT requests,
- a Copilot Studio environment you can build in.

Every lab after this one uses these ingredients, so take your time here.

## Estimated timing: 30 minutes

## Lab scenario

You work for **Contoso**, a company with offices in Casablanca, Paris, Madrid, Dubai and Lisbon. The IT Service Desk is drowning in repetitive questions ("How do I reset my password?", "How long does Visio take?") and in requests that someone has to read, classify and route by hand.

Your manager asks you to build agents that help. Step by step.

## Job skills

- Task 1: Check your licenses and access
- Task 2: Create the Contoso IT Hub SharePoint site and upload the policies
- Task 3: Create the IT Requests list
- Task 4: Check your Copilot Studio environment

---

## Task 1: Check your licenses and access

Why start here? Because 90% of "the lab doesn't work" problems are a missing license.

1. Sign in to [https://m365.cloud.microsoft](https://m365.cloud.microsoft) with your lab account.

1. In the left pane, look for **Agents** and **New agent**. If you see **New agent**, Agent Builder is available to you. ✅

1. Open a new browser tab and go to [https://copilotstudio.microsoft.com](https://copilotstudio.microsoft.com).

1. If you're asked to pick a country/region or start a trial, follow the prompts. When the **Home** page loads, you're in. ✅

1. Look at the environment picker in the top-right corner. Write down the environment name. You'll use it in Labs 03 to 09.

   > **Note:** Copying an agent from Agent Builder and building in Copilot Studio both need an environment **with a Dataverse database**. A personal developer environment or a dedicated "Agents-Lab" environment is perfect. Avoid building in the shared default environment in production tenants.

   > 📸 **Screenshot:** `lab00-01-copilot-studio-home.png` - Copilot Studio Home page with the environment picker.

---

## Task 2: Create the Contoso IT Hub SharePoint site

1. Go to [https://www.office.com/launch/sharepoint](https://www.office.com/launch/sharepoint) and select **+ Create site**.

1. Choose **Team site**, then the **Standard team** template, and select **Use template**.

1. Enter these values and select **Next**, then **Create site**:

   | Setting | Value |
   |---|---|
   | Site name | `Contoso IT Hub` |
   | Site description | `Policies and requests for the Contoso IT Service Desk` |
   | Privacy | Private |

1. When the site opens, select **Documents**, then create a folder named `IT Policies`.

1. Open the folder and upload the four files from **Allfiles/Labs/00**:

   - `Contoso-Laptop-and-Device-Policy.docx`
   - `Contoso-Software-Request-Policy.docx`
   - `Contoso-Remote-Access-and-VPN-Guide.docx`
   - `Contoso-Password-and-MFA-Policy.docx`

1. Copy the URL of the site (it looks like `https://<tenant>.sharepoint.com/sites/ContosoITHub`). Paste it into a notepad. You'll need it a lot.

> **Why do we upload to SharePoint and not straight into the agent?**
> Because SharePoint keeps the **permissions**. When an agent answers from SharePoint, a user only gets answers from files they're allowed to open. Upload a file into an agent, and everyone who uses the agent can read it. Remember this - it matters in Lab 09.

---

## Task 3: Create the IT Requests list

This list is the "inbox" of our Service Desk. In Lab 05 an agent writes into it. In Lab 08 an autonomous agent watches it.

1. On the **Contoso IT Hub** site, select **+ New** > **List** > **Blank list**.

1. Name it `IT Requests` and select **Create**.

1. Add the following columns (**+ Add column**). Type the choice values exactly as shown - the agents depend on them.

   | Column name | Type | Settings |
   |---|---|---|
   | `Description` | Multiple lines of text | |
   | `Category` | Choice | Hardware, Software, Network, Account & Access, Security |
   | `Priority` | Choice | Low, Normal, High, Critical (default: Normal) |
   | `Status` | Choice | New, Triaged, Waiting for approval, Approved, Rejected, Resolved (default: New) |
   | `AssignedTeam` | Choice | Endpoint, Apps, Network, Identity, SecOps |
   | `RequestedBy` | Person | |
   | `TriageNotes` | Multiple lines of text | |

1. Add one test item so the list isn't empty:

   | Field | Value |
   |---|---|
   | Title | `Docking station not detected` |
   | Description | `My Dell dock stopped detecting my second screen after the update.` |
   | Status | New |

   > 📸 **Screenshot:** `lab00-02-it-requests-list.png` - the IT Requests list with all columns.

---

## Task 4: Check your Copilot Studio environment

1. Back in Copilot Studio, select **Agents** in the left pane. You should see an empty list (or your existing agents).

1. Select **Home**. Notice the **Agent** and **Workflow** tiles. That's where we start in Lab 04.

1. Select the **...** at the bottom of the left pane. Notice **Open classic experience**. We won't use it, but now you know where the older, topic-based designer lives.

> **Two engines, one studio.** Copilot Studio runs agents on a **harness** - the runtime between your design and the AI model.
> - The **GitHub Copilot harness** (the default for new agents) takes a goal, plans the steps, calls tools, and recovers when something fails.
> - The **standard harness** (classic experience) follows the topics and paths you draw.
>
> Don't let the name confuse you: the GitHub Copilot harness is a Copilot Studio runtime. Your data isn't sent to the GitHub Copilot service.

---

## Check your work

- [ ] You can open **New agent** in Microsoft 365 Copilot.
- [ ] You can open Copilot Studio and you know your environment name.
- [ ] The **Contoso IT Hub** site has four policies in **Documents > IT Policies**.
- [ ] The **IT Requests** list exists with seven custom columns and one item.

## Key takeaways

- Agents are only as good as the data you give them. Clean, short, well-titled documents give better answers.
- SharePoint keeps permissions. Uploaded files don't.
- Copilot Studio needs an environment with Dataverse.
- New Copilot Studio agents run on the GitHub Copilot harness by default.

**Next:** [Lab 01 - Build your first agent with natural language](LAB_01-Build_your_first_agent_with_Agent_Builder.md)
