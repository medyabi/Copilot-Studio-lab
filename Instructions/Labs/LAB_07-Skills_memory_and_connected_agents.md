---
lab:
    title: 'Lab 07: Skills, memory and connected agents'
    module: 'Module 4 - Advanced agents'
---

# Lab 07 - Skills, memory and connected agents

## Lab introduction

Our Service Desk agent is getting big. Instructions for policies, for tickets, for software, for tools... Soon it will be one giant prompt that nobody dares to touch.

How do real teams handle this? The same way they handle big code: **split it into pieces**.

| Piece | What it gives you | Analogy |
|---|---|---|
| **Skill** | A focused procedure the agent loads only when needed | A checklist in a drawer |
| **Memory** | The agent remembers each user's preferences between conversations | A colleague who remembers you prefer French |
| **Connected agent** | A whole specialist agent your agent can delegate to | Transferring a call to the licensing expert |

## Estimated timing: 60 minutes

## Lab scenario

Three requests from the Service Desk manager:

1. "Triage must follow **exactly** our rules - the same rules you used in Lab 03."
2. "Users hate repeating that they're in Dubai or that they want answers in French."
3. "The licensing team built their own agent. Don't copy their knowledge - **ask their agent**."

## Job skills

- Task 1: Add the triage skill to the Service Desk agent
- Task 2: Turn on memory
- Task 3: Build and publish the License Advisor agent
- Task 4: Connect the License Advisor to the Service Desk agent
- Task 5: Test delegation

---

## Task 1: Add the triage skill

Remember the `it-ticket-triage` skill from Lab 03? We reuse the **exact same package**. That's portability.

1. Open **Contoso Service Desk** > **Build** tab > **Skills** > add a skill.

1. Choose **Upload** and select `Allfiles/Labs/03/it-ticket-triage.zip` (or the `SKILL.md` file, if your UI asks for a file).

1. Review the skill's name and description. The description is what the runtime matches against the user's request - read it as if you were the model.

1. Replace the triage part of your instructions. Find the line that starts with `4. When the user needs something done, prepare a ticket...` and change it to:

   ```text
   4. When the user needs something done, use the it-ticket-triage skill to set Category,
      Priority and AssignedTeam, then prepare the ticket summary.
   ```

1. **Save**.

   > 📸 **Screenshot:** `lab07-01-skill-added.png`

> **Also try:** **Generate with AI**. Describe a skill in one sentence (for example `A skill that writes a polite, two-line closure message for a resolved ticket`) and review what it produces. You can export any skill as Markdown and share it.

> **Built-in skills.** The GitHub Copilot harness already ships skills for reading CSV, Word, PDF, PowerPoint, Excel, HTML and Markdown files. That's why your agent can analyze an attached file without extra setup.

---

## Task 2: Turn on memory

1. On the **Build** tab, in the components panel, turn **Memory** on. **Save**.

1. On **Preview**, type:

   ```text
   For your info: I work from the Dubai office and I prefer answers in French.
   ```

1. Start a **new conversation** (not just a new message) and type: `My laptop battery dies after one hour.`

1. Does the agent answer in French? Does it take Dubai into account if relevant?

> **What you should know about memory (preview):**
> - Each user has a **separate, private** memory. You, the maker, can't read it.
> - Memories are deleted if a user doesn't talk to the agent for **28 days**.
> - Memory is off in group chats and Teams channels.
> - Turning memory off stops the agent from *using* memories, it doesn't delete them.
>
> **Question:** Would you turn on memory for an HR agent that handles sick leave?
> **Answer:** Think twice. Memory is great for preferences, risky for sensitive data. Decide per agent - and tell users in the greeting that the agent remembers.

---

## Task 3: Build and publish the License Advisor agent

1. Go to **Agents** > **New agent**. Name it `Contoso License Advisor`.

1. **Instructions**:

   ```text
   You are the Contoso License Advisor. You answer questions about which Microsoft licenses
   Contoso owns, who is eligible, the approval needed and the monthly charge to the cost center.
   Answer only from the Contoso License Catalog. Always state the monthly charge and the approval rule.
   If a license is not in the catalog, say so and suggest contacting the Licensing team (licensing@contoso.com).
   ```

1. **Knowledge** > drag and drop `Allfiles/Labs/07/Contoso-License-Catalog.docx`. Name: `Contoso License Catalog`.

1. Test on **Preview**: `How much does a Copilot license cost my cost center?` → EUR 28 per month, manager approval.

1. Open **Agent settings** and make sure other agents are **allowed to connect** to this agent.

1. Select **Publish**. (A connected agent must be **published** - the parent always uses the latest *published* version.)

   > 📸 **Screenshot:** `lab07-02-license-advisor-published.png`

---

## Task 4: Connect the License Advisor

1. Open **Contoso Service Desk** > **Build** tab > **Connected agents** > **Add a connected agent**.

1. Select **Contoso License Advisor**.

1. Edit the **description** so it's clear for the parent's runtime when to delegate:

   ```text
   Specialist for Microsoft license questions at Contoso: which licenses exist,
   eligibility, approval rules and the monthly charge to a cost center.
   Delegate any question about license cost, eligibility or license reclaim.
   ```

1. **Save**.

> **Connected agents can come from anywhere.** From the same dialog you can connect agents built in Copilot Studio, in **Microsoft Foundry**, with the **Microsoft 365 Agents SDK**, or any remote agent that speaks **A2A** (Agent2Agent protocol). That's how you build a multi-agent system across teams and platforms.

> **Security note:** sharing the parent agent lets it call the connected agent for its users. But **data access** still follows each tool's connection. If the connected agent's tools use *author* authentication, users could see data through the parent that they couldn't see directly. Prefer **user authentication** for connected agents' tools.

---

## Task 5: Test delegation

1. On **Preview** of **Contoso Service Desk**, run:

   | # | Message | Expected route |
   |---|---|---|
   | 1 | `How much would Power BI Pro cost my cost center per month?` | Delegates to **License Advisor** → EUR 13, manager approval |
   | 2 | `I need Visio. Cost center IT-4410, it's for DC diagrams. How much will my cost center pay?` | License Advisor (EUR 14) **and** the Request software approval workflow |
   | 3 | `Triage: the whole Lisbon office has no Wi-Fi` | **Skill**: Network / Critical (>10 people) / Network team |
   | 4 | `Is my password policy 14 characters?` | Own knowledge - **no** delegation |

1. Open the **activity trace** for test 2. You should see the plan with several steps: delegation to the connected agent, then the workflow tool. One question, two specialists, one answer.

   > 📸 **Screenshot:** `lab07-03-trace-multi-agent.png`

> **Question:** When should you build a connected agent instead of just adding the knowledge to your agent?
> **Answer:** When another team **owns** that knowledge and its rules, when it needs its **own** tools and permissions, or when **several** parent agents need it. One owner, one agent, many consumers.

---

## Check your work

- [ ] The it-ticket-triage skill is attached and test 3 returns the exact output format.
- [ ] Memory is on and a new conversation remembers your language preference.
- [ ] **Contoso License Advisor** is published and connected; test 2 shows delegation + workflow in one plan.

## Key takeaways

- **Skills** keep instructions small and procedures consistent - and they travel between Agent Builder and Copilot Studio.
- **Memory** is per user, private, and should be a deliberate choice.
- **Connected agents** let each team own its specialist agent. The parent's description of the child drives delegation.
- Delegation doesn't bypass data permissions - the connection identity of each tool does.

**Next:** [Lab 08 - Build an autonomous agent with triggers](LAB_08-Build_an_autonomous_agent.md)
