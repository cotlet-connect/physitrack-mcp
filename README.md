# Physitrack API integration and MCP server: connect Physitrack to ChatGPT, Claude and any AI agent

**Physitrack API integration and MCP server by Cotlet.dev: yes, Claude, ChatGPT and other AI agents can work in your Physitrack account.** Physitrack has no official MCP server and no ChatGPT or Claude app; its API is for PMS and EHR systems only (checked 26 Nov 2026). Cotlet.dev connects your own signed-in Physitrack account to any AI agent, so you ask in plain words and the AI helps you spot patients with low adherence, collect outcome scores and answer patient messages.

## What you can ask

> Which active patients had under 50% adherence this week? Draft a short check-in message for each.

> Download this month's outcome measure results and put the start and latest score per patient in my sheet.

> Download the PDF of every program I assigned today so I can attach them to the patient files.

> Assign my 'Knee rehab week 1' template to these five patients. Show me the list before you assign.

> List unread messages from patients and draft a reply to each. Send nothing yet.

## What physio clinic owners and practice managers ask their AI to do in Physitrack

From physio clinic owners and practice managers' posts on Reddit, forums, review sites and the Physitrack help center (checked 26 Nov 2026).

| Job | Ask your AI | Make it repeat |
|---|---|---|
| Patients not doing their exercises | "Which active patients had under 50% adherence this week? Draft a short check-in message for each." | Weekly |
| Outcome scores into a sheet | "Download this month's outcome measure results and put the start and latest score per patient in my sheet." | Your own endpoint, called from a sheet or n8n |
| Program PDFs for the patient file | "Download the PDF of every program I assigned today so I can attach them to the patient files." | When you ask |
| Assign a template to several patients | "Assign my 'Knee rehab week 1' template to these five patients. Show me the list before you assign." | When you ask |
| Unread patient messages | "List unread messages from patients and draft a reply to each. Send nothing yet." | Daily |
| RTM days for billing | "Which RTM patients have 16 or more days of data this month, and who is close to 16?" | Weekly |
| Patients who never opened PhysiApp | "Which patients got a program in the last 14 days but never logged in to PhysiApp? Draft a reminder with their access code." | Weekly |
| Physitrack invoices for the accountant | "Download all my Physitrack invoices for this financial year as PDFs." | When you ask |

Your AI asks you before it sends, books, replies or changes anything. Payments stay in your hands.

## Physitrack API

Physitrack has an API for PMS and EHR systems only, started by email to its support team, so a single clinic cannot get a key (checked 26 Nov 2026). With Cotlet.dev you build your own Physitrack API: show your AI agent a job once in your signed-in Physitrack account, save it as an endpoint and call it from n8n, Make, Zapier or your own code. How it works: https://cotlet.dev/integrations/api/physitrack

Endpoints you can build in a few minutes (none of them exists until you build it):

- `outcome-scores`: Outcome measure scores per patient over time
- `low-adherence`: Patients under N% adherence in a period
- `rtm-days`: RTM patients with days of data this month
- `unread-messages`: Unread patient messages with the patient
- `never-opened-app`: Patients with a recent program who never logged in
- `assign-template`: Assign a programme template to a patient after your OK

Call one with your Cotlet API key: `POST https://cotlet.dev/api/v1/custom/outcome-scores`. One call runs up to 90 seconds, writes wait for your OK, payments are blocked.

## Connect in 2 minutes

Get your API key at https://cotlet.dev/app and export it as `COTLET_API_KEY`.

### Claude Code

```bash
claude mcp add --transport http cotlet https://cotlet.dev/mcp/v1 --header "Authorization: Bearer $COTLET_API_KEY"
```

### ChatGPT

Settings → Apps → add connector URL `https://cotlet.dev/mcp/v1`, then sign in with Cotlet.dev. No API key needed.

### Cursor

Copy `.cursor/mcp.json` from this repo into your project:

```json
{
  "mcpServers": {
    "cotlet": {
      "url": "https://cotlet.dev/mcp/v1",
      "headers": {
        "Authorization": "Bearer ${COTLET_API_KEY}"
      }
    }
  }
}
```

