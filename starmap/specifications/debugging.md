---
layout: page
title: Debugging
status:
  date: Oct 25, 2016
  is: Drafting
---

# Debugging

TODO: Discuss debugging tools.

- Enumerating Plans and Executors currently active in a MotionRuntime.
- Pausing animations.
- Slowing down animations.
- Scrubbing through animations.
- Direct tool integrations.

Mockup of debugging tool:

![]({{ site.url }}/assets/MaterialMotionDebugger.svg)

Notes:

- Left column shows element hierarchy.
- Right column shows current state + timeline.
- Timeline shows performer activity state and plan association over time.
- Current state shows graph of performers and their exposed values.
- If no element is selected, all performers should be shown in right-hand column.

Related to the Server in many ways. Likely a good deal of overlapping inspection tools.
