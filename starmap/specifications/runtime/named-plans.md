---
layout: page
title: Named plans
status:
  date: Oct 17, 2016
  is: Stable
knowledgelevel: L3
library: runtime
depends_on:
  - /starmap/specifications/runtime/Plan
  - /starmap/specifications/runtime/Performer
availability:
  - platform:
    name: Android
    label: "runtime-android as of v4.0.0"
    url: https://github.com/material-motion/material-motion-runtime-android
  - platform:
    name: iOS
    label: "runtime-objc as of v4.0.0"
    url: https://github.com/material-motion/material-motion-runtime-objc
---

# Named plans feature specification

This is the engineering specification for **named plans**.

## Overview

This feature enables the registration of _named plans_ to a runtime. Named plans can be added and removed by name, enabling fine configuration of a performer's behavior.

> A named plan cannot have `null` or an empty string as its name.

Example use case: associating a behavior with a target.

Example pseudo-code:

```swift
runtime.addPlan(matchLocationOf(cursor), named: "drag", to: target)

runtime.addPlan(springToLocation(origin), named: "drag", to: target)
```

Example use case: removing a behavior from a target.

Example pseudo-code:

```swift
runtime.addPlan(springToLocation(origin), named: 'spring', to: target)

runtime.removePlan(named: 'spring', from: target)
```

## Performer specification

Performers can receive named plans.

### Add/remove API

Performers can implement an add\/remove function.

> Performers may choose not to implement this API.

If one method is implemented, the other must be implemented as well.

Example pseudo-code:

```swift
protocol NamedPlanPerforming {
  function addPlan(NamedPlan, named: String)
  function removePlan(named: String)
}
```

### NamedPlan type

Provide a NamedPlan type.

Plans must conform to the NamedPlan type in order to indicate that they support being registered as named plans to a transaction.

# MotionRuntime specification

MotionRuntimes support named plans. Named plans are plans with a name associated via the transaction.

### Named APIs

Provide an `addPlan` and `removePlan` API with a name argument.

Note that the plan type must be a `NamedPlan`. Motion family designers use this type to indicate which plans support being named.

Example pseudo-code:

```swift
class MotionRuntime {
  function addPlan(NamedPlan, named: String, to: Target)
  function removePlan(named: String, from: Target)
}

// Associate a named plan with a target.
runtime.addPlan(plan, named: name, to: target)

// Remove any named plan from a target.
runtime.removePlan(named: name, from: target)
```

### Target-scoped names

Names are scoped to a target.

The runtime maintains a separate named plan mapping for each target.

### Remove-then-add

Two things happen when a named plan is added.

1. Remove any previously committed plan with the same name from the target's performers.

  _Note:_ This may be on a different performer instance on the same target. In the example above perhaps a PhysicsPerformer is needed for the second transaction, but not for the first.

2. Provide the relevant performer with the new named plan.

Example pseudo-code:

```swift
// Step 1
performerForName(name).removePlan(named: name)

// Step 2
performer = performerForPlan(plan)
performer.add(plan: plan, withName: name)
performerForName(name) == performer 
> true
```

### API contract

Here are the Performer's expectations for this API.

*Removing a name which was never added before:*

```swift
let runtime = MotionRuntime()
runtime.removeNamedPlan("foo")
```
 
* Nothing happens. No performer is created.

*Adding a name which was never added before:*

```swift
let runtime = MotionRuntime()
runtime.addNamedPlan(plan, "foo")
```

* A performer is created for plan. 
* The performer's `addNamedPlan(plan, "foo")` is called.

*Adding a name which was added before:*

```swift
let runtime = MotionRuntime()
runtime.addNamedPlan(plan, "foo")
runtime.addNamedPlan(plan2, "foo")
```

* A performer is created for plan. 
* The performer's `addNamedPlan(plan, "foo")` is called.
* The performer's `removeNamedPlan("foo")` is called.
* The performer's `addNamedPlan(plan2, "foo")` is called.