## Run it locally (stdio)

This repo is a real MCP server. It speaks MCP over stdio, lists its Physitrack tools without any key, and forwards every tool call to the hosted Cotlet.dev server. The hosted URL above stays the recommended way. Use this when your client only starts local servers.

```json
{"mcpServers":{"physitrack":{"command":"uvx","args":["physitrack-mcp"],"env":{"COTLET_API_KEY":"your-key"}}}}
```

Or with Docker:

```bash
docker build -t physitrack-mcp .
docker run -i --rm -e COTLET_API_KEY physitrack-mcp
```

Tools: `physitrack_guide`, `physitrack_status`, `physitrack_open`, `physitrack_read`, `physitrack_click`, `physitrack_type`, `physitrack_close`. Without `COTLET_API_KEY` a tool call answers: "Set COTLET_API_KEY. Get a key at https://cotlet.dev/app".

## Command line

Python 3.10+, nothing else to install. From PyPI:

```bash
pip install physitrack-mcp        # or run it once: uvx physitrack-mcp connect
physitrack-mcp connect
```

Or straight from this repo:

```bash
export COTLET_API_KEY="your-key"
python3 physitrack_agent.py connect   # ready config for Claude Code, Cursor, ChatGPT
python3 physitrack_agent.py status    # is Physitrack ready in your Cotlet.dev account
python3 physitrack_agent.py guide     # Physitrack screens and sign-in link
python3 physitrack_agent.py read      # open Physitrack, read the page, close the browser
python3 physitrack_agent.py tools     # list Cotlet.dev tools
python3 physitrack_agent.py serve     # start the stdio MCP server (same as no argument)
```

## What you can ask your AI to do in Physitrack

These are real jobs Physitrack users asked about. Cotlet lets ChatGPT, Claude or any MCP client do them in your own signed-in Physitrack account.

### Adherence and patient contact

#### Can AI find the patients in Physitrack who are not doing their exercises and draft a check-in message?

Yes. Physitrack calculates adherence for each patient. With Cotlet, your agent reads adherence in your signed-in account, lists active patients under your percent for the week and drafts a short check-in for each. Nothing is sent until you say yes.

Ask your agent: "Which active Physitrack patients had under 50% adherence this week? Draft a short check-in message for each."

As an API endpoint: Yes. Save the low-adherence list as an endpoint in Cotlet and call it every Monday from n8n, Make, Zapier or a script to get who to contact.

#### Can AI answer my unread patient messages in Physitrack?

Yes. With Cotlet, your agent opens the unread messages in your signed-in Physitrack account, lists them by patient and drafts a reply to each. Nothing is sent until you say yes, so you read every reply first.

Ask your agent: "List unread messages from patients in Physitrack and draft a reply to each. Send nothing yet."

As an API endpoint: Yes. Save the unread list as an endpoint in Cotlet and call it every morning from n8n, Make or Zapier to get the messages waiting.

#### Can AI find the Physitrack patients who never opened PhysiApp and remind them?

Yes. With Cotlet, your agent lists patients in your signed-in Physitrack account who got a program in the last 14 days but never logged in to PhysiApp. It drafts a reminder with each patient's access code. Nothing is sent until you say yes.

Ask your agent: "Which Physitrack patients got a program in the last 14 days but never logged in to PhysiApp? Draft a reminder with their access code."

As an API endpoint: Yes. Save the never-opened list as an endpoint in Cotlet and call it every week from n8n, Make or Zapier.

### Programs and templates

#### Can AI assign a Physitrack template to several patients at once?

Yes. With Cotlet, your agent opens each patient in your signed-in Physitrack account and assigns your chosen programme template, such as Knee rehab week 1. It shows you the list of patients first. Nothing is assigned until you say yes.

Ask your agent: "Assign my Knee rehab week 1 template in Physitrack to these five patients. Show me the list before you assign."

#### Can AI save Physitrack program PDFs to my patient files?

