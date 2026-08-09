# Plan: Sync Lovable Export + Lovable Paste Guide

## Context

The user wants to take the MicroCards design from Figma Make into Lovable for Caleb to build on.
Two problems to solve:
1. `MicroCards-Lovable-Export.tsx` is missing 2 screens that exist in `App.tsx` — `landing` and `about`
2. Builder.io (and other plugin approaches) don't work here because **Figma Make is not a Figma design file** — it's a live React code environment. Plugins that read Figma layers have nothing to read. The source code IS the export.

## Why Plugins Don't Work Here

Figma Make is a code editor (React/Vite) that runs inside the Figma interface. It has no Figma layers, frames, or design tokens that plugins like Builder.io can parse. Those plugins scan the Figma layer tree — which doesn't exist here. The only exportable artifact is the `.tsx` source code itself.

## What's Missing From the Lovable Export

| Screen | In App.tsx | In Lovable Export |
|--------|-----------|------------------|
| `landing` | ✅ `LandingScreen` (~120 lines) | ❌ Missing |
| `about` | ✅ `AboutScreen` (~130 lines) | ❌ Missing |
| All others (8 screens) | ✅ | ✅ |

`MicroCards-Lovable-Export.tsx` is 1,568 lines; `App.tsx` is 1,942 lines. The ~374-line gap is entirely the two missing screens plus the `VideoPlaceholder` component (already added to App.tsx but not yet synced to the export).

## Implementation Plan

### Step 1 — Read both files fully
Read `src/app/App.tsx` and `MicroCards-Lovable-Export.tsx` completely to extract the exact `LandingScreen` and `AboutScreen` JSX.

### Step 2 — Sync VideoPlaceholder into the export
`VideoPlaceholder` component was added to App.tsx but the export still has its older version. Confirm the export's version matches the latest App.tsx version.

### Step 3 — Add missing screens to the export
In `MicroCards-Lovable-Export.tsx`:
- Update `type Screen` to add `"landing" | "about"`
- Add `LandingScreen` component (copied from App.tsx)
- Add `AboutScreen` component (copied from App.tsx)
- Add both to the `screens` record
- Add both to `SCREEN_ORDER` at the front: `["landing", "about", "welcome", ...]`

### Step 4 — Write the full updated export as a code block in chat
Since the user wants to email the code to Caleb, write the **entire** `MicroCards-Lovable-Export.tsx` content as a single copy-pasteable code block in the chat. No file download needed.

### Step 5 — Include Lovable paste instructions in the chat
After the code block, write the exact steps Caleb follows in Lovable:
1. Create new Lovable project (React + Vite + Tailwind)
2. Install: `npm install motion lucide-react recharts`
3. Replace `src/App.tsx` with the pasted code
4. Remove the phone-frame wrapper comment block when ready for real mobile

## Files to Modify
- `/workspaces/default/code/MicroCards-Lovable-Export.tsx` — add 2 screens + sync VideoPlaceholder

## Verification
- Confirm Screen type union has all 10 members
- Confirm `screens` record and `SCREEN_ORDER` include `landing` and `about`
- Confirm the code block written in chat is complete (no truncation)
