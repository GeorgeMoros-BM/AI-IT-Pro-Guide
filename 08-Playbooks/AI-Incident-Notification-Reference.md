---
title: "AI Incident Notification Reference"
artifact_type: reference
audience:
  - executive
  - practitioner
  - legal
domain:
  - ai-governance
  - privacy
status: draft
maturity: advanced
last_updated: 2026-11-05
time_to_read: 8 minutes
review_frequency: quarterly
publish: false
client_safe: true
---
# Purpose

Use this with Phase 7 of [[AI-Incident-Response-Playbook]] to find which notification duties may apply in Canada, the United States, and the EU. It isn't legal advice and it isn't complete, so have counsel confirm what applies.

Checked on November 5, 2026. Each rule's wording varies, and these tables are short summaries of it. The Sources section separates official pages from summaries by law firms and vendors.

---
# Start Here

These rules mostly turn on the data, the sector, or the security impact, not on whether AI was involved. That's my reading of the rules below.

| Question | If Yes, Check |
|---|---|
| Was personal information involved? | Privacy rules wherever the affected people live |
| Was health information involved? | Ontario PHIPA and HIPAA |
| Is the organization a regulated financial institution? | OSFI, the US bank rule, FTC Safeguards, and NYDFS |
| Is it a US public company? | SEC Form 8-K Item 1.05 |
| Does it operate critical infrastructure? | Bill C-8 and CIRCIA |
| Did the system influence a consequential decision? | Colorado SB 26-189 and the EU AI Act |
| Does a contract set its own clock? | The contract |

Duties can follow the people affected, not only the organization. A Canadian company with customers in several US states may owe notice in each state.

---
# When the Clock Starts

Clocks start at different moments, so record all of them:
- **Awareness or discovery:** GDPR, HIPAA, FTC Safeguards, and California's breach law
- **Determination that a reportable event occurred:** Ontario FIPPA, SEC Item 1.05, the US bank rule, and NYDFS
- **Reasonable belief:** CIRCIA and the New York RAISE Act

---
# Canada: Federal

| Regime | Applies To | Trigger | Notify | Clock |
|---|---|---|---|---|
| PIPEDA | Private-sector organizations outside Alberta, BC, and Quebec, plus federally regulated businesses and data crossing borders | Breach of security safeguards with a real risk of significant harm | Privacy Commissioner and affected individuals | As soon as feasible. Keep records of every breach for 24 months |
| OSFI advisory | Federally regulated financial institutions | A technology or cyber incident that meets OSFI's criteria, including high or critical severity | OSFI Technology Risk Division and Lead Supervisor | Within 24 hours, or sooner if possible, then regular updates |
| Bill C-8, Part 2 | Designated operators in telecom, pipelines, power lines, nuclear, transportation, banking, and clearing and settlement | Cyber security incident involving a critical cyber system | Communications Security Establishment, then the regulator | Up to 72 hours, set by regulation |

Status to watch:
- **Bill C-8:** It received Royal Assent on June 15, 2026. Part 2 wasn't in force in the latest sources, and no operators had been designated. Suppliers may still see its terms in contracts with regulated clients.
- **Bill C-36:** It would replace PIPEDA's privacy provisions, and it's at second reading. The summaries reviewed don't describe changes to the breach duty.
- **AI law:** Canada has no binding federal AI statute. The Artificial Intelligence and Data Act died in January 2025.

---
# Canada: Provincial

| Regime | Applies To | Trigger | Notify | Clock |
|---|---|---|---|---|
| Quebec private-sector law | Enterprises in Quebec | Confidentiality incident with a risk of serious injury | Commission d'accès à l'information and affected individuals | Promptly. Keep the incident register for at least five years |
| Alberta PIPA | Organizations in Alberta | Real risk of significant harm, by a reasonable person's standard | Commissioner, who may require notice to individuals | Without unreasonable delay |
| BC PIPA | Organizations in BC | No mandatory duty | Voluntary report to the Commissioner, who expects notice where there's a real risk of harm | None set |
| Ontario PHIPA | Health information custodians | Theft, loss, or unauthorized use or disclosure of personal health information | Individuals in every case, and the IPC when listed criteria are met | As soon as reasonably possible. Annual statistics by March 1 |
| Ontario FIPPA | Provincial institutions | Real risk of significant harm | IPC and affected individuals | As soon as feasible after determining a breach occurred. Annual report by March 31 |

Ontario's municipal privacy law, MFIPPA, wasn't changed, so municipalities, police services, and school boards have no explicit duty. The IPC recommends similar practices. BC's 2026 Bill 9 amends public-sector law, not PIPA.

Health and public-sector laws in other provinces aren't covered here.

---
# United States: Federal and Sector

