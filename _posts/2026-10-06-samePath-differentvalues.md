---
layout: default
title: "Same Path, Different Value"
date: 2026-10-06
categories: ["AI Security", "LLM"]
tags: [ai-security, llm-security, kernel, fuzzing, kcov, trust-model, tool-calls]
---

I saw [Yunseong Kim's presentation](https://www.linkedin.com/feed/update/urn:li:activity:7513274417406423040/) at [Linux Plumbers Conference 2026](https://lpc.events/event/20/contributions/2402/): a proposal to let kernel fuzzers see not only where the code goes, but also which values it carries.

The Linux kernel has tools to observe what happens at its boundaries. A concrete example is KCOV, the coverage tracer used by fuzzers like syzkaller, which records which paths the code takes.

In plain words, two requests can follow exactly the same path and still be very different, because the path tells us "which functions and branches I went through", but not "with which values".

A very simple example:

```c
void write_data(int fd, char *buf, int len) {
    if (len > 0) {
        write(fd, buf, len);
    }
}
```

A request with `len = 16` and one with `len = 4096` can both:

* enter `write_data()`
* pass the `len > 0` check
* reach `write()`

For KCOV, the execution path is identical. The behavior can still be very different: one value can be perfectly valid, while another can trigger a bug further down.

That is why Yunseong Kim proposed `-fsanitize-coverage=trace-args,trace-ret` ([kernel RFC](https://groups.google.com/g/kasan-dev/c/Dzh5R8jpsEQ), [LLVM RFC](https://discourse.llvm.org/t/rfc-sanitizercoverage-add-fsanitize-coverage-trace-args-trace-ret/91026)), which also records the values going into and out of functions.

Same path, different value. And that is exactly where bugs can hide.

With AI agents, the problem is similar.

Knowing which tool was called only tells part of the story. The same tool, with different arguments, can turn a €16 payment into a €4,096 one (Okay, I have a bit of a thing for powers of two).

And with AI as a black box, the boundary is the point where you can observe what is happening and enforce a decision.

That is where you also need to be able to say no.

That is what I am trying to do with [llm-tollgate](https://github.com/FrancescoPaoloL/llm-tollgate-poc), a POC for applying policy, trust scoring, and taint propagation to tool calls.

The kernel is getting there one bug at a time.

With AI, it is better to get there first.

---

*Notes.* The kernel work discussed here is Yunseong Kim's: see his [paper](https://arxiv.org/abs/2606.00455) and the RFCs linked above. The analogy to AI agents is mine, and llm-tollgate is a POC, not a finished product. These are my personal views, not my employer's.

