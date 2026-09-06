# Defender XDR, Sentinel, and the AI Detour

My original goal for this stage of the lab was straightforward: continue working through SC-200 while bringing more of the Microsoft security stack into my hybrid Active Directory environment.

As tends to happen with home labs, the road ended up being more interesting than the map.

This phase brought together Microsoft Defender for Endpoint, Defender for Identity, Defender XDR, Microsoft Sentinel, and Defender for Cloud Apps. It also led to an unexpected side project: 

experimenting with how an AI assistant could be given useful access to a SOC environment without giving it broad control over that environment.

---

## Expanding the Defender Environment

### Microsoft Defender for Endpoint

The first objective was getting my Windows systems onboarded into Microsoft Defender for Endpoint (MDE).

I configured MDE onboarding through Active Directory Group Policy. The Domain Controller successfully received the configuration and onboarded into Defender through the GPO deployment.

My Windows 11 VM did not cooperate quite as nicely.

Rather than assuming the policy had worked because it worked on the Domain Controller, I verified the endpoint state and found that the Windows 11 system had not successfully onboarded. 

I eventually ran the Microsoft onboarding script directly on the VM, after which the endpoint appeared correctly in Defender.

The result was two MDE-managed systems in the lab, but the more useful lesson was that a successful deployment mechanism on one system does not prove that the same mechanism succeeded everywhere.

For a security operations environment, verification matters more than assuming that configuration equals implementation.

---

## Adding Identity Visibility with Defender for Identity

The Domain Controller was also onboarded to Microsoft Defender for Identity (MDI), allowing the lab to begin incorporating Active Directory identity activity into the security picture.

I also configured the Microsoft Entra ID integration used by the environment so that identity information could be incorporated into the broader Defender experience.

This was an important step for the direction I want the lab to take.

Endpoint telemetry can tell part of an investigation story, but identity telemetry adds another perspective: users, authentication activity, Active Directory behavior, and relationships between identities and systems.

That becomes particularly important as I move further into SC-200 investigation scenarios where an incident may span multiple security domains.

---

## Connecting Microsoft Sentinel and Defender XDR

Microsoft Sentinel had already been enabled against my Log Analytics workspace, with Windows security telemetry being collected into the `SecurityEvent` table.

The next step was bringing Sentinel and Defender XDR closer together through Microsoft's unified security operations experience.

This gives the lab both sides of an architecture I specifically wanted experience with:

- XDR for security signals and investigation across Defender products
- SIEM for centralized telemetry, KQL, correlation, and broader analysis

Rather than learning these as completely isolated products, I can now begin learning how they contribute different evidence to the same investigation.

That will become increasingly useful as I move further into KQL, alert triage, Advanced Hunting, and incident investigation.

---

## Defender for Cloud Apps

I also activated a Defender for Cloud Apps trial because it is covered in the SC-200 course I am currently working through.

Not every component in this lab is intended to remain permanently licensed or enabled. Some exist specifically so I can get hands-on experience with a technology while studying it.

That has led to another lesson from building this environment: licensing and cost management are part of running a cloud security lab.

---

## Keeping an Enterprise Security Lab Affordable

One of the challenges with reproducing an enterprise Microsoft security environment at home is licensing.

My approach is evolving as I learn more about Microsoft's licensing and consumption models.

I currently use an Office 365 E5 trial and separately obtained MDE and MDI licensing. I also activated a Defender for Cloud Apps trial for the relevant portion of my SC-200 training.

I purchased the standalone MDE and MDI licensing before learning more about some of the protection options available through Microsoft's cloud workload protection platform (CWPP).

That discovery changed how I plan to approach licensing going forward.

When my current Office 365 E5 trial ends, I plan to evaluate moving into a Microsoft 365 E5 trial. At that point, I can disable overlapping standalone subscriptions while using the capabilities available through the trial.

I may also extend the current trial first if that better matches where I am in the course.

Another important discovery has been understanding the difference between a headline monthly price and the way a service is actually billed.

For a home lab, systems do not need to run 24 hours a day. Where services are billed according to protected-resource usage, shutting down or deallocating lab systems when they are not needed can significantly change the

practical cost of experimenting with enterprise technology.

There are other costs to watch as well. Log Analytics ingestion, retention, cloud resources, security plans, and licensing can all contribute independently.

The lesson has become:

