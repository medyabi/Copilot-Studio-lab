---
lab:
    title: 'Lab 03: Capabilities, skills and the jump to Copilot Studio'
    module: 'Module 1 - Agent Builder'
---

# Lab 03 - Capabilities, skills and the jump to Copilot Studio

## Lab introduction

So far our agent can **talk** and **read**. Now we let it **work**.

Agent Builder gives you three ways to add muscle without writing code:

- **Code interpreter** ("Create documents, charts, and code") - the agent writes and runs Python in a sandbox to analyze data, build charts and create files.
- **Image generator** ("Create images") - the agent creates images from a prompt.
- **Skills** - a packaged, repeatable procedure (a `SKILL.md` file plus optional scripts and resources) that the agent follows every time a matching task comes up.

Then we hit the ceiling. Agent Builder **can't call external systems** - it can't create a SharePoint item, send an email or start an approval. For that we need Copilot Studio. So at the end of this lab we carry the agent across.

## Estimated timing: 40 minutes

## Lab scenario

The Service Desk manager sends a CSV export of Q3 tickets every quarter and asks the same questions: which category is growing, where do we miss the SLA, which site is unhappy? She wants the IT Helper to answer these in seconds, the same way every time. She also wants every incoming request triaged with the same rules - category, priority, team.

## Job skills

- Task 1: Analyze data with code interpreter
- Task 2: Create a skill from a description
- Task 3: Upload a skill package
- Task 4: Copy the agent to Copilot Studio

---

## Task 1: Analyze data with code interpreter

1. Edit **Contoso IT Helper** and select **Configure**.

1. Under **Capabilities**, check that **Create documents, charts, and code** is **on**. Leave **Create images** on too.

1. Switch the right panel to **Preview**, select the **+** in the chat box and upload `Allfiles/Labs/03/Contoso-IT-Tickets-Q3-2026.csv`.

1. Ask:

   ```text
   Using this ticket export, give me:
   1. the number of tickets per category, as a bar chart,
   2. the SLA compliance rate (WithinSLA = Yes) per priority,
   3. the site with the lowest average SatisfactionScore.
   Keep the commentary to five bullet points.
   ```

1. Expand the code block the agent shows (if any). The agent wrote Python, ran it in a sandbox, and built the chart from the real numbers - it didn't "imagine" them.

   > 📸 **Screenshot:** `lab03-01-code-interpreter-chart.png`

1. Now ask: `Create an Excel file with one sheet per category and a summary sheet.` Download the file and open it.

> **Question:** Why use code interpreter instead of just asking the model to "count"?
> **Answer:** Language models are bad at arithmetic over 240 rows. Code is exact. When numbers matter, make the agent **compute**, not **guess**.

---

## Task 2: Create a skill from a description

Every quarter, the manager asks for the same report. Typing the same five-line prompt each time is the definition of a skill.

1. In the Agent Builder chat (left pane), paste:

   ```text
   Add a reusable skill called "quarterly-sla-report".
   When the user uploads a Contoso ticket export (CSV with TicketID, OpenedDate, Site,
   Category, Priority, ResolutionHours, WithinSLA, SatisfactionScore), always:
   1. compute tickets per category and per site,
   2. compute SLA compliance per priority and flag any priority below 85%,
   3. find the 3 slowest categories by median ResolutionHours,
   4. produce a one-page Word report with a title, a 5-bullet executive summary,
      the tables and one bar chart.
   Package it with clear instructions and the analysis script so the result looks the same every time.
   ```

1. Select **Configure**, expand **Skills**, and confirm that **quarterly-sla-report** was created. Select it and read its instructions.

1. In **Preview**, start a **New chat**, upload the CSV again and type only: `Quarterly report please.`

1. Check: did you get a Word document with the same structure you asked for? Run it a second time. Same structure? That's the point of a skill.

> If Agent Builder didn't create the skill, you can always upload a package instead (next task). Skills in Agent Builder are limited to **eight per agent**, 50 MB per package.

---

## Task 3: Upload a skill package

Skills are **portable**. The same package works in Agent Builder, in Copilot Studio (Lab 07), and in other tools that support the open Agent Skills format. Let's prove it.

1. Open `Allfiles/Labs/03/it-ticket-triage/SKILL.md` in any text editor and read it. Notice the structure:
   - a **front matter** with `name` and `description` (the description tells the agent *when* to use the skill),
   - the **rules** (category signals, priority rules, team mapping),
   - a strict **output format**.

1. In Agent Builder, **Configure** > **Skills** > **Add**, and upload `Allfiles/Labs/03/it-ticket-triage.zip`.

   > Always upload the **.zip**, never the `SKILL.md` alone.

1. Review the name, description and instructions, then switch to **Preview** and test:

   | Message | Expected |
   |---|---|
   | `Triage this: "I clicked a link in an email and now my browser opens strange pages"` | Category: Security, Priority: Critical, AssignedTeam: SecOps |
   | `Triage this: "Need Power BI Pro for the sales dashboard, no rush"` | Software, Low, Apps |
   | `Triage this: "Left my laptop in a taxi in Madrid 20 minutes ago"` | Hardware, **High** (lost device rule), Endpoint |

   > 📸 **Screenshot:** `lab03-02-skill-triage-result.png`

1. Select **Update** to save the agent.

---

## Task 4: Copy the agent to Copilot Studio

Here's the ceiling. Ask the agent: `Please create the ticket in the IT Requests list for me.` It can write the summary, but it can't create the item. Agent Builder doesn't author actions that call external services.

Time to move up.

1. Open the agent's **More options (...)** menu and select **Copy to Copilot Studio**.

1. Select the environment you noted in Lab 00 (it must have Dataverse). Wait for the copy to finish. Copilot Studio opens with your agent.

1. **Save** the agent in Copilot Studio so the snapshot is stored.

1. Look at what came across - and what didn't:

   | Copied | Not copied - what to do |
   |---|---|
   | Name, description, instructions, starter prompts, icon | - |
   | SharePoint files/folders/sites, websites | - |
   | | Uploaded (embedded) files - upload again |
   | | Email / Teams chats as knowledge - use the Outlook / Teams connectors |
   | | "Create documents, charts, and code" - turn on code interpreter in Copilot Studio |
   | | "Create images" - not supported in Copilot Studio |

   > 📸 **Screenshot:** `lab03-03-copied-agent-in-copilot-studio.png`

> **Important:** a copy is a **snapshot**, not a sync. Changes you make to the Agent Builder version later won't reach the Copilot Studio version, and the other way round. Your original agent keeps working in Microsoft 365 Copilot.

We'll keep this copy as a reference. In Lab 04 we build the "real" Service Desk agent in Copilot Studio from scratch, so you understand every piece.

---

## Check your work

- [ ] The agent produced a bar chart and an Excel file from the CSV.
- [ ] Two skills are attached: **quarterly-sla-report** and **it-ticket-triage**.
- [ ] A copy of **Contoso IT Helper** exists in Copilot Studio.

## Key takeaways

- **Code interpreter** makes the agent compute instead of guess.
- A **skill** = a repeatable procedure the agent loads only when the task matches its description.
- Skills are portable `.zip` packages with a `SKILL.md` at the root.
- Agent Builder stops at **actions**. When you need connectors, workflows, triggers or ALM, copy the agent to Copilot Studio.

**Next:** [Lab 04 - Your first Copilot Studio agent](LAB_04-Your_first_Copilot_Studio_agent.md)
