# Accessibility — every control has a name a screen reader can say

## What was happening

A screen reader announces a button by its name, and a button's name comes
from its text before its `title`. Most of GobboNet's small buttons are a
symbol with a tooltip: the thread buttons (📌 📁 🏷 ⇩ ✎ ×), the folder
buttons, the sidebar toggle (☰), home (⌂), attach (📎), web search (🔍),
Files (📄), the branch and variant arrows (◀ ▶), the attachment chip's ×,
the group editor's ×. They were read as "pushpin", "multiplication sign",
"black left-pointing triangle", with the tooltip, if at all, after. In a
sidebar of ten chats every thread's buttons sounded the same, so nothing said
which chat "Delete" belonged to. The header's model dropdown and nine number
boxes in CONFIG (auto-continue, card tools, max runtime, lore compression,
idle stand-down) had no name at all: their words sit beside them in a
`<span>`.

Across the bench's states, the browser's accessibility tree had 25 controls
with no name and 1,012 named only by a symbol (axe: select-name 17, label 8).
Inside the character and persona editors, which none of those states opens,
ten more had no name: the four colour pickers and their colour-code boxes
(their "Text" and "Dialog" labels point at nothing), and the character
editor's Carousel Prompt and Alternative Greetings checkboxes.

## The change

- `aria-label` on each, in plain words, matching its tooltip ("Pin",
  "Rename", "Delete"). Titles are unchanged, so the tooltips read as before.
- A chat's six buttons sit in a group named after the chat
  (`role="group"` on `.thread-ctrl`), so a screen reader says the chat's name
  once as focus enters its buttons, then each button by its word. A first
  version put the chat's name in every button's label ("Delete Showcase");
  with a chat named after a whole first message, that was six long sentences
  in a row and sounded like being stuck in one place. Folder buttons name
  their folder ("Rename folder Campaigns"): there are two, and folder names
  are short.
- The web search button's name is "Web search" whatever its title says; its
  `aria-pressed` already tells on from off.
- The Files button's name follows its tooltip, count included ("Files in this
  chat (5)"), set beside the title in `js/27-files-panel.js`.
- The model dropdown is labelled by the MODEL text beside it
  (`aria-labelledby="header-model-label"`). Each number box gets its
  sentence with the unit ("Wait between posts, seconds").
- `_groupActionBtn` (js/29-groups.js) uses the title as the name when the
  label is only a symbol.
- In the character and persona editors, the colour pickers are "Text color"
  and "Dialog color" and their boxes "Text color code" and "Dialog color
  code"; the Carousel Prompt and Alternative Greetings checkboxes are named by
  the titles beside them (`aria-labelledby`, ids already there).
- The four colour pickers also carry `aria-roledescription="color picker"`.
  In browse mode NVDA reads Chromium's colour picker (a "color chooser") as
  page layout, so it says only the colour, "clickable, 87% red 97% green 100%
  blue", even with a name; a role description makes it read the picker as a
  control, with its name: "Text color, color picker", then the colour.
- New attributes are placed after the ones `tests/test-card-tools.mjs` and
  `tests/test-search-switch.mjs` match on, so their patterns hold.

## Tests

- The audit, main against this change in the same states: unnamed
  controls 25 -> 0, symbol-only names 1,012 -> 0, axe select-name 17 -> 0,
  label 8 -> 0; every other axe finding unchanged.
- Nothing visible changes: screenshots before and after, 27 of 27 identical.
- `tests/test-accessibility-names.mjs` (new): every symbol-only button,
  number box, checkbox and colour control in `chat.html` has a name, the
  colour pickers say "color picker", and the buttons built in `js/` have
  theirs. The Files button and the lore compression box were hidden in
  every state the audit visits; this test is what found them.
- The same tree with every editor and form opened in turn (CONFIG with all its
  sections and the MCP form, the catalogue, the character, persona and group
  editors, SCHED's and MODS' editors, DATA, ABOUT): no unnamed control left;
  on main, 20.
- `tests/*.mjs` all pass.