| Regime | Applies To | Trigger | Notify | Clock |
|---|---|---|---|---|
| SEC Form 8-K Item 1.05 | Public companies | A cybersecurity incident determined to be material | Investors, through a public filing | Four business days after determining materiality, which must happen without unreasonable delay |
| HIPAA | Covered entities and business associates | Breach of unsecured protected health information | Individuals, HHS, and media for more than 500 residents of a state. Business associates notify the covered entity | No later than 60 days after discovery. Breaches under 500 go to HHS annually |
| FTC Safeguards Rule | Non-bank financial institutions under the FTC | Unauthorized acquisition of unencrypted information on at least 500 consumers | FTC | As soon as possible, and no later than 30 days after discovery |
| Bank incident rule | Banks and bank service providers | An incident that materially disrupts operations, customer access, or financial stability | Primary federal regulator. Service providers notify affected bank customers | No later than 36 hours after determination. Service providers notify as soon as possible |
| NYDFS Part 500 | Entities licensed by New York DFS | A cybersecurity incident meeting the rule's criteria | Superintendent | No later than 72 hours after determination. Extortion payments have a 24-hour notice and a 30-day explanation |
| CIRCIA | Critical infrastructure entities, with final scope unknown | Covered cyber incident or ransom payment | CISA | 72 hours, or 24 hours after a ransom payment |

**CIRCIA isn't in effect yet.** The final rule went to the White House for review on October 1, 2026. The review can take 90 days or longer, and one law firm expects the rule to take effect at least 60 days after publication.

---
# United States: State

## Breach Laws

There's no general federal breach law. All 50 states and DC have their own, and one vendor survey counts 22 with fixed deadlines of 30, 45, or 60 days, and 29 that require notice without unreasonable delay. Check each affected resident's state.

California is a useful high-water mark. Since January 1, 2026, it requires notice to residents within 30 calendar days of discovery. If 500 or more residents are notified, the Attorney General must be told within 15 calendar days of notifying them.

## AI Laws

| Law | Applies To | Trigger | Notify | Clock | Status |
|---|---|---|---|---|---|
| California SB 53 | Frontier developers | Critical safety incident | California Office of Emergency Services | 15 days from discovery, or 24 hours if there's imminent danger | In effect since January 1, 2026 |
| New York RAISE Act | Frontier developers with models in New York | Critical safety incident | New York DFS oversight office | 72 hours, or 24 hours if there's imminent danger of death or serious injury | Takes effect January 1, 2027 |
| Colorado SB 26-189 | Deployers of automated decision tools | An adverse consequential decision materially influenced by the tool | The consumer | Within 30 days of the decision | Takes effect January 1, 2027, with enforcement tied to Attorney General rulemaking |

Most organizations using AI aren't frontier developers. For them, SB 53 and RAISE matter mainly as signals of what to ask vendors. Colorado's replacement law dropped the old duty to report algorithmic discrimination to the Attorney General.

A December 2025 executive order set up federal challenges to state AI laws. One tracker reported on August 26, 2026, that no statute or court had preempted any of them. Recheck this before you rely on a state law.

---
# European Union

| Regime | Applies To | Trigger | Notify | Clock |
|---|---|---|---|---|
| GDPR Articles 33 and 34 | Controllers and processors | A personal data breach, unless a risk to individuals is unlikely | Supervisory authority, and individuals if the risk is high. Processors notify the controller | Without undue delay, and where feasible within 72 hours of awareness |
| EU AI Act Article 73 | Providers of high-risk AI systems. Deployers inform the provider | Serious incident | National authorities | 15 days by default, 10 days for a death, 2 days for widespread incidents or serious critical infrastructure disruption |

**The Article 73 start date is unsettled.** Article 113 points to 2 August 2026, because Chapter IX isn't among its exceptions. Regulation (EU) 2026/1744 moved the high-risk requirements to December 2027 and August 2028, and some sources say Article 73 follows those dates. One analysis reports that no authority has resolved this. Agree on a written position with counsel now, because a two-day clock leaves no time to settle it.

The latest Commission guidance reviewed for this note is the September 2025 draft. Check for a final version.

---
# Not Covered

- countries outside Canada, the US, and the EU, including the UK
- US state health, insurance, and sector rules
- contract clauses, which need a review for each client or vendor

---
# Maintenance

Review this quarterly, and before you share it with a client. Bill C-8, Bill C-36, CIRCIA, Article 73, and state AI laws are the items most likely to change.

---
# Related Notes

- [[AI-Incident-Response-Playbook]]
- [[Governance-and-Risk]]
- [[Security-and-Privacy]]
- [[Prompt-Risk-Tiering]]

---
# Sources

