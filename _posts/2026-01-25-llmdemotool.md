---
layout: default
title: "Building LLM Security Tools: A Learning Journey"
date: 2026-01-25
---

## Why I Started This

LLMs are everywhere now, sometimes even where they shouldn't be. Everyone seems focused on building chatbots, assistants, and AI features. But as I dug deeper, I noticed something: the security side feels immature.

The OWASP LLM Top 10 was published to highlight common vulnerabilities (prompt injection, data leakage, excessive permissions) but practical testing tools? Not many. I wanted to understand this space better, so I decided to build something. Not because I'm an expert (I'm definitely not), but because building things is how I learn best.

## What I Built

I created a basic tool for testing LLM applications against OWASP vulnerabilities. Right now it detects three attack types:

- **Prompt injection** (LLM01): When someone tries to override system instructions
- **Sensitive data exposure** (LLM02): Detecting attempts to leak private information
- **Excessive agency** (LLM06): Checking if the model is being manipulated to do things it shouldn't

Obviously this is work in progress, subject to revisions and improvements as I learn more.

The tool uses C++ for detection logic (built on top of llama.cpp), Python for the API layer, and runs in Docker. I deployed it on Azure and set up GitHub Actions for CI/CD; if I'm learning modern AI security, I might as well do it properly.

You can check it out here: [github.com/FrancescoPaoloL/llmSecurityDemo](https://github.com/FrancescoPaoloL/llmSecurityDemo)

## The Hard Parts (For Me)

The biggest challenge wasn't the security logic: pattern matching isn't rocket science. It was getting the infrastructure right: building the C++ components and making everything perform well, both locally and in Docker.

When I first containerized everything, inference was taking 12-15 seconds per request. Completely unusable. After digging into the problem, I found three issues:

1. **The binaries weren't compiled with [AVX2 optimizations](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#AVX2).** AVX2 is a CPU instruction set that speeds up mathematical operations that is critical for matrix calculations in neural networks. Without it, everything runs much slower.

2. **Docker wasn't exposing enough CPU cores.** This was my inexperience with container configuration showing. The application could only see 2 cores instead of the 12 available.

3. **[OpenMP](https://en.wikipedia.org/wiki/OpenMP) threading wasn't configured properly.** OpenMP is a library for parallel processing and it lets programs split work across multiple CPU cores. I hadn't set the thread count, so it was defaulting to single-threaded execution.

Once I rebuilt everything with proper CPU flags and fixed the threading configuration, I got response time down to about 0.3 seconds. Still not GPU-fast, but workable for a testing tool.

Another lesson learned: caching is your friend. GitHub Actions was downloading a 409MB model file on every build until I added proper caching. Now builds take 5 minutes instead of 15.

## What's Next

This tool is far from complete. I've implemented 3 out of 10 OWASP categories. The remaining seven are on my list, but I'm taking it step by step.

I'm also planning to port this to a Raspberry Pi 3 (sic!) and running LLM security checks on edge devices with limited resources sounds like an interesting challenge.


