---
lab:
    title: 'Lab 04: Your first Copilot Studio agent'
    module: 'Module 2 - Copilot Studio fundamentals'
---

# Lab 04 - Your first Copilot Studio agent

## Lab introduction

Welcome to Copilot Studio. Same idea as Agent Builder - instructions, knowledge, capabilities - but with a much bigger toolbox: connectors, MCP servers, workflows, triggers, connected agents, evaluation, channels and environments.

New agents in Copilot Studio run on the **GitHub Copilot harness**. What does that mean for you as a builder?

- You don't draw conversation trees. You **describe** the agent and **connect** components.
- The runtime takes a goal, **plans** the steps, picks the tools and knowledge it needs, and **adapts** when a step fails.
- Everything you configure lives on one **Build** tab. You test on **Preview** and measure on **Evaluate**.

> **Cost reminder:** agents on this harness use **Copilot Credits** - also while you build, test and evaluate. Keep tests focused.

## Estimated timing: 45 minutes

## Lab scenario

The Service Desk is ready for a "real" agent: the **Contoso Service Desk** agent. In this lab it does what the IT Helper did (answer from policies, write ticket summaries). From Lab 05 it starts taking action.

## Job skills

- Task 1: Create the agent and write its instructions
- Task 2: Add SharePoint knowledge
- Task 3: Choose the model and review agent settings
- Task 4: Test on the Preview tab and read the activity trace

---

## Task 1: Create the agent and write its instructions

1. Go to [https://copilotstudio.microsoft.com](https://copilotstudio.microsoft.com) and check that the environment you chose in Lab 00 is selected (globe icon in the left rail).

1. On **Home**, select the **Agent** tile. (Or **Agents** > **New agent**.) The agent designer opens on the **Build** tab with the name field active.

   > 💡 You can also describe the agent in plain language and let the **agentic builder** (preview) propose a full solution. We build manually here so you see every part.

1. Name the agent `Contoso Service Desk`.

   > The name you type **before the first save** becomes the agent's internal schema name, which can't be changed later. Get it right now.

1. In **Instructions**, paste:

   ```text
   # Role
   You are the Contoso Service Desk agent. You help Contoso employees with IT questions and IT requests.

   # How you work
   1. Understand the request. If details are missing, ask at most three short questions
      (device, error message, since when, how many people affected).
   2. For policy questions (devices, software, remote access, passwords, MFA), answer ONLY
      from the Contoso IT policies knowledge and cite the document.
   3. If the answer is not in the policies, say so. Never guess a rule, a price or a deadline.
   4. When the user needs something done, prepare a ticket with:
      Title, Category (Hardware | Software | Network | Account & Access | Security),
      Priority (Low | Normal | High | Critical), Description.

   # Boundaries
   - Never ask for or repeat passwords, MFA codes or recovery keys.
   - Suspected phishing, an MFA prompt the user did not start, or a data leak:
     Category Security, Priority Critical, tell the user to call ext. 4357 now.
   - Only IT topics. For HR, finance or legal, politely redirect.

   # Style
   Friendly, short sentences, bullet points for lists. Answer in the user's language.
   ```

1. Select **Save**.

   > 📸 **Screenshot:** `lab04-01-build-tab-instructions.png`

> **Compare with Lab 01.** These are the same four blocks - role, steps, boundaries, style. In Copilot Studio, **instructions are the main way to control behavior**. There are no topics to fall back on. Clear instructions are everything.

---

## Task 2: Add SharePoint knowledge

1. On the **Build** tab, in the components panel on the right, select **Knowledge**.

1. In **Add knowledge**, choose **SharePoint**.

1. Paste the URL of the **Contoso IT Hub** site (`https://<your-tenant>.sharepoint.com/sites/ContosoITHub`) or browse to its **Documents** library, and confirm.

1. Give the source a clear **name** and **description**. This matters: the runtime reads descriptions to decide when to search a source.

   | Field | Value |
   |---|---|
   | Name | `Contoso IT policies` |
   | Description | `Official Contoso IT policies: laptops and devices, software requests and approval categories, remote access and VPN, passwords and MFA. Use for any question about Contoso IT rules.` |

1. Select **Add** and **Save** the agent.

> Notice what's **not** there: a "general knowledge" on/off switch. On this harness, you control what the agent knows by the sources you add. Web search is off unless you add **Public websites** and turn on **Search all websites**.

---

## Task 3: Choose the model and review agent settings

1. On the **Build** tab, find **Model** and open the list. Note the models your environment offers. Keep the default for now.

   > **Rule of thumb:** a bigger reasoning model for multi-step work with tools; a faster model for high-volume, simple Q&A. You can change it later and measure the impact in Lab 09.

1. Open **Agent settings** (the gear icon). Browse the four tabs:

   | Tab | What you'll find there |
   |---|---|
   | **Agent details** | Primary language, solution, schema name |
   | **AI & behavior** | Generative AI behavior options |
   | **Safety & access** | Authentication, content moderation |
   | **Greeting & prompts** | Welcome message and suggested prompts |

1. In **Greeting & prompts**, add three suggested prompts:

   - `How long does it take to get Visio?`
   - `I lost my laptop`
   - `My VPN shows a red cross`

1. In **Safety & access**, confirm that authentication is **Authenticate with Microsoft**. That's what makes SharePoint answers follow each user's permissions.

1. **Save**.

---

## Task 4: Test on the Preview tab and read the activity trace

1. Select the **Preview** tab.

1. Run these tests:

   | # | Message | Look for |
   |---|---|---|
   | 1 | `How long does it take to get Visio?` | Category B, manager approval, 2 business days + citation |
   | 2 | `Global Secure Access shows a red cross` | The troubleshooting checklist from the VPN guide |
   | 3 | `My screen flickers since yesterday, only me` | Max 3 questions, then a ticket with Category Hardware, Priority Normal |
   | 4 | `Someone called me and asked me to approve an MFA prompt` | Security / Critical / call ext. 4357 |

1. After test 1, open the **activity trace** (the agent's step-by-step view). Find:
   - the **plan** the agent made,
   - the **knowledge search** it ran and the query it used,
   - the documents it found.

   > 📸 **Screenshot:** `lab04-02-activity-trace.png`

> **Question:** Why bother with the activity trace when the answer looks right?
> **Answer:** Because "looks right" isn't proof. The trace shows *why* the agent answered - which source, which tool, which inputs. When something goes wrong in Lab 05-08 (and it will), the trace is the first place you look.

1. Optional: open the copy of **Contoso IT Helper** you made in Lab 03 and ask it test 1. Compare the answers and the traces.

---

## Check your work

- [ ] **Contoso Service Desk** exists, with instructions and the SharePoint knowledge source.
- [ ] Three suggested prompts are configured.
- [ ] You can explain what the activity trace shows for a knowledge question.

## Key takeaways

- New Copilot Studio agents run on the GitHub Copilot harness: describe, connect, let the runtime plan.
- **Names and descriptions** of knowledge (and later tools and agents) drive the runtime's choices. Write them for the model, not for humans.
- The **Preview** tab + **activity trace** is your debugger.
- Building and testing consume Copilot Credits. Test with purpose.

**Next:** [Lab 05 - Give your agent hands: connectors and MCP tools](LAB_05-Connectors_and_MCP_tools.md)