Official sources:
- [Summary of privacy laws in Canada (OPC)](https://www.priv.gc.ca/en/privacy-topics/privacy-laws-in-canada/02_05_d_15/)
- [Breach of Security Safeguards Regulations (SOR/2018-64)](https://gazette.gc.ca/rp-pr/p2/2018/2018-04-18/html/sor-dors64-eng.html)
- [OSFI Technology and Cyber Security Incident Reporting](https://www.osfi-bsif.gc.ca/en/guidance/guidance-library/technology-cyber-security-incident-reporting)
- [Bill C-36 on LEGISinfo](https://www.parl.ca/legisinfo/en/bill/45-1/c-36)
- [Alberta OIPC: Breach Notification Requirements](https://oipc.ab.ca/breach-notification/)
- [Ontario IPC: FIPPA and MFIPPA amendments FAQ](https://www.ipc.on.ca/en/resources/updates-obligations-provincial-and-municipal-institutions/frequently-asked-questions-schedule-2-bill-194fippa-amendments)
- [SEC press release on cybersecurity disclosure](https://www.sec.gov/newsroom/press-releases/2023-139)
- [HHS Breach Notification Rule](https://www.hhs.gov/hipaa/for-professionals/breach-notification/index.html)
- [FTC: Safeguards Rule notification requirement](https://www.ftc.gov/business-guidance/blog/2024/05/safeguards-rule-notification-requirement-now-effect)
- [OCC: Computer-Security Incident Notification](https://www.occ.gov/news-issuances/bulletins/2021/bulletin-2021-55.html)
- [23 NYCRR 500.17](https://www.law.cornell.edu/regulations/new-york/23-NYCRR-500.17)
- [GDPR Article 33](https://gdpr-info.eu/art-33/)
- [GDPR Article 34](https://gdpr-text.com/read/article-34/)
- [European Commission: Draft guidance on serious AI incidents](https://digital-strategy.ec.europa.eu/en/consultations/ai-act-commission-issues-draft-guidance-and-reporting-template-serious-ai-incidents-and-seeks)
- [Regulation (EU) 2026/1744](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=OJ%3AL_202601744)

Secondary sources:
- [Fasken: Quebec regulations for confidentiality incidents](https://www.fasken.com/en/knowledge/2022/07/regulations-for-confidentiality-incidents)
- [ClearBreach: BC PIPA reform](https://clearbreach.ca/guides/bc-pipa-reform-update/)
- [Ontario Medical Association: Privacy breach reporting](https://oma.org:443/practice-professional-support/running-your-practice/operations-and-practice-management/cybersecurity/privacy-and-secure-electronic-communication/privacy-breach-reporting-requirements)
- [Lavery: Bill C-8](https://www.lavery.ca/en/publications/our-publications/6475-bill-c-8-a-new-federal-cyber-security-framework-for-telecommunications-and-critical-cyber-systems.html)
- [Fusion Computing: Bill C-8 status](https://fusioncomputing.ca/what-is-bill-c-8/)
- [Gowling WLG: Bill C-36 tabled](https://gowlingwlg.com/en/insights-resources/articles/2026/ottawa-tables-long-awaited-federal-privacy-reform-legislation)
- [Cloud Forces: Canada's AI regulation after AIDA](https://www.cloudforces.ca/blog/canada-ai-regulation-vacuum-2026-sme-guide)
- [Vaquill: SEC Item 1.05 playbook](https://www.vaquill.ai/blog/sec-cybersecurity-disclosure-item-1-05-gc-playbook)
- [Covington: CISA submits CIRCIA final rule](https://www.insideprivacy.com/critical-infrastructure/cisa-submits-final-circia-rule-for-omb-review/)
- [Paubox: CIRCIA clocks and scope](https://www.paubox.com/blog/circia-final-rule-white-house-deadlines-set-scope-open)
- [State breach law survey (Data Breach Cost)](https://databreachcost.com/breach-notification-laws)
- [Alston & Bird: California and Oklahoma updates](https://www.alstonprivacy.com/key-breach-notification-updates-in-california-and-oklahoma-for-2026/)
- [Wharton: SB 53](https://ai-analytics.wharton.upenn.edu/wharton-accountable-ai-lab/sb-53-what-californias-new-ai-safety-law-means-for-developers/)
- [Future of Privacy Forum: RAISE Act vs. SB 53](https://fpf.org/blog/the-raise-act-vs-sb-53-a-tale-of-two-frontier-ai-laws/)
- [Legal 500: RAISE Act finalized](https://www.legal500.com/intelligence/united-states/technology/new-york-finalizes-raise-act-for-frontier-ai-models;-law-takes-effect-january-1-2027)
- [Eckert Seamans: Colorado SB 26-189](https://www.eckertseamans.com/legal-updates/colorado-repeals-and-replaces-its-landmark-ai-statute-what-businesses-need-to-know)
- [Glacis: State AI laws tracker](https://www.glacis.io/guide-state-ai-laws)
- [CIO: Article 73 reporting clocks](https://www.cio.com/article/4218777/the-eu-ai-act-just-gave-you-a-breach-notification-clock-you-didnt-know-about.html)
- [iLert: Article 73 dates after the omnibus](https://www.ilert.com/blog/eu-ai-act-2026-incident-response)
- [Kovrr: When Article 73 starts applying](https://www.kovrr.com/blog-post/the-ai-incident-reporting-duty-nobody-can-date)

---
# Changelog

- **2026-11-05**: Created from Phase 7 of the playbook, and added Canadian provincial and sector rules and US rules
