# Signatures of Addition

Things that pile up because adding was easier than deciding. Walk the surface against the sections that apply. Each row is a signature, why it tends to exist, and the usual rung on the ladder. "Usual" is a starting point; the evidence decides.

---

## Product and flows

| Signature | Why it exists | Usual rung |
| --- | --- | --- |
| A setting almost nobody changes | The team couldn't agree, so they shipped both | Default |
| A setting that exists for one customer | A sales ask that became permanent | Hide (flag or per-account config), or Delete if the customer churned |
| Two ways to reach the same place (sidebar + tab + command palette + button) | Each was added by a different team or release | Merge; keep the one people use, plus the shortcut |
| An onboarding tour or welcome carousel | The product didn't explain itself, so a layer was added on top | Delete; fix the empty state and labels instead |
| A wizard for a form with five fields | Someone felt the form was long | Delete the wizard; one page, fewer fields |
| A field that's optional and almost always empty | "Might be useful later" | Delete, or move to the detail view |
| A field you could infer (country from address, name from email, timezone from browser) | It was easier to ask than to infer | Default, editable |
| "Are you sure?" on a reversible action | Fear of mistakes | Delete; offer Undo instead |
| A confirmation toast for a change the user can already see | Feedback by habit | Delete |
| A feature with a flag that's been at 100% (or 0%) for months | The cleanup ticket never got scheduled | Delete the flag (and the dead branch) |
| A feature that duplicates a better one shipped later | Nobody owned removing the old one | Merge, migrate, Delete |
| Plans or tiers that differ by one feature | Pricing designed by addition | Merge tiers |
| A notification type nobody turns on, or everyone turns off | Default-on to drive engagement | Delete, or default off |
| Multiple empty, loading, and error designs for the same thing | Built per screen | Merge into one pattern |

## Interface

| Signature | Why it exists | Usual rung |
| --- | --- | --- |
| Icon + label + tooltip saying the same thing | Belt, suspenders, second belt | Shorten to label (or icon + tooltip for dense toolbars) |
| Border + shadow + background on the same card | Each was added to make it "pop" | Shorten to one |
| Several primary-looking buttons in one view | Every stakeholder wanted theirs prominent | Keep one primary, demote the rest |
| A heading with a subtitle that restates it | Template had a subtitle slot | Delete the subtitle |
| A tooltip explaining a label | The label is wrong | Shorten: fix the label, delete the tooltip |
| Section dividers between sections that already have headings | Visual noise by default | Delete; spacing does the job |
| A badge, dot, or counter that's always on | Engagement pressure | Delete, or show only when it changes something |
| Decorative icons in front of every list item | Filling space | Delete |
| A filter bar with eight filters where two get used | Built for completeness | Keep the two, hide the rest under "More" |
| Breadcrumbs on a two-level hierarchy | Pattern-library default | Delete; a back link is enough |
| A modal that could be inline | Modals feel "safe" | Delete the modal; edit in place |
| Pagination controls plus "load more" plus infinite scroll | Debates settled by doing all three | Merge to one |
| Animations on frequent actions | Polish added uniformly | Delete (high-frequency actions shouldn't animate) |
| Custom component where a native one works (select, date input, checkbox) | Design consistency | Delete the custom one when native is good enough |

## Copy

| Signature | Why it exists | Usual rung |
| --- | --- | --- |
| "Please", "simply", "just", "easily", "in order to" | Politeness filler | Shorten |
| Helper text that repeats the label ("Email — Enter your email") | Every field got helper text | Delete |
| An empty state with a paragraph | Explaining instead of offering the next action | Shorten to one line + the action |
| Errors that explain what happened but not what to do | Written from the system's view | Shorten to the fix |
| Button labels like "Submit", "OK", "Continue" | Defaults | Shorten to the verb that happens ("Send invite") |
| Marketing voice inside the product | Copy reused from the landing page | Shorten |
| The same idea said in title, subtitle, and body | Each layer written separately | Keep one |
| Legal or disclaimer text on every page | Applied once, copied everywhere | Keep it where it applies, Delete elsewhere |

## Code

| Signature | Why it exists | Usual rung |
| --- | --- | --- |
| An abstraction with one implementation | "We might need another" | Delete; inline it |
| A wrapper that forwards to one library call | Future-proofing | Delete |
| `V2` next to the original, both in use | Migration never finished | Merge, then Delete the old one |
| A config option with one value in every environment | Built configurable on day one | Delete; make it a constant |
| A feature flag fully rolled out | Cleanup never scheduled | Delete flag and dead branch |
| Props or parameters that every caller passes the same way | Optionality nobody uses | Default, then Delete |
| Defensive checks for states the types already rule out | Copy-paste caution | Delete |
| Dependencies used for one function | Faster to install than write | Shorten (native API or a few lines) |
| Utils folder with near-duplicate helpers | Nobody searched before writing | Merge |
| Comments restating the code | Habit | Delete |
| Tests that assert implementation details of deleted behavior | Never removed with the feature | Delete |
| Indirection layers (service → manager → handler → helper) for a simple call | Architecture by template | Merge layers |
| Generated or vendored code checked in alongside its source | Convenience once | Delete, generate at build |
| Commented-out code | "Just in case" | Delete; git remembers |

---

## Questions that find more

When the table runs dry, ask these about the surface:

- If we launched this today, which parts would make it into v1?
- What would we remove if we had to cut it in half by Friday?
- What does a new user ignore, and what does a power user ignore? Things both ignore go first.
- Which parts exist to compensate for another part being bad? Fix the bad part, delete the compensation.
- Which settings exist because we didn't make a decision?
- What do we maintain that nobody would miss if it silently broke for a week?
- What would the simplest competitor leave out?
