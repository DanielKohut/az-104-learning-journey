# Module 3: Azure Compute

# Azure Autoscale – Core Concepts (AZ-104)

This document explains **Azure Autoscale fundamentals** at a conceptual level.
It is intended for **exam preparation (AZ-104)** and general understanding.

---

## What Is Azure Autoscale?

**Azure Autoscale** automatically adjusts the number of running instances for:
- App Service Plans
- Virtual Machine Scale Sets (VMSS)

Scaling decisions are based on **metrics** such as CPU usage, memory, or request count.

---

## Autoscale Building Blocks

An autoscale configuration is composed of:

- **Metric** – What is measured (e.g. CPU Percentage)
- **Condition / Rule** – When scaling should occur
- **Action** – What scaling action is taken
- **Limits** – Minimum, maximum, and default instance count
- **Schedule (optional)** – Time-based scaling

---

## Metric

A **metric** represents a measurable value.

Common examples:
- CPU Percentage
- Memory usage
- HTTP request count
- Queue length

Metrics are evaluated continuously by Azure Monitor.

---

## Condition (Rule)

A **rule** defines *when* a scale action is triggered.

A rule consists of:
- **Operator** (>, <, >=, <=)
- **Threshold** (e.g. 80%)
- **Duration**

Example:
CPU Percentage > 85% for 5 minutes

## Duration

**Duration** specifies how long a condition must be continuously met before a scaling action is triggered.

Key points:
- Scaling does **not** happen instantly
- The metric condition must be true for the entire duration
- Prevents scaling caused by short-lived metric spikes

Example:
CPU Percentage > 85% for 5 minutes

## Action

The **Action** defines what happens when an autoscale rule is triggered.

Possible actions:
- **Scale out** – increase the number of instances
- **Scale in** – decrease the number of instances

Actions are typically incremental, for example:
- Increase instance count by 1
- Decrease instance count by 1

---

## Cooldown

**Cooldown** is the waiting period after a scaling action is completed.

During the cooldown period:
- No additional scaling actions are allowed
- Metrics are still collected, but ignored for scaling decisions

Purpose of cooldown:
- Prevents rapid scale-in and scale-out oscillation
- Allows the platform to stabilize after scaling

---

## Duration vs Cooldown

| Concept   | Description |
|----------|-------------|
| Duration | How long a condition must be met before scaling starts |
| Cooldown | How long Azure waits after a scale action before allowing another |

Rule of thumb:

Minimum time between scaling actions = Duration + Cooldown

---

## Scale Out vs Scale In

### Scale Out

Scale out happens when workload increases.

Typical characteristics:
- Triggered by high load
- Often uses **Maximum** aggregation
- Reacts to overloaded or hot instances

Typical logic:
- Maximum CPU Percentage is higher than a defined threshold

### Scale In

Scale in happens when workload decreases.

Typical characteristics:
- Triggered by low load
- Often uses **Average** aggregation
- Ensures overall low utilization across all instances

Typical logic:
- Average CPU Percentage is lower than a defined threshold

---

## Metric Aggregation

Metric aggregation determines how metric values from multiple instances are evaluated.

Common aggregation types:
- Average
- Maximum
- Minimum
- Total

Guidelines:
- Use **Maximum** to detect single overloaded instances
- Use **Average** to detect overall low utilization

---

## Instance Limits

Autoscale always respects configured limits:

- Minimum instance count
- Maximum instance count
- Default instance count

Autoscale behavior:
- Never scales below the minimum
- Never scales above the maximum

---

## Autoscale Evaluation Flow

1. Metrics are collected by Azure Monitor
2. Autoscale rules are evaluated
3. Duration condition is satisfied
4. A scale action is executed
5. Cooldown period starts
6. Evaluation continues after cooldown

---

## Autoscale Schedules

Autoscale can also be based on schedules.

Common use cases:
- Scale out during business hours
- Scale in during nights or weekends

When a schedule is active:
- Scheduled rules take priority over metric-based rules

---

## What Autoscale Does NOT Do

- Autoscale does not react instantly
- Autoscale does not ignore cooldown
- Autoscale does not exceed defined instance limits
- Autoscale does not scale continuously

---

## Common Exam Traps

- Forgetting the cooldown period
- Assuming scaling happens every duration
- Ignoring aggregation type (Average vs Maximum)
- Forgetting minimum or maximum limits
- Trusting screenshots instead of the question text

---

## One-Line Memory Hook

Autoscale = Measure → Wait → Scale → Wait again

