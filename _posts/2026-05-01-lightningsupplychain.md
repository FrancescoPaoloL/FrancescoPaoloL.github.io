---
layout: default
title: "When Malware Hides in Developer Tools"
date: 2026-05-01
categories: [AI, Security]
tags: [supply-chain, pytorch, malware, claude-code, ai-security, npm, pypi]
---

On April 30, 2026, versions 2.6.2 and 2.6.3 of the PyPI package `lightning` (PyTorch Lightning) were compromised with a malicious payload. Semgrep published a detailed analysis with all the IOCs — you can find it [here](https://semgrep.dev/blog/2026/malicious-dependency-in-pytorch-lightning-used-for-ai-training).

---

## How it gets in

A single `pip install lightning` is all it takes.

The infected versions contain a hidden `_runtime/` folder with a 14.8 MB obfuscated JavaScript payload that executes automatically the moment you import the library.

If `lightning` is anywhere in your dependencies, direct or transitive, you are exposed.

---

## How it stays

This is the part I find most interesting.

Once inside a repository, the malware writes two config files:

- `.claude/settings.json`, a hook for Claude Code
- `.vscode/tasks.json`, a task for VS Code

Both are set to run a dropper every time a developer opens the project; I repeat: just opening the folder is enough.

Semgrep describes this as one of the first documented cases of malware abusing Claude Code's hook system in a real-world attack. That detail is worth keeping in mind: the tools we use every day are becoming persistence vectors.

---

## How it spreads

If the malware finds npm credentials, it uses them to inject itself into every package those credentials can publish. It bumps the version number and republishes. Anyone who installs those packages downstream gets hit too.

The initial vector is PyPI. The propagation happens through npm. Two ecosystems, one attack.

---

## What it steals

GitHub tokens, cloud credentials (AWS, Azure, GCP), CI secrets, environment variables. It uses four parallel channels to exfiltrate them, including a dead-drop built on the GitHub commit search API.

---

## A note on lockfile-analyzer

I have a personal learning project called [`lockfile-analyzer`](https://github.com/FrancescoPaoloL/lockfile-analyzer) a CLI tool that parses npm and yarn lockfiles looking for risk signals: typosquatting, integrity hash validation, secrets in metadata, suspicious lifecycle scripts.

Would it have caught this attack? Partly.

Obviously, the initial vector (the PyPI package `lightning`) is outside its scope: it currently only handles npm and yarn lockfiles, not `requirements.txt` or `poetry.lock`. But the npm propagation phase, where the malware reinfects packages by injecting a malicious `preinstall` script, is exactly the kind of signal the tool looks for.

I guess this incident is a good argument for extending it to the Python ecosystem. Maybe I will get there.

---

## The point

AI security is not just about models. It is about everything around them: training libraries, editor hooks, CI pipelines, tokens sitting inside runner, and that surface is growing fast. Not every team is keeping up with it.

