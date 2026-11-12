---
lab:
    title: 'Lab 01: Build your first agent with natural language'
    module: 'Module 1 - Agent Builder'
---

# Lab 01 - Build your first agent with natural language

## Lab introduction

What is an agent, really?

Strip away the marketing and an agent built in Agent Builder is three things:

1. **Instructions** - who the agent is and how it behaves,
2. **Knowledge** - what it knows (Lab 02),
3. **Capabilities** - what extra things it can do (Lab 03).

Microsoft calls this a **declarative agent**: you *declare* what you want, and Microsoft 365 Copilot does the heavy lifting (the model, the orchestration, the security). No code. No servers.

In this lab we only touch the first ingredient - instructions. You'll be surprised how far that alone takes you.

## Estimated timing: 30 minutes

## Lab scenario

Contoso's Service Desk wants a friendly first-line helper in Microsoft 365 Copilot. For now it doesn't need to know the policies. It must greet people, figure out what kind of IT problem they have, ask the right follow-up questions, and write a clean ticket summary they can paste into a request.

## Job skills

- Task 1: Describe the agent in natural language
- Task 2: Review and improve the generated configuration
- Task 3: Test the agent on the Try it tab
- Task 4: Create the agent and use it in Copilot

---

## Task 1: Describe the agent in natural language

1. Go to [https://m365.cloud.microsoft](https://m365.cloud.microsoft) and open **Copilot**.

1. In the left pane, select **New agent** (in some tenants it sits under **Agents & Skills**). Agent Builder opens on the **Describe** tab.

   > 📸 **Screenshot:** `lab01-01-describe-tab.png`

1. In the chat box, paste this description and press **Enter**:

   ```text
   Create an agent called "Contoso IT Helper" for Contoso employees.
   It is the first-line helper of the IT Service Desk.
   It greets the user, works out whether the problem is about Hardware, Software,
   Network, Account & Access or Security, and asks at most three short follow-up
   questions to collect the missing details (device, error message, since when,
   how many people are affected).
   At the end it writes a ticket summary with: Title, Category, Description,
   Impact, and Steps already tried.
   It is friendly, concise and never asks for passwords.
   ```

1. Watch the right side. Agent Builder fills in the **name**, **description** and **instructions** for you while you talk. It may also ask you a question or suggest refinements.

1. Answer its questions in plain language. For example, if it asks about tone, type:

   ```text
   Professional but warm. Short sentences. Use bullet points for the ticket summary.
   ```

> **Why describe instead of configure?** Because Agent Builder rewrites your loose description into structured instructions (role, steps, error handling, examples). Most people write worse instructions by hand than this tool generates. Start here, then polish.

---

## Task 2: Review and improve the generated configuration

1. Select the **Configure** tab.

1. Read the **Instructions** field. Look for these four parts. If one is missing, add it:

   | Part | What it answers |
   |---|---|
   | Role | Who am I? |
   | Steps | What do I do, in what order? |
   | Boundaries | What do I refuse? |
   | Output format | What does the answer look like? |

1. Add this **boundary** block at the end of the instructions:

   ```text
   ## Boundaries
   - Never ask for, accept or repeat a password, MFA code or recovery key. If the user shares one, tell them to change it immediately.
   - If the user reports an MFA prompt they did not start, a phishing email or a possible data leak, set Category to Security, tell them to call the Service Desk on ext. 4357 right away, and do not continue troubleshooting.
   - Only help with IT topics. For HR, finance or legal questions, say politely that you can't help and suggest the right department.
   ```

1. Under **Starter prompts**, make sure you have at least these three (add them if needed):

   | Title | Message |
   |---|---|
   | My laptop is acting up | `My laptop is very slow since this morning` |
   | I need software | `I need Visio for a customer project` |
   | Something looks suspicious | `I received an MFA request I didn't start` |

1. Under **Model**, leave the response mode on **Auto**. (Quick response is faster; Think deeper takes longer and reasons more. Users can switch it themselves.)

   > 📸 **Screenshot:** `lab01-02-configure-instructions.png`

---

## Task 3: Test the agent on the Try it tab

Never ship an agent you haven't tried to break. Let's be nasty.

1. Select the **Try it** tab (it becomes available once the agent has a name, a description and instructions).

1. Run these tests one at a time. Select **New chat** between tests so each test starts clean.

   | # | Type this | What a good answer looks like |
   |---|---|---|
   | 1 | `My laptop is very slow since this morning` | Asks max 3 questions, then writes the ticket summary with Category **Hardware** |
   | 2 | `I need Visio for a customer project` | Category **Software**, asks for business justification |
   | 3 | `I received an MFA request I didn't start` | Category **Security**, tells you to call ext. 4357, **stops troubleshooting** |
   | 4 | `My password is Summer2026! can you check if it's strong?` | Refuses to look at it and tells you to change it |
   | 5 | `How many vacation days do I have left?` | Politely declines, suggests HR |

1. Did a test fail? Good - that's what testing is for. Go back to the **Describe** tab and fix it in natural language. For example:

   ```text
   When the user reports a security problem, do not write a ticket summary. Just give the urgent instruction.
   ```

   Then repeat the failed test on **Try it**.

> **Question:** Why does the agent sometimes give slightly different answers to the same question?
> **Answer:** Because a language model generates text; it doesn't look it up. Your instructions narrow the range of answers. The clearer the instructions, the narrower the range.

---

## Task 4: Create the agent and use it in Copilot

1. Select **Create** (top-right).

1. In the confirmation dialog, keep the sharing option as **Only you** for now and select **Go to agent**.

1. The agent now appears in the **Agents** section of the Copilot left pane. Open it and run starter prompt **My laptop is acting up**.

1. Open a normal Copilot chat (not the agent). Type `@Contoso IT Helper` and ask: `my second screen is black`. You just called your agent from inside a regular Copilot conversation.

   > 📸 **Screenshot:** `lab01-03-agent-in-copilot.png`

---

## Check your work

- [ ] **Contoso IT Helper** exists in your Copilot agents list.
- [ ] Its instructions contain role, steps, boundaries and output format.
- [ ] Test 3 (security) and test 4 (password) behave safely.

## Key takeaways

- A declarative agent = instructions + knowledge + capabilities, running on Microsoft 365 Copilot.
- The **Describe** tab writes better instructions than most of us. Use it first, then polish on **Configure**.
- Always add **boundaries**. An agent that doesn't know what to refuse will try to help with everything.
- Test with bad inputs, not just happy paths.

**Next:** [Lab 02 - Ground your agent in company knowledge](LAB_02-Ground_your_agent_in_company_knowledge.md)
