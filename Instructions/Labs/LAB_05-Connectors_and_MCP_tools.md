---
lab:
    title: 'Lab 05: Give your agent hands - connectors and MCP tools'
    module: 'Module 3 - Agents that act'
---

# Lab 05 - Give your agent hands: connectors and MCP tools

## Lab introduction

Until now our agents **talk**. From this lab they **do**.

A **tool** is something the agent can call to perform an action or fetch live data. In Copilot Studio you have three families:

| Tool type | Best for | Example in this lab |
|---|---|---|
| **Connectors** | Well-known services with a ready-made Power Platform connector | SharePoint "Create item", Outlook "Send an email" |
| **MCP servers** | Any service that speaks Model Context Protocol - custom or internal | Microsoft Learn MCP server |
| **Workflows** | Multi-step, deterministic processes | Lab 06 |

Here's the key idea: **you don't wire the tools together**. You give each tool a clear name and description, and the runtime decides *when* to call it, *with which inputs*, and *in what order*. Good descriptions = good decisions.

## Estimated timing: 60 minutes

## Lab scenario

Employees keep asking the Service Desk agent to "just open the ticket for me". The manager agrees, but with rules: the agent creates the item in the **IT Requests** list, sends the user a confirmation email, and can look up the status of the user's existing requests. Engineers also want the agent to answer technical "how do I configure X in Microsoft 365 / Intune / Entra" questions from **official Microsoft documentation**.

## Job skills

- Task 1: Add the SharePoint "Create item" tool
- Task 2: Add the SharePoint "Get items" tool
- Task 3: Add the Outlook "Send an email" tool
- Task 4: Update the instructions to orchestrate the tools
- Task 5: Add the Microsoft Learn MCP server
- Task 6: Test and debug with the activity trace

---

## Task 1: Add the SharePoint "Create item" tool

1. Open **Contoso Service Desk** in Copilot Studio and stay on the **Build** tab.

1. In the components panel, select **Tools**. The **Add a tool** dialog opens with tabs **Featured**, **Model Context Protocol (MCP)**, **Connectors** and **Workflows**.

1. Search for `SharePoint` and select **Create item**.

1. If prompted, create or select a **connection** (sign in with your lab account) until you see a green check mark.

1. Select **Add**, then open the tool's details and configure it:

   | Setting | Value |
   |---|---|
   | Name | `Create IT request` |
   | Description | `Creates a new IT request in the Contoso IT Requests list. Use it only after the user has confirmed the ticket summary. Inputs: Title, Description, Category, Priority.` |
   | Site Address | Your **Contoso IT Hub** site (set as a fixed value) |
   | List Name | `IT Requests` (fixed value) |

1. For the list columns, let the agent fill **Title**, **Description**, **Category** and **Priority** dynamically. Give each input a short description, for example for Category: `One of: Hardware, Software, Network, Account & Access, Security`.

   > 📸 **Screenshot:** `lab05-01-create-item-tool.png`

> **Why fix the site and list?** Because you never want the model to "choose" which SharePoint site to write to. **Fix what must not change, let the agent fill only what it must.** This is your first guardrail.

---

## Task 2: Add the SharePoint "Get items" tool

1. **Tools** > search `SharePoint` > **Get items** > **Add**.

1. Configure:

   | Setting | Value |
   |---|---|
   | Name | `Find my IT requests` |
   | Description | `Returns existing IT requests from the Contoso IT Requests list. Use it when the user asks about the status of their requests. Return Title, Status, Priority and AssignedTeam only.` |
   | Site Address / List Name | Same fixed values as Task 1 |
   | Top Count | `10` |

---

## Task 3: Add the Outlook "Send an email" tool

1. **Tools** > search `Office 365 Outlook` > **Send an email (V2)** > **Add**. Create the connection if needed.

1. Configure:

   | Setting | Value |
   |---|---|
   | Name | `Send request confirmation` |
   | Description | `Sends the user a confirmation email after an IT request was created. Only send to the user who is chatting. Never send to external addresses.` |
   | To | Let the agent fill it, with input description `Email address of the signed-in user` |

> **Whose identity runs the tool?** Look at the tool's authentication options:
> - **User authentication** - the tool runs as the person chatting. The email comes from *their* mailbox, the SharePoint item is created *by them*, and they only see data *they* can access.
> - **Agent author authentication** - the tool runs with *your* connection, for everybody.
>
> For a Service Desk agent used by many people, **user authentication** is the safer default. Users set up their connection the first time the agent needs it. Keep that in mind - it's a classic exam and customer question.

---

## Task 4: Update the instructions to orchestrate the tools

