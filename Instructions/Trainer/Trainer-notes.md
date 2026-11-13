# Trainer notes

## Suggested agendas

| Format | Labs | Audience |
|---|---|---|
| Half-day "business" track | 00 (pre-done by trainer), 01, 02, 03, demo of 08 | Business users, managers, pre-sales account teams |
| One-day "maker" track | 00, 04, 05, 06, 07 | Makers, IT pros |
| Two-day full path | 00-09 | Mixed workshop |
| 90-minute customer demo | 01 (live), 05 Task 5 (MCP), 08 Task 5-6 (trigger + injection) | Customers, partners |

## Before the session (do this 48 hours ahead)

- [ ] Assign **Microsoft 365 Copilot** licenses to every learner account (Labs 01-03).
- [ ] Give every learner Copilot Studio access and a **Dataverse environment** (one shared "Agents-Lab" environment or one developer environment each). Check **Copilot Credits** capacity - building, testing, evaluating and autonomous runs all consume credits.
- [ ] Set a **manager** in Entra ID for each learner account (Lab 06 approval). Simplest: make the trainer account everyone's manager.
- [ ] Check the Power Platform **data policy** of the lab environment allows: SharePoint, Office 365 Outlook, Office 365 Users, Approvals, Microsoft Teams, and custom MCP servers (Lab 05).
- [ ] Check **Knowledge sources for agents** allows SharePoint and file upload.
- [ ] Make sure Restricted SharePoint Search is **off** in the lab tenant (it blocks SharePoint knowledge in Agent Builder).
- [ ] Optional: pre-create the Contoso IT Hub site and list for the half-day track.

## Verify before each delivery (fast-moving features)

These labs were written in **November 2026** against preview features of the GitHub Copilot harness. Do a dry run and check:

| Lab | What to check |
|---|---|
| 03 | Skills in Agent Builder: still under **Configure > Skills > Add**; zip limits (8 skills, 50 MB) |
| 03 | **Copy to Copilot Studio** lands in the classic designer (standard harness) - confirmed Nov 2026; skills are not copied |
| 05 | MCP custom server dialog fields (Name / Description / Server URL / Authentication) |
| 06 | Actions placed **after "Respond to the agent"** keep running in your environment (run history shows the approval step). If not, split the approval into a second workflow triggered by the SharePoint item. |
| 07 | Skill upload in Copilot Studio accepts the `.zip` package (otherwise upload `SKILL.md`) |
| 07 | Name of the setting that allows other agents to connect |
| 08 | Trigger list (SharePoint **When an item is created**, **Recurrence**), **Start listening** and **Activate for myself** flow |
| 09 | Evaluate CSV template columns; channel name for Teams + Microsoft 365 Copilot |

## Common problems

| Symptom | Likely cause | Fix |
|---|---|---|
| Agent Builder ignores SharePoint files | Source still **Preparing**, no permission, or Restricted SharePoint Search | Wait, check access, check tenant setting |
| "You can't use this environment" when copying | No Dataverse or unsupported region | Use the lab environment |
| Tool greyed out in **Add a tool** | Blocked by data policy | Adjust DLP for the lab environment |
| Agent never calls the tool | Vague tool name/description | Rewrite description: *what, when, inputs* |
| Workflow not listed in Tools | Not published, or missing trigger/response nodes | Publish; check **When an agent calls the flow** + **Respond to the agent** |
| Workflow tool times out | Approval placed before **Respond to the agent** | Respond first (100-second limit) |
| Connected agent missing | Not published, not allowed to connect, different environment | Publish and enable connections |
| Autonomous agent doesn't run | Not published, or not activated in **Agent management** | Publish, then activate |
| Trigger fires in a loop | "Created **or modified**" trigger + agent updates the item | Use "When an item is created" |

## Discussion questions (use between labs)

1. When is Agent Builder enough, and when do you need Copilot Studio? (Lab 03)
2. User authentication vs. agent author authentication - which one for a Service Desk agent? For an autonomous agent? (Labs 05, 08)
3. Which steps of your customer's process are *fuzzy* (agent) and which are *fixed* (workflow)? (Lab 06)
4. Who should own the License Advisor - IT or the licensing team? Why? (Lab 07)
5. Name three controls that stop an autonomous agent from doing damage that don't depend on the prompt. (Lab 08)

## Answer key (expected triage for Lab 08 test items)

| Title | Category | Priority | Team |
|---|---|---|---|
| Laptop stolen from car | Hardware | High | Endpoint |
| Phishing - clicked link | Security | Critical | SecOps |
| Mouse not working (injection) | Hardware, Normal - or "Needs human review" | | Endpoint |
| Docking station not detected (Lab 00 item) | Hardware | Normal | Endpoint |