Yes. Physitrack lets you print an exercise programme. With Cotlet, your agent opens each programme you assigned today in your signed-in account and downloads its PDF, ready to attach to the patient file. It only reads and downloads.

Ask your agent: "Download the PDF of every program I assigned in Physitrack today so I can attach them to the patient files."

### Outcomes and RTM

#### Can AI export Physitrack outcome measures to a spreadsheet?

Yes. Physitrack lets you view and download the aggregate results of assigned outcome measures. With Cotlet, your agent downloads this month's results from your signed-in account and puts each patient's start and latest score in your sheet.

Ask your agent: "Download this month's Physitrack outcome measure results and put the start and latest score per patient in my sheet."

As an API endpoint: Yes. Save the score table as an endpoint in Cotlet that returns JSON per patient, and send it to Google Sheets from n8n, Make or a script each month.

#### Can AI track RTM days in Physitrack for billing?

Yes. With Cotlet, your agent reads the remote therapeutic monitoring data in your signed-in Physitrack account and lists which RTM patients have 16 or more days of data this month and who is close to 16. It only reads. Billing decisions stay with you.

Ask your agent: "Which Physitrack RTM patients have 16 or more days of data this month, and who is close to 16?"

As an API endpoint: Yes. Save the RTM check as an endpoint in Cotlet and call it every week from n8n, Make or Zapier to see who is close to 16 days.

### Invoices

#### How do I download all my Physitrack invoices at once?

Yes. Physitrack lets you download an invoice from your account. With Cotlet, your agent opens your signed-in account and downloads every invoice for this financial year as PDFs, so you can hand them to your accountant. It only reads and downloads.

Ask your agent: "Download all my Physitrack invoices for this financial year as PDFs."

## Questions from Physitrack users

**Does Physitrack have an API?**

Physitrack has an API for PMS and EHR systems only, started by email to its support team, so a single clinic cannot get a key (checked 26 Nov 2026). With Cotlet.dev you build your own Physitrack API on your signed-in account and call it from n8n, Make or your own code: https://cotlet.dev/integrations/api/physitrack

**How do I connect Physitrack to ChatGPT?**

Add Cotlet.dev as an app in ChatGPT (Settings → Apps, connector URL `https://cotlet.dev/mcp/v1`), sign in to Physitrack once in your Cotlet.dev Cloud Browser, and ask ChatGPT in plain words. No code and no Physitrack API key.

**Is there a way to use Claude with Physitrack?**

Yes. In Claude Code run the one-line `claude mcp add` command above; in the Claude app add `https://cotlet.dev/mcp/v1` as a custom connector. Claude then works in your own Physitrack account.

**Is there a Physitrack MCP server?**

Physitrack has no official MCP server for your account. Cotlet.dev is a hosted MCP server that works in your own Physitrack account for you, with Claude, ChatGPT, Cursor, Codex, n8n, Make and Zapier.

**Physitrack's API is for PMS systems only. How can my clinic use AI with it?**

Your AI works in your own signed-in Physitrack account on the same screens you use, so you do not need a PMS partnership or an API key.

**Can AI find patients who stopped doing their exercises in Physitrack?**

Yes. It reads the adherence on each patient's programme, lists who fell behind and drafts a nudge after your OK.

## Find it on

