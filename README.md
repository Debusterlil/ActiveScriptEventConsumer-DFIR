# ActiveScriptEventConsumer DFIR Case Study

Defensive DFIR case study of a permanent WMI subscription using
`ActiveScriptEventConsumer`.

The repository focuses on:

- detection with Sysmon and PowerShell logging;
- WMI hunting;
- event and memory artifact correlation;
- incident response;
- safe cleanup validation;
- Sigma rules.

## Repository structure

- `detections/sigma` — Sigma detection and correlation rules;
- `hunting` — read-only hunting checklist;
- `response` — incident-response plan;
- `case-study` — sanitized timeline, evidence matrix and validation;
- `tools` — reserved for defensive analysis utilities.

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
The published materials are intended for defensive research and detection.

## MITRE ATT&CK

- T1546.003 — Windows Management Instrumentation Event Subscription
