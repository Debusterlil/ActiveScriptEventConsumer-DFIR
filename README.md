# ActiveScriptEventConsumer DFIR Case Study

Defensive DFIR case study of a permanent WMI subscription using
`ActiveScriptEventConsumer`.

The repository focuses on:

- detection with Sysmon and PowerShell logging;
- WMI hunting;
- event and memory artifact correlation;
- incident response;
- safe cleanup validation;
- Sigma detection and correlation rules.

## Start here

1. Begin with [`case-study/`](case-study/) to review the sanitized timeline,
   evidence matrix and validation results.
2. Open [`detections/sigma/`](detections/sigma/) to examine the Sigma rules.
3. Use [`hunting/`](hunting/) for read-only investigation guidance.
4. Use [`response/`](response/) for containment, cleanup and validation steps.

## Repository structure

- [`detections/sigma/`](detections/sigma/) — Sigma detection and correlation rules;
- [`hunting/`](hunting/) — read-only hunting checklist;
- [`response/`](response/) — incident-response plan;
- [`case-study/`](case-study/) — sanitized timeline, evidence matrix and validation;
- [`tools/`](tools/) — defensive analysis utilities.

## Safety

This repository does not include:

- code that creates a permanent WMI subscription;
- complete consumer script contents;
- memory dumps;
- EVTX evidence;
- WMI repository fragments;
- private workstation identifiers;
- internal investigation archives.

The laboratory emulation was performed in an isolated virtual machine.

The published materials are intended exclusively for defensive research,
detection engineering, incident response and DFIR education.

## MITRE ATT&CK

[T1546.003 — Windows Management Instrumentation Event Subscription](https://attack.mitre.org/techniques/T1546/003/)

## Publication

A detailed Russian-language article describing the laboratory, investigation
and detection methodology is currently under editorial review.

The publication link will be added after release.
