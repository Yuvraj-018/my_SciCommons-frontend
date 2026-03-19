# SciCommons Frontend — GSoC 2026 PoC

Next.js 14 frontend for SciCommons. This branch 
(`gsoc-2026-poc`) adds an Editorial Dashboard 
implementing a Kotahi-inspired workflow.

## What this PoC adds

A new route `/editorial` with two tabs:

### Tab 1 — Find Reviewers
AI-powered reviewer recommendations ranked by semantic 
similarity between the paper abstract and reviewer bio.

![Find Reviewers](screenshots/find_reviewers.png)

- Enter Article ID + Community ID
- Click Find Best Reviewers
- See ranked reviewer cards with match percentages
- Click Invite to create a ReviewerInvitation record
- Card transitions from Invite button to status pill

### Tab 2 — Editorial Status
Full editorial workflow panel showing reviewer 
progress and decision management.

![Editorial Status](screenshots/editorial_status.png)

**Reviewer Status** — workflow stepper showing each 
reviewer's position in the Kotahi status flow:
`Invited → Accepted → In Progress → Completed`

Declined reviewers shown separately.

**Editorial Decision** — decision form with:
- Accept / Minor Revision / Major Revision / Reject
- Comments to author
- Decision history

![Decision Panel](screenshots/decision_panel.png)

## New files
```
src/app/editorial/
├── page.tsx           — two-tab dashboard
├── ReviewerCard.tsx   — reviewer card with invite flow
├── WorkflowStepper.tsx — Kotahi status stepper
├── DecisionPanel.tsx  — decision form + history
└── types.ts           — TypeScript interfaces
```

## Architecture notes

Informed by studying:
- **Kotahi** — Team tab left-to-right reviewer progression,
  Decision tab Submit + Publish button logic
- **OJS** — submission tracking metrics

## Setup
```bash
# Install dependencies
yarn install  
# or
npm install --legacy-peer-deps

# Create env file
echo "NEXT_PUBLIC_BACKEND_URL=http://localhost:8000" > .env.local
echo "NEXT_PUBLIC_REALTIME_URL=http://localhost:8888" >> .env.local

# Start dev server
yarn dev

# Open editorial dashboard
open http://localhost:3000/editorial
```

## Stack

- Next.js 14 (App Router)
- TypeScript
- TanStack Query v5
- ShadCN/UI
- Tailwind CSS

## Backend

Requires the backend repo running:
https://github.com/Yuvraj-018/My_SciCommons-backend/tree/gsoc-2026-poc
