---
layout: page
title: Streams
permalink: /starmap/specifications/primitives/streams/
enables:
  - /starmap/specifications/streams/IndefiniteObservable
---

# Streams

A light-weight implementation of the Observable pattern designed specifically for use in motion
design. It is based in spirit upon the
[ReactiveX](http://reactivex.io/documentation/observable.html) specification, though we've
intentionally trimmed the operators down to the bare essentials required for motion design.

## Streams vs Plans

Streams lean more toward the engineering side of the product development process than the design
tooling side. The reason for this is because of the heavy use of lambdas to build streams from
scratch. Lambdas are inherently difficult to introspect and represent in a tool without making other
trade-offs (performance, platform expectations). This being said, streams can be a powerful tool
for quickly prototyping interactions.

Consider the exploration of building an axis-locked Draggable interaction. We might use a stream to
prototype the desired behavior:

```swift
let stream = TranslationObservable(withPanRecognizer: pan)
  .map { CGPoint(x: 0, y: $0.y) }
  .subscribe { view.layer.position = $0 }
```

And eventually propose an update to the Draggable spec:

```
let draggable = Draggable(withGestureRecognizer: pan)
draggable.axis = .vertical
```

The general work flow we expect to follow is:

1. Rapidly prototype with streams.
2. Identify common patterns and propose Plan specifications.
3. Build out the plan specifications.

In other words, **use streams for prototyping and plans for production**.
