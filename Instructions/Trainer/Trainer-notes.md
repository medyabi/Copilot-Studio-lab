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
- [ ] Set a **manager** in Entra ID for each learner account (Lab 06 approval). Make the **trainer account everyone's manager**: every approval lands in your inbox and you approve them live in class (a good demo moment).
- [ ] Make sure that manager has a **license with Exchange Online** (a mailbox). An unlicensed manager receives the approval but Outlook fails with `OwaUserHasNoMailboxAndNoLicenseAssignedException`. A new mailbox takes 5-30 minutes to appear.
- [ ] Check the Power Platform **data policy** of the lab environment allows: SharePoint, Office 365 Outlook, Office 365 Users, Approvals, Microsoft Teams, and custom MCP servers (Lab 05).
- [ ] Check **Knowledge sources for agents** allows SharePoint and file upload.
- [ ] Make sure Restricted SharePoint Search is **off** in the lab tenant (it blocks SharePoint knowledge in Agent Builder).
- [ ] Optional: pre-create the Contoso IT Hub site and list for the half-day track.

## Running the labs with a team (shared tenant and environment)

One site, one environment, one list **per learner**. Do this before the session:

- [ ] Build the **Contoso IT Hub** site, upload the 4 policies and create the **IT Requests** template list yourself (Lab 00 Tasks 2-4). Learners don't repeat them.
- [ ] Learners only do Lab 00 **Task 1** and **Task 6** (create `IT Requests - XX` from the existing list).
- [ ] Give every learner at least **Edit** on the site (they create a list and items) and Copilot Studio **maker** access in the environment.
- [ ] Announce the naming rule: initials on every agent, workflow and list (`Contoso Service Desk - XX`, `Contoso License Advisor - XX`, `Request software approval - XX`, `Contoso Triage Agent - XX`).
- [ ] Credits: all learners draw from the same environment. Set an Azure **budget alert** on the pay-as-you-go plan, and keep an eye on Lab 08 (autonomous runs).
- [ ] Lab 08: check every trigger points to the learner's **own** list. A trigger on the shared template list fires every learner's agent on every item.
- [ ] After the session: deactivate the Lab 08 agents (**Agent management**), unpublish or delete the learners' agents and workflows, and delete the `IT Requests - XX` lists.

## Verify before each delivery (fast-moving features)

These labs were written in **November 2026** against preview features of the Microsoft Copilot harness. Do a dry run and check:

| Lab | What to check |
|---|---|
| 03 | Skills in Agent Builder: still under **Configure > Skills > Add**; zip limits (8 skills, 50 MB) |
| 03 | **Copy to Copilot Studio** lands in the classic designer (standard harness) - confirmed Nov 2026; skills are not copied |
| 05 | MCP custom server dialog fields (Name / Description / Server URL / Authentication) |
| 06 | Actions placed **after "Respond to the agent"** keep running - confirmed Nov 2026 (Activity tab shows the approval step, 1m40s, then the If branch). |
| 06 | The **▷ Test** button is unavailable for agent-triggered workflows - test from the agent, debug in **Activity** / **Run node** |
| 07 | Skills **+** menu: Upload a skill / Create from blank / Browse skills (preview) - confirmed Nov 2026; `.zip` upload works |
| 07 | Only agents built in the new experience can be connected; classic agents show *Classic agent connections are not supported* |
| 07 | **Allow other agents to connect** (Settings > AI + behavior) is on by default - confirmed Nov 2026 |
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
| Workflow fails: `Resource ' RequesterEmail' does not exist` | Learner **typed** the input name instead of inserting the chip | Golden rule #1 in Lab 06: delete the text, insert the ⚡ chip, publish again |
| Approval never sent / invalid address | Text in front of an email chip (`manager's Mail`, `Requester: RequesterEmail`) | Email fields take **one chip only** |
| Agent created a normal IT request with Status New, no approval | **Bypass**: workflow failed, orchestrator used Create IT request | Fix the workflow; add the "never use Create IT request for software" instruction + tool description. Great discussion point: instructions are not guardrails |
| Learner picked a Nintex/Datablend "workflow" | Searched "workflow" in Add a tool | Use **Add > Workflow** |
| Email reads "forMicrosoft Visiowas approved" | No spaces around the chip in Body | Add a space each side |
| Closure skill not used, "no ticket found" | The agent checked Find my IT requests first and the ticket doesn't exist | Expected (grounded). Test with a real ticket number |
| Memory test answers in English | Memory off, not saved, or same conversation | Save, then **New chat** |
| Several Teams alerts / triage values change on their own | Learners' triggers point to the **shared** list | Each trigger and tool must use the learner's own `IT Requests - XX` |
| Learner connected someone else's License Advisor | No initials in agent names | Rename with initials; reconnect |
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