> Don't just ask what a service costs. Understand what causes it to cost money.

Cost management has therefore become another part of the lab rather than something separate from the technical work.

---

# The AI Detour

While building out Defender XDR, I started thinking about another question:

> How could I use AI as part of my SOC training without simply handing an AI broad access to my security environment?

That question resulted in a considerably larger detour than I originally expected.

Rather than starting with what I wanted an AI system to do, I decided to start with what it should be **allowed** to do.

## Work-SOC-Reader

I created a dedicated non-human application identity in Microsoft Entra ID called `Work-SOC-Reader`.

The identity was given specifically selected application permissions for read-only access to Defender information, including:

- Defender XDR incidents
- MDE machine information
- Advanced Hunting
- vulnerability information
- software inventory
- security configuration
- security recommendations
- security baseline assessment information
- threat intelligence indicators

The important part was not simply making the API calls work.

I wanted the authorization boundary to remain understandable.

The identity should be able to observe selected security information, but it should not be able to modify endpoints, change security configuration, contain devices, alter Azure resources, or perform other 

administrative actions.

---

## Guardrails Before Functionality

I established several rules for the integration:

- Read-only access by default
- Least privilege
- No infrastructure modification
- No arbitrary raw-API proxy
- No credentials or secrets exposed in output or committed to Git
- Explicitly allowlisted operations
- Repository access does not imply authorization to modify live infrastructure

I then used ChatGPT Work to implement the MCP connector within those constraints.

This distinction is important: I did not personally write the connector implementation from scratch. My role was defining the identity, permission model, security boundaries, and guardrails, then using AI 

to implement and test against those requirements.

That became an interesting exercise in a different kind of AI usage.

Instead of asking:

> "Can AI build this?"

the more important questions became:

> "What should AI be permitted to access?"

and:

> "How can I verify that the resulting system stays inside that boundary?"

---

## What the Existing Access Can Become

The connector started as a way to provide controlled visibility into Defender, but the permissions already in place have potential uses directly related to my SC-200 training.

For example, read-only incident and machine access could eventually allow AI to review the same Defender evidence I am investigating.

Advanced Hunting access opens another possibility as I get deeper into KQL: working with actual lab telemetry while maintaining a read-only authorization model.

Vulnerability, software, configuration, and recommendation data could provide additional endpoint context when investigating an alert instead of treating the alert as an isolated event.

Threat intelligence could provide another source of context during IOC and hunting exercises.

The goal is not to have AI perform the investigation for me.

The workflow I want to eventually practice is closer to:

    Generate activity
          ↓
    Investigate it myself
          ↓
    Query and correlate evidence
          ↓
    Form and document a conclusion
          ↓
    Have AI review the available evidence
          ↓
    Compare its observations with my analysis

Used that way, AI becomes a second set of eyes rather than a replacement for developing investigation skills.

---

## Where I Chose to Stop

Once the basic integration was working, it became tempting to keep expanding it.

I investigated adding additional narrowly scoped capabilities for:

- Microsoft Sentinel configuration
- Defender for Cloud plans
- Defender for Identity sensor state
- Defender for Cloud Apps discovery state
- automated comparison of the live environment against `LAB_STATE.md`

All of those are technically possible.

They also require additional API surfaces, Azure RBAC, Microsoft Graph permissions, authentication paths, MCP tools, testing, and maintenance.

At some point I realized that my SC-200 lab was becoming an MCP engineering project.

Security Engineering is one of my longer-term career goals, so this was a valuable detour and exactly the kind of subject I want to explore further.

But the immediate goal is still to complete SC-200 and continue developing the skills needed for a SOC Analyst role.

So I stopped.

The additional engineering work isn't abandoned. It is deliberately deferred until expanding it supports the training rather than distracting from it.

Sometimes scope control is part of the lesson.

---

## What's Next?

The Microsoft security pieces of the lab are now much more interconnected than when I started.

I have endpoint, identity, SIEM, and XDR technologies available to continue learning with, along with a read-only AI integration that I can revisit when the SC-200 material reaches deeper investigation and hunting exercises.

The next focus is therefore back where this journey started:

- KQL
- Defender XDR investigation
- Advanced Hunting
- alert triage
- identity investigation
- cross-domain correlation
- incident documentation

The roadmap for this lab was never intended to prevent detours.

Sometimes the unplanned pit stops are where the most interesting learning happens.
