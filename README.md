# IndieCRM for Claude

![IndieCRM](plugins/indiecrm/assets/logo.png)

Your sales pipeline, one conversation away. Connect IndieCRM to Claude to review
open deals, find the right contact, and keep your pipeline current while you work.
Works with Claude, Cowork, and Claude Code using your existing IndieCRM account.

## Put your pipeline to work

| Workflow | What Claude can do |
| --- | --- |
| Pipeline review | Review recent open deals, group values by currency, and suggest next steps. |
| Contact lookup | Find contacts and companies in the workspace you choose. |
| Deal updates | Create deals, move them between pipeline stages, and record notes or calls. |
| Research handoff | Save verified lead research and available images to New leads, for workspaces with private beta access. |

## Start with a conversation

> Review my open deals. Which ones should I focus on this week, and why?

> Find Acme in my contacts and show me what is saved about the company.

> Move the Acme demo deal to Negotiation and record that the demo went well.

> Save this verified Sales Navigator lead list to New leads in my workspace.

Claude uses records returned by IndieCRM and asks when a target is ambiguous.
Research imports require verified Sales Navigator lead URLs and New leads beta access.

## Claude and Cowork

1. Open **Customize → Connectors → Add custom connector** in Claude.
2. Name the connector **IndieCRM** and enter `https://indiecrm.app/mcp`.
   If the dialog asks for authentication settings, choose **Sign in now** and
   **Register automatically**. Leave the client ID and secret empty.
3. Add the connector, select **Connect**, and sign in to IndieCRM to approve access.
   On Team and Enterprise plans, an owner first adds the connector for the organization;
   each person then connects with their own IndieCRM account.

For the guided workflows, download
[indiecrm-claude.zip](https://github.com/danielsinewe/indiecrm-claude/releases/latest/download/indiecrm-claude.zip).
Open **Customize → Plugins → Add → Upload plugin**, select the ZIP, and connect
IndieCRM on the plugin's **Connectors** tab. The package contains two skills:
`manage-crm` and `save-lead-research`.

## Claude Code

```sh
claude plugin marketplace add danielsinewe/indiecrm-claude
claude plugin install indiecrm@indiecrm-plugins
```

Start Claude Code and use `/mcp` to authenticate IndieCRM in your browser.
The skills are available as `/indiecrm:manage-crm`,
`/indiecrm:sales-navigator-audience` and `/indiecrm:save-lead-research`.

For just the connector, use this instead of installing the plugin:

```sh
claude mcp add --transport http --scope user indiecrm https://indiecrm.app/mcp
```

Authenticate with `/mcp`. Avoid installing both routes in the same Claude Code
session so you do not expose duplicate CRM tool sets.

## Try it

> Review my IndieCRM pipeline and suggest next steps for my open deals.

> Move the Acme demo deal to Negotiation and log a note that the demo went well.

Claude discovers your workspaces first. Tell it which workspace to use when you
have more than one. All calls use your existing account and workspace permissions.
Deal, contact and company lists support offset pagination with at most 200 rows
per page. Follow nextOffset until null; saved research lead lists also support pagination. Lead research imports require the
New leads private beta and verified Sales Navigator lead URLs. LinkedIn enrichment
can use metered provider research and runs only when requested.

The connector cannot send emails, schedule calendar meetings, delete contacts or
companies, or edit arbitrary contact/company fields. Product deletion is an
explicit separate tool, restricted to the exact selected product. Logging an email or meeting saves an
activity. Use the IndieCRM app for sending and scheduling.

## Privacy and support

The package contains no API keys, account data, local executables, or hooks.
OAuth connects each person to their own IndieCRM account. Returned CRM data is
processed by Anthropic when used in Claude. Disconnect IndieCRM in Claude's
connector settings when you no longer need it.

- [Setup and support](https://indiecrm.app/claude)
- [Privacy Notice](https://indiecrm.app/privacy)
- [Terms & Conditions](https://indiecrm.app/terms)
- [Support](https://indiecrm.app/support)
- Contact: hello@danielsinewe.com

This public repository and its download are available for direct installation.
They do not imply an approved listing in Anthropic's directory. Directory review
and publication are separate steps. IndieCRM is operated by Daniel Sinewe.

## Package layout

`.claude-plugin/marketplace.json` lists `plugins/indiecrm`. That plugin contains
its own `.claude-plugin/plugin.json`, `.mcp.json`, skills, README, license, and
brand assets. Only the connector configuration and workflow instructions are
published here; the hosted IndieCRM service has its own terms.

## Updates

Claude Code: run `claude plugin marketplace update indiecrm-plugins`, then
`claude plugin update indiecrm@indiecrm-plugins`. Apply the update in the next
session as directed by Claude Code. For uploaded ZIPs, download the new release
and update through the host plugin controls; an old uploaded archive does not
automatically become a new skill version. The HTTPS service updates separately.
After updating, confirm the loaded version, all three skills and the MCP server,
then ask for a read in an explicitly named workspace. No reinstall or reconnection
is a substitute for checking the actual loaded tool catalog.

Audience planning needs supported audience tools plus a permitted signed-in
source browser. If the host omits a required tool, report the exact missing name
and stop that step; do not invent a schema, use an alias experiment, or silently
switch to a local CLI. Installation and validation alone do not establish a
successful authenticated workflow in every host.