1. Add this block to the agent's **Instructions** (after "How you work"):

   ```text
   # Tools
   - Before calling "Create IT request", show the user the ticket summary
     (Title, Category, Priority, Description) and ask "Shall I create this request?".
     Only call the tool after a clear yes.
   - After the request is created, call "Send request confirmation" to the user
     with the title and the item ID, then tell the user the ID.
   - When the user asks "what's the status of my requests", call "Find my IT requests"
     and show a short table: Title | Status | Priority.
   - For technical "how do I configure..." questions about Microsoft 365, Intune,
     Entra or Windows, use the Microsoft Learn tools and include the article link.
     Contoso policies always win over generic Microsoft guidance.
   ```

1. **Save**.

> **Question:** Why ask for confirmation in the instructions when the tool could just run?
> **Answer:** Because creating a record is a **write action**. A good rule: *read* tools can run freely, *write* tools need a confirmation. In Lab 06 we go further with a real approval, and in Lab 08 we discuss why instructions alone are not a security boundary.

---

## Task 5: Add the Microsoft Learn MCP server

The Microsoft Learn MCP server is a free, public, no-authentication MCP server that lets agents search and read official Microsoft documentation. Perfect for learning MCP.

1. **Tools** > **Add** (next to the search bar) > **Model Context Protocol (MCP)**.

1. Enter:

   | Field | Value |
   |---|---|
   | Name | `Microsoft Learn documentation` |
   | Description | `Searches and reads official Microsoft documentation on learn.microsoft.com. Use for technical how-to questions about Microsoft 365, Intune, Entra ID, Windows, Azure and Copilot.` |
   | Server URL | `https://learn.microsoft.com/api/mcp` |
   | Authentication | None |

1. Select **Add**. Copilot Studio performs the MCP handshake and lists the tools the server exposes (for example `microsoft_docs_search`, `microsoft_docs_fetch`, `microsoft_code_sample_search`).

1. Read each tool's description, then **Save**.

   > 📸 **Screenshot:** `lab05-02-mcp-tools-list.png`

> **What just happened?** You didn't configure three tools - the **server** told Copilot Studio which tools it has. If Microsoft adds a tool to the server tomorrow, your agent sees it automatically. That's the power of MCP: one connection, a living catalog of tools.

> **Bonus:** open the **MCP** tab of **Add a tool** and browse the gallery (Microsoft 365 servers such as Mail, Foundry IQ, Fabric IQ...). Some connect with your signed-in identity in one click.

---

## Task 6: Test and debug with the activity trace

1. Select **Preview** and run this conversation:

   ```text
   My second screen stays black since the Windows update this morning. Only me.
   ```

   - The agent should ask a question or two, then show a ticket summary and ask for confirmation.
   - Answer `yes`.
   - The agent creates the item, sends you the confirmation email and gives you the ID.

1. Open the **activity trace**. For **Create IT request**, check:
   - which **inputs** the agent generated (Title, Category, Priority, Description),
   - what the tool **returned** (the new item ID).

   > 📸 **Screenshot:** `lab05-03-trace-create-item.png`

1. Open the **IT Requests** list in SharePoint. Is the item there with the right Category and Priority? Check your inbox for the confirmation.

1. Test the other tools:

   | Message | Expected tool |
   |---|---|
   | `What's the status of my IT requests?` | Find my IT requests |
   | `How do I configure Windows Hello for Business with Intune?` | Microsoft Learn documentation (with links) |
   | `What's our MFA policy?` | Knowledge (Contoso policy), **not** MCP |

1. **Break it on purpose.** Remove the word "only" from the Create IT request description, type `create a ticket: printer jam` and see if the agent skips the confirmation. Then put the description back.

> **Troubleshooting:** the agent doesn't call a tool when you expect it? 90% of the time the fix is in the **tool name / description** or the **instructions**, not in the tool itself.

---

## Check your work

- [ ] A new item was created in **IT Requests** by the agent, after you confirmed.
- [ ] You received the confirmation email.
- [ ] The agent answered a technical question with links from Microsoft Learn via MCP.
- [ ] You can show the tool inputs and outputs in the activity trace.

## Key takeaways

- Tools turn an assistant into an agent. The runtime picks tools from their **names and descriptions**.
- Fix the parameters that must never change (site, list). Let the agent fill only the rest.
- Decide **whose identity** runs each tool. User authentication is the safe default for shared agents.
- **MCP** = one connection, many tools, published by the server and kept up to date.

**Next:** [Lab 06 - Workflows and human approval](LAB_06-Workflows_and_human_approval.md)
