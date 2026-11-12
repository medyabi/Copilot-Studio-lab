---
lab:
    title: 'Lab 02: Ground your agent in company knowledge'
    module: 'Module 1 - Agent Builder'
---

# Lab 02 - Ground your agent in company knowledge

## Lab introduction

Ask your Lab 01 agent "How long does it take to get Visio?" and it will guess. Politely. Confidently. And wrong.

Why? Because it has never read the Contoso Software Request Policy. The model knows a lot about the world, but nothing about **your** company.

The fix has a name: **grounding**. You give the agent knowledge sources, and before it answers, Copilot searches those sources and builds the answer from what it finds - with **citations** so the user can check.

In this lab you ground the agent, you see what changes, and you learn the one setting that decides whether the agent may also use the public web.

## Estimated timing: 40 minutes

## Lab scenario

The Service Desk wants the IT Helper to answer policy questions from the official documents only, cite the source, and say "I don't know" instead of guessing. It also wants Microsoft's own documentation available for "how do I..." questions about Microsoft 365.

## Job skills

- Task 1: Prove the problem (ask before grounding)
- Task 2: Add SharePoint knowledge
- Task 3: Add a public website and decide about web search
- Task 4: Teach the agent how to use its knowledge
- Task 5: Test grounding and citations
- Task 6: Share the agent

---

## Task 1: Prove the problem

1. Open **Contoso IT Helper** in Copilot.

1. Ask: `How long does it take to get Visio, and who approves it?`

1. Write down the answer. It's probably generic ("contact your IT department", "usually a few days"). There's no citation. That's the problem we fix.

---

## Task 2: Add SharePoint knowledge

1. In the Copilot left pane, hover over **Contoso IT Helper**, select **...** > **Edit**.

1. Select the **Configure** tab and scroll to **Knowledge**.

1. Select the knowledge box and either:
   - **Browse** (Attach cloud files) and pick the **IT Policies** folder on the **Contoso IT Hub** site, **or**
   - paste the folder URL (it must contain the site path, for example `.../sites/ContosoITHub/Shared Documents/IT Policies`, and no query parameters).

1. Wait until the source no longer shows **Preparing**. You can test while it prepares, but the agent won't use the files until they're ready.

   > 📸 **Screenshot:** `lab02-01-knowledge-sharepoint.png`

> **Limits worth knowing:** up to 100 SharePoint files, 1 SharePoint list and 50 OneDrive files per agent. The agent respects SharePoint permissions and sensitivity labels. Keep documents short and focused - one topic per file works best.

---

## Task 3: Add a public website and decide about web search

1. Still in **Knowledge**, select **Enter URL** and add:

   ```text
   https://support.microsoft.com/office
   ```

   > Public website rules: max **two levels** in the path, **no query parameters** (`?x=1`), and up to **four** URLs.

1. Look for the **Search all websites** toggle. Leave it **off**.

> **Question:** Why leave web search off?
> **Answer:** With it on, the agent may mix internet content into answers about *Contoso* policy. For an internal policy agent, you want answers from **your** sources only. Turn it on for agents whose job is general research.

1. Look for **Reference people in organization** (People data). Leave it **on** - it lets the agent say who someone is or who their manager is, which helps for approvals.

---

## Task 4: Teach the agent how to use its knowledge

Knowledge without instructions is like giving someone a library card without telling them what to read.

1. Scroll back to **Instructions** and add this block:

   ```text
   ## Using knowledge
   - For any question about Contoso devices, software, remote access, passwords or MFA, answer ONLY from the Contoso IT Policies documents and always cite the document.
   - Use support.microsoft.com only for "how do I..." questions about Microsoft 365 apps.
   - If the answer is not in the documents, say "I couldn't find that in the Contoso IT policies" and offer to write a ticket summary instead. Never guess a policy, a price, an approval rule or a deadline.
   - When a policy has a table, answer with the relevant row, not the whole table.
   ```

1. Add two more starter prompts:

   | Title | Message |
   |---|---|
   | Lost device | `I lost my laptop at the airport, what do I do?` |
   | Travel | `I'm going to Dubai next week, anything I should do?` |

1. Select **Update** to save.

---

## Task 5: Test grounding and citations

1. On **Try it** (or in the agent chat), run these tests. Select **New chat** between tests.

   | # | Question | Expected (from the policies) |
   |---|---|---|
   | 1 | `How long does it take to get Visio, and who approves it?` | Category B, manager approval, 2 business days - **with a citation** |
   | 2 | `I lost my laptop at the airport, what do I do?` | Report within 4 hours, Intune wipe, replacement in 2 business days |
   | 3 | `Which laptop does a pre-sales engineer get?` | Dell Latitude 9450 2-in-1, 5G, 3-year refresh |
   | 4 | `Can I use ChatGPT with customer data?` | No - only approved Microsoft Copilot tools; other AI tools are Category D |
   | 5 | `What's the Wi-Fi password in the Paris office?` | "I couldn't find that..." + offers a ticket summary. **No guess.** |
   | 6 | `How do I share my screen in Teams?` | Answer based on support.microsoft.com |

1. For test 1, hover over or select the citation. It should open the **Software Request Policy**.

   > 📸 **Screenshot:** `lab02-02-answer-with-citation.png`

1. Compare test 1 with your note from Task 1. That difference is grounding.

> **Troubleshooting:** If the agent ignores the documents, check that (a) the source isn't still **Preparing**, (b) your account can open the files, and (c) your tenant doesn't use Restricted SharePoint Search (it blocks SharePoint as a knowledge source).

---

## Task 6: Share the agent

1. Open the agent's **...** menu and select **Share**.

1. Choose **Specific users in your organization** and add a colleague (or a second lab account).

1. Copy the link and send it to them.

1. Ask your colleague to run test 1. Do they get the same citation?

> **The permission test.** Agent sharing does **not** share the SharePoint files. If your colleague can't open the IT Policies folder, the agent can't use it for them either. Answers follow the *user's* permissions. That is exactly what you want in a real company.

---

## Check your work

- [ ] The agent answers the Visio question with a citation to the Software Request Policy.
- [ ] The agent says it can't find the Wi-Fi password instead of inventing one.
- [ ] Web search is off. The agent is shared with one colleague.

## Key takeaways

- **Grounding** = the agent searches your sources first, then answers with citations.
- Knowledge alone isn't enough. Tell the agent **when** to use each source and what to do when nothing is found.
- Public web search is a deliberate choice, not a default.
- SharePoint knowledge respects each user's permissions. Sharing an agent never shares its files.

**Next:** [Lab 03 - Capabilities, skills and the jump to Copilot Studio](LAB_03-Capabilities_skills_and_copy_to_Copilot_Studio.md)
