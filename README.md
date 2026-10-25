# Kadal

**Turning every fish sale into a credit history for Tamil Nadu’s women fish vendors.**

Kadal (கடல், “the sea”) is a Tamil, voice-first mobile app backed by a shared ledger on NPCI’s
DRUNIX platform. It records each vendor’s purchases, sales and repayments as she goes about her
day, and turns that record into access to cheaper, formal credit.

- **Team:** One Eight Eight
- **Registration ID:** STU0662
- **Hackathon:** Drunix Hackathon in collaboration with Citi (India Blockchain Forum)
- **Track:** Financial Inclusion

> **Status:** idea stage. The problem research, user journey and system architecture are done.
> The working prototype will be built in Round 2 (10–22 Nov 2026). Nothing in this repository
> is a working product yet.

---

## The problem

Women fish vendors in Tamil Nadu run a real business every day: they buy fish at the harbour
auction at dawn, sell it by noon, and start again the next day. But every rupee moves in cash or
on spoken promises, so none of it leaves a record a bank can read.

Without a record, banks treat them as having no income. For working capital, the 61-day fishing
ban or emergencies, vendors turn to auctioneers, relatives and moneylenders. Field studies in
Tamil Nadu report short-term informal rates of up to 120% a year. Vendors typically take home
₹50–150 a day.

These women are creditworthy but invisible. Lenders want to serve them; what they lack is
reliable data to underwrite them cheaply.

## The solution

Kadal gives each vendor a trusted record of the business she already runs.

| Feature | What it does |
|---|---|
| Voice auction log | The auctioneer speaks the sale in Tamil. Speech recognition fills in a structured entry and reads it back. Both parties confirm and sign on their phones, even offline. |
| Morning credit line | A partner bank or regulated lender offers a small daily working-capital limit (about ₹2,000–3,000 to start), sent by UPI before the auction. |
| Sales capture | Customers pay by UPI QR, or she speaks cash and credit sales into the app. Customer dues are tracked, with Tamil voice reminders. |
| Evening repayment | With her consent, the day’s loan is repaid from her UPI collections. |
| Lean-season savings | A small amount (for example ₹20 a day) goes into a recurring deposit on good days, as a cushion for the fishing ban. |
| Income profile | Months of signed records become a verified income summary she can share through Account Aggregator, only when she chooses. |

**Existing players join as partners.** The auctioneer’s same-day advances are recorded too, so
each one she repays adds to her history. SHG federations can act as guarantors, and several
lenders can join the same network and compete for her business.

**Kadal is the technology and data layer, not a lender.** Money never sits on the ledger or in
the app. Disbursement and repayment flow directly between the vendor’s bank account and the
regulated lender over UPI, in line with RBI’s digital lending rules.

## Architecture (proposed)

### Client
- Flutter app for low-cost Android phones, offline-first, with an encrypted local store and a
  signed transaction queue.
- Tamil speech recognition through Bhashini and AI4Bharat models, adapted to harbour vocabulary
  (local fish names, spoken numbers).
- Large-button tap mode for noisy harbours.
- IVR fallback for feature phones, and an assisted mode so SHG leaders can enter records for
  members.

### Integration
- An API gateway connects the app to DRUNIX through the Fabric SDK.
- UPI through a sponsor bank.
- Account Aggregator for consented data sharing.

### Ledger: a permissioned DRUNIX network

The network has four kinds of organisation: the harbour auction association, the SHG federation,
the partner bank or lender, and an optional insurer.

Go chaincode contracts:

| Contract | Purpose |
|---|---|
| `AuctionSale` | Records a sale, accepted only when buyer and auctioneer both sign |
| `Advance` | Records the auctioneer’s same-day advances and their repayment |
| `CreditLine` | Records the lender’s daily working-capital limit and each disbursement |
| `Repayment` | Records repayments from UPI collections |
| `SavingsSweep` | Records the daily savings set aside |
| `ConsentedIncomeSummary` | Produces the income summary she agrees to share |

Individual sale details stay in private data collections, visible only to the parties in the
deal. Lenders see only the summaries a vendor has consented to share.

### Credit model
A simple model reads ledger data (days active, average daily turnover, repayment streaks,
seasonality) and recommends a limit. The lender makes the final decision.

### DRUNIX elements used
- Lite peer / committing peer separation
- Stateless validation service
- State database (LevelDB / CouchDB / YugabyteDB)
- KeyDB transient store and private data
- Go chaincode
- Multi-organisation network design
- Falcon for network deployment
- APIs, SDKs and integrations

## Planned repository layout

This is a starting point and will change as the prototype is built.

```
.
├── app/            Flutter app (Tamil, voice-first, offline-first)      planned
├── chaincode/      Go chaincode contracts                              planned
├── gateway/        API gateway: Fabric SDK, UPI sandbox, Account Aggregator   planned
├── network/        DRUNIX test network configuration (Falcon)          planned
├── credit-model/   Limit recommendation from ledger data               planned
├── docs/           Proposal and architecture notes                     planned
└── kadal-deck/     Pitch deck (browser, PDF and PowerPoint)            exists
```

## Getting started

There is nothing to run yet. Setup instructions will be added once the Round 2 prototype is in
place. The pitch deck can already be opened; see [`kadal-deck/README.md`](kadal-deck/README.md).

## Roadmap

- [x] Problem research
- [x] User journey
- [x] System architecture
- [ ] **Round 2 prototype.** The full loop running on a DRUNIX test network deployed with Falcon:
  a Tamil voice sale at a mock auction, signed confirmation, a morning credit disbursement in a
  UPI sandbox, sales capture, evening repayment, and the resulting income summary.
- [ ] Short field survey of women vendors at a Chennai harbour, to check rates, ticket sizes and
  phone use
- [ ] Pilot with 50 vendors at one harbour
- [ ] Grow to 500 vendors within a season
- [ ] Expand to other Tamil Nadu landing centres, Kerala and Puducherry

## Dependencies

- A regulated lending partner and a sponsor bank for UPI
- Buy-in from one harbour auction association and one SHG federation for the pilot
- Tamil speech accuracy good enough for noisy harbours, improved with field recordings
- Account Aggregator integration, and each lender’s own credit approval for lending at scale

## Expected impact (modeled, to be validated in the pilot)

These numbers assume a vendor keeps about ₹3,000 of borrowed working capital through the year.
The 120% informal rate is the highest cited in Tamil Nadu field studies; the ~24% formal rate is
our assumption. The difference is about ₹2,880 a year per vendor.

| Scope | Modeled annual saving |
|---|---|
| 500 vendors at the pilot harbour | ₹14.4 lakh |
| 35,835 people in fish marketing in Tamil Nadu (Census 2016) | ₹10.3 crore |
| 1,82,654 women in marine fish marketing in India (Census 2010) | ₹52.6 crore |

Targets:
- 60% of active pilot users (about 300 women) qualify for a formal credit line within six months.
- A ₹5,000 lean-season buffer per vendor (₹20 a day across about 250 selling days).

## Team

**One Eight Eight** · Registration ID STU0662

## License

To be decided.