- [Physitrack on Cotlet.dev](https://cotlet.dev/integrations/mcp/physitrack?utm_source=github&utm_medium=readme&utm_campaign=physitrack&utm_content=find)
- [PyPI package physitrack-mcp](https://pypi.org/project/physitrack-mcp/)
- [Odel](https://odel.app/modules/cotlet/physitrack)
- [mcpbeat](https://mcpbeat.com/mcp-servers/cotlet/physitrack/)

## Also works with

- [Mangomint MCP](https://github.com/cotlet-connect/mangomint-mcp)
- [Wanderlog MCP](https://github.com/cotlet-connect/wanderlog-mcp)
- [SmugMug MCP](https://github.com/cotlet-connect/smugmug-mcp)
- [Garmin Connect MCP](https://github.com/cotlet-connect/garmin-mcp)
- [Housecall Pro MCP](https://github.com/cotlet-connect/housecall-pro-mcp)
- [Medium MCP](https://github.com/cotlet-connect/medium-mcp)
- [Crexi MCP](https://github.com/cotlet-connect/crexi-mcp)
- [DistroKid MCP](https://github.com/cotlet-connect/distrokid-mcp)
- [LinkedIn MCP](https://github.com/cotlet-connect/linkedin-mcp)
- [EstimateOne MCP](https://github.com/cotlet-connect/estimateone-mcp)
- [Google Search Console MCP](https://github.com/cotlet-connect/google-search-console-mcp)
- [Lichess MCP](https://github.com/cotlet-connect/lichess-mcp)
- [Strava MCP](https://github.com/cotlet-connect/strava-mcp)
- [StreamElements MCP](https://github.com/cotlet-connect/streamelements-mcp)
- [Meetup MCP](https://github.com/cotlet-connect/meetup-mcp)
- [AccuRanker MCP](https://github.com/cotlet-connect/accuranker-mcp)
- [GetYourGuide MCP](https://github.com/cotlet-connect/getyourguide-mcp)
- [Idealista MCP](https://github.com/cotlet-connect/idealista-mcp)
- [Riverside MCP](https://github.com/cotlet-connect/riverside-mcp)
- [Udemy MCP](https://github.com/cotlet-connect/udemy-mcp)
- [Faire MCP](https://github.com/cotlet-connect/faire-mcp)
- [Teachable MCP](https://github.com/cotlet-connect/teachable-mcp)
- [Vagaro MCP](https://github.com/cotlet-connect/vagaro-mcp)
- [Buildertrend MCP](https://github.com/cotlet-connect/buildertrend-mcp)
- [Zoopla MCP](https://github.com/cotlet-connect/zoopla-mcp)
- [Dribbble MCP](https://github.com/cotlet-connect/dribbble-mcp)
- [Hostelworld MCP](https://github.com/cotlet-connect/hostelworld-mcp)
- [Mashvisor MCP](https://github.com/cotlet-connect/mashvisor-mcp)
- [Rent Manager MCP](https://github.com/cotlet-connect/rent-manager-mcp)
- [ThriveCart MCP](https://github.com/cotlet-connect/thrivecart-mcp)
- [Fresha MCP](https://github.com/cotlet-connect/fresha-mcp)
- [Booksy MCP](https://github.com/cotlet-connect/booksy-mcp)
- [BookingKoala MCP](https://github.com/cotlet-connect/bookingkoala-mcp)
- [Milanote MCP](https://github.com/cotlet-connect/milanote-mcp)
- [Partiful MCP](https://github.com/cotlet-connect/partiful-mcp)
- [Contractor Foreman MCP](https://github.com/cotlet-connect/contractor-foreman-mcp)
- [Pixieset MCP](https://github.com/cotlet-connect/pixieset-mcp)
- [GlossGenius MCP](https://github.com/cotlet-connect/glossgenius-mcp)
- [ZenMaid MCP](https://github.com/cotlet-connect/zenmaid-mcp)
- [AroFlo MCP](https://github.com/cotlet-connect/aroflo-mcp)
- [Simpro MCP](https://github.com/cotlet-connect/simpro-mcp)
- [Workiz MCP](https://github.com/cotlet-connect/workiz-mcp)
- [Kickserv MCP](https://github.com/cotlet-connect/kickserv-mcp)
- [Service Autopilot MCP](https://github.com/cotlet-connect/service-autopilot-mcp)
- [Mindbody MCP](https://github.com/cotlet-connect/mindbody-mcp)
- [DaySmart MCP](https://github.com/cotlet-connect/daysmart-mcp)
- [Acuity Scheduling MCP](https://github.com/cotlet-connect/acuity-scheduling-mcp)
- [Jane App MCP](https://github.com/cotlet-connect/jane-app-mcp)
- [FieldEdge MCP](https://github.com/cotlet-connect/fieldedge-mcp)
- [Phorest MCP](https://github.com/cotlet-connect/phorest-mcp)
- [DINGG MCP](https://github.com/cotlet-connect/dingg-mcp)
- [Invoice Simple MCP](https://github.com/cotlet-connect/invoice-simple-mcp)
- [PhotoShelter MCP](https://github.com/cotlet-connect/photoshelter-mcp)
- [RazorSync MCP](https://github.com/cotlet-connect/razorsync-mcp)
- [ShootProof MCP](https://github.com/cotlet-connect/shootproof-mcp)
- [Invoice2go MCP](https://github.com/cotlet-connect/invoice2go-mcp)
- [Appointy MCP](https://github.com/cotlet-connect/appointy-mcp)
- [Contractor+ MCP](https://github.com/cotlet-connect/contractor-plus-mcp)
- [Petpooja MCP](https://github.com/cotlet-connect/petpooja-mcp)
- [Teachers Pay Teachers MCP](https://github.com/cotlet-connect/teachers-pay-teachers-mcp)
- [Bloom MCP](https://github.com/cotlet-connect/bloom-mcp)
- [hipages MCP](https://github.com/cotlet-connect/hipages-mcp)
- [ServiceSeeking MCP](https://github.com/cotlet-connect/serviceseeking-mcp)
- [MyJobQuote MCP](https://github.com/cotlet-connect/myjobquote-mcp)
- [MyBuilder MCP](https://github.com/cotlet-connect/mybuilder-mcp)
- [Yelp for Business MCP](https://github.com/cotlet-connect/yelp-for-business-mcp)
- [Rosie MCP](https://github.com/cotlet-connect/rosie-mcp)
- [Roofr MCP](https://github.com/cotlet-connect/roofr-mcp)
- [FieldPulse MCP](https://github.com/cotlet-connect/fieldpulse-mcp)
- [YourTradebase MCP](https://github.com/cotlet-connect/yourtradebase-mcp)
- [JobNimbus MCP](https://github.com/cotlet-connect/jobnimbus-mcp)
- [Ascora MCP](https://github.com/cotlet-connect/ascora-mcp)
- [eLocal MCP](https://github.com/cotlet-connect/elocal-mcp)
- [Alto MCP](https://github.com/cotlet-connect/alto-mcp)
- [Rightmove Plus MCP](https://github.com/cotlet-connect/rightmove-plus-mcp)
- [TurboTenant MCP](https://github.com/cotlet-connect/turbotenant-mcp)
- [Innago MCP](https://github.com/cotlet-connect/innago-mcp)
- [Property Finder MCP](https://github.com/cotlet-connect/property-finder-mcp)
- [LoopNet MCP](https://github.com/cotlet-connect/loopnet-mcp)
- [Dotloop MCP](https://github.com/cotlet-connect/dotloop-mcp)
- [SkySlope MCP](https://github.com/cotlet-connect/skyslope-mcp)
- [ShowingTime MCP](https://github.com/cotlet-connect/showingtime-mcp)
- [connectMLS MCP](https://github.com/cotlet-connect/connectmls-mcp)
- [WebPT MCP](https://github.com/cotlet-connect/webpt-mcp)
- [Tebra MCP](https://github.com/cotlet-connect/tebra-mcp)
- [ChiroTouch MCP](https://github.com/cotlet-connect/chirotouch-mcp)
- [ChiroFusion MCP](https://github.com/cotlet-connect/chirofusion-mcp)
- [Semble MCP](https://github.com/cotlet-connect/semble-mcp)
- [Nookal MCP](https://github.com/cotlet-connect/nookal-mcp)
- [Cliniko MCP](https://github.com/cotlet-connect/cliniko-mcp)
- [Gentu MCP](https://github.com/cotlet-connect/gentu-mcp)
- [splose MCP](https://github.com/cotlet-connect/splose-mcp)
- [All apps connected through Cotlet.dev](https://github.com/cotlet-connect)

---

More: https://cotlet.dev/integrations/mcp/physitrack?utm_source=github&utm_medium=readme&utm_campaign=physitrack

Start free: https://cotlet.dev/signup?app=physitrack&utm_source=github&utm_medium=readme&utm_campaign=physitrack
