# SciCommons Frontend — GSoC 2026 PoC

Next.js 14 frontend for SciCommons. This branch adds an 
Editorial Dashboard implementing a Kotahi-inspired 
post-submission workflow.

## What is SciCommons

SciCommons is an open peer review platform where 
researchers submit papers to communities (acting as open 
journals), receive expert reviews, and discuss research 
publicly. Built by INCF over three GSoC cycles using 
Next.js, Django-Ninja, and a real-time Tornado server.

## The Problem

The platform had no UI for anything that happens after 
a paper is submitted. No editor could assign reviewers, 
track review progress, or record a formal decision. 
The `CommunityArticle` status field existed in the 
database but nothing ever changed it.

## What This PoC Adds

A new route `/editorial` with two tabs that together 
implement the complete post-submission pipeline.

### Tab 1 — Find Reviewers

<img width="1314" height="963" alt="Screenshot 2026-03-20 004515" src="https://github.com/user-attachments/assets/debd834d-48ac-450b-9734-f9984da73f03" />


Enter an Article ID and Community ID. The AI matching 
endpoint ranks community members by semantic similarity 
to the paper abstract using locally-hosted embeddings.

- Reviewer cards show match percentage with color-coded 
  left border (green >70%, yellow 50–70%, gray below)
- Invite button triggers a real API call — creates a 
  `ReviewerInvitation` record in the database
- Button transitions to a status pill on success
- Page auto-switches to Editorial Status tab after invite

### Tab 2 — Editorial Status

<img width="1897" height="894" alt="Screenshot 2026-03-20 004434" src="https://github.com/user-attachments/assets/0ead0066-2206-4b86-b149-bff26db6fd40" />


**Submission overview** — single line showing reviewer 
counts: invited, accepted, in progress, declined.

**Reviewer Status** — workflow stepper per reviewer 
showing their position in Kotahi's status flow:
```
Invited → Accepted → In Progress → Completed
```

Declined reviewers shown in a separate section.

**Editorial Decision**

<img width="1892" height="1009" alt="Screenshot 2026-03-20 004453" src="https://github.com/user-attachments/assets/d8846d1d-b9a1-4710-a95c-904b7df8cd22" />


- Current article status badge
- Decision form: Accept / Minor Revision / Major Revision / Reject
- Comments to author textarea
- Decision history showing past verdicts

Accept transitions status to `accepted` and enables 
publishing. Revise returns to `under_review`. Reject 
ends the active review cycle.

## New Files
```
src/app/editorial/
├── page.tsx              ← two-tab dashboard
├── ReviewerCard.tsx      ← card with invite mutation
├── WorkflowStepper.tsx   ← Kotahi status stepper
├── DecisionPanel.tsx     ← decision form + history
└── types.ts              ← TypeScript interfaces
```

## Architecture Notes

**TanStack Query v5** for all data fetching:
- `enabled: false` on suggestions query — only fetches 
  on button click, not on mount
- `useMutation` for invite — handles loading, error, 
  and success states without manual state management
- Auto-refetch of editorial status after every invite 
  or decision

**No new npm packages** — uses only what was already 
in `package.json`. Badge styling via Tailwind classes 
rather than importing a Badge component.

## Known Limitations

- `community_article_id` hardcoded to 1 — seed data 
  creates exactly one CommunityArticle
- No authentication on invite/decision actions — any 
  user can invite reviewers in the PoC
- WorkflowStepper shows all community members including 
  those invited via curl tests during development
- Dark theme applied — light mode users will see the 
  full SciCommons dark theme on this route

## Quick Start
```bash
# Install dependencies
npm install --legacy-peer-deps

# Create env file
echo "NEXT_PUBLIC_BACKEND_URL=http://localhost:8000" > .env.local
echo "NEXT_PUBLIC_REALTIME_URL=http://localhost:8888" >> .env.local

# Start dev server
npm run dev

# Open editorial dashboard
open http://localhost:3000/editorial
```

Requires backend running:
https://github.com/Yuvraj-018/My_SciCommons-backend/tree/gsoc-2026-poc

## Stack

- Next.js 14 (App Router)
- TypeScript (strict)
- TanStack Query v5
- ShadCN/UI (local components)
- Tailwind CSS

## GSoC 2026 — INCF, Project #18

Mentors: Armaan Alam, Mohd Faisal Ansari, Suresh Krishna
