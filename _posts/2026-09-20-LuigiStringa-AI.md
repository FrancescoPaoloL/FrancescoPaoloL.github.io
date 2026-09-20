---
layout: default
title: "What a 1990 AI Book Still Says About Trusting LLM Systems"
date: 2026-09-20
categories: ["AI Security", "LLM"]
tags: [ai-security, llm-security, trust-model, generative-ai]
---

The text is chapter 15 of *MEMI* by Roberto Vacca [1], dedicated to Luigi Stringa [2]. It holds the first four chapters of a book on AI that was never finished (many parts carry notes like "this section will be revised"). The most recent sources it cites date from 1990.

## Memory: skills you no longer work out from scratch

In the book, memory mostly means stored skills. When you learn to drive, you think about every move. After a while you don't: the solution is already saved. The authors say an intelligent machine has to work the same way, so it needs a good balance between computing and memory, not only a fast processor.

The closest modern counterpart is what a trained model has absorbed into its weights. RAG is a different kind of memory: facts kept outside the model and looked up when needed [3]. It addresses a related problem, but it is not what the book describes.

> "a lot of solutions to our behavioral problems are stored, not re-computed anew each time." (p. 187)

## Propose, check, decide

In the book, intuition is a leap to a conclusion, which a machine can also make at random, with all the uncertainty that comes with it. The conclusion is then checked against reality before it is accepted. That gives three steps: something proposes, something checks, and only then does the system act, abstain, or ask.

Guardrails are one way to do the middle step. As I read their descriptions, Llama Guard classifies prompts and responses against a safety risk taxonomy [4], and NeMo Guardrails lets developers add programmable rules that are independent of the underlying model [5]. Neither is meant to tell you that a fact is right or that a plan is sound. For that you need other checks: tests, schema validation, a second source, a human approval. In my llm-tollgate-poc [link] the check sits right before the tool call.

> "Intuition may be defined as an effective way to jump to conclusions. The conclusions, though, have to be tested logically against reality" (p. 190)

## Checks that fail together

Two imperfect recognizers, put together, work better than one. The book does the math: each answers in 70% of cases and gets 5 in 100 wrong. But the calculation only holds if the two systems fail independently. If they fail together, the gain disappears.

That is easy to get wrong today. You can stack three layers of verification that are really one blind spot counted three times: same model family, same training data, same prompt. A checker that is itself an LLM is not fully independent of the model it checks.

A quick test, if you have labeled cases: compare how often both checks are wrong at the same time with the product of how often each one is wrong alone. If the two numbers are close, the layers are independent. If the first is much higher, they are not.

The book also shows abstention as a setting you choose. Accept only when both recognizers agree, and the system answers in about 42% of cases with almost no errors. Accept also when only one answers, and it answers in 83.5% of cases with 3% error (p. 203).

> "by using increasing numbers of totally independent types of analysis, each with a fairly high probability of rejection, we cut down the overall probability of rejection and of error [...]" (p. 203)

## An explanation you can't understand is no use

If a machine finds an odd solution, it has to explain why it should be accepted, and in a way people can understand. The book gives the example of a chess program: if you asked it "why this move?", all it could say was that its table said so.

> "It will be necessary, then, that the machine also provide a documentation, validation or substantiation of why the solution proposed should be accepted." (p. 192)
>
> "This explanation must also be understandable." (p. 193)
>
> The chess program's answer: "that's what my lookup table says" (p. 192)

## Saying how far you can trust the data

If the machine takes a piece of information from a source, it has to say how reliable it is, or at least add a disclaimer. It also has to adapt the answer to who is asking. This is the source problem in RAG: the paper that introduced it lists provenance, meaning where an answer comes from, among the open problems of pre-trained models, and proposes external retrieval as a way to address it [3]. For an agent that acts, it matters twice: someone has to see why it did something, and where the information behind the action came from.

> "The machine would have to find a way to assess and express the reliability, the accuracy or the expected magnitude of errors in the original description." (p. 193)
>
> "our machine, then, would have to add a caveat footnote." (p. 193)

## Better a coherent system than skilled but disconnected parts

The book imagines teams that each design one part of a car without talking to each other: wheels and engine don't fit. The AI of the time was like that, with chess, translation and vision each going its own way. Better a system that is mediocre but coherent, and asks for clarification when the orders don't match what it sees.

The book mentions this in half a sentence. I think it deserves more, because asking before acting is a property of the whole system, not a filter you bolt on. For an agent today, it means stopping to ask before an action that can't be undone.

> "an integrated system, whose parts may not be excellent, but which behaves in an eminently reasonable way, for example, asking for clarifications or pointing out inconsistencies" (p. 201)

## One more thing worth keeping: evaluation

Turing's test [6] says a machine is intelligent if a judge, chatting with it, cannot tell whether it is a machine. The authors point out that the verdict depends too much on the judge, and say to use such tests with caution and change them when needed.

I think the same happens with benchmarks. When a number becomes the target, it stops measuring well. That is Goodhart's law [7].

> "we cannot hope to define a priori useful criteria to evaluate our progress in this very complex and difficult discipline. [...] Let's use them tentatively - but let us also be ready to discard the old ones" (p. 185)

The text has aged badly in other places. For instance, it assumed language would stay hard for machines, probably because neural networks take up only a few pages.

## Where this leaves us

Producing an answer is cheap now. The engineering goes into the system around it: deciding whether to trust the answer, checking it, and knowing when to leave it unused.

## Sources

1. Vacca, R., *MEMI*, ch. 15, dedicated to Luigi Stringa (unfinished text). Pages 176-209 of the copy consulted. [self-published, 2010]
2. Luigi Stringa, Italian physicist and director of the ITC-irst in Trento from 1985 (no English page exists). [Wikipedia, in Italian](https://it.wikipedia.org/wiki/Luigi_Stringa)
3. Lewis, P. et al. (2020). *Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks*. NeurIPS 33, pp. 9459-9474. [arXiv:2005.11401](https://arxiv.org/abs/2005.11401)
4. Inan, H. et al. (2023). *Llama Guard: LLM-based Input-Output Safeguard for Human-AI Conversations*. [arXiv:2312.06674](https://arxiv.org/abs/2312.06674)
5. Rebedea, T. et al. (2023). *NeMo Guardrails: A Toolkit for Controllable and Safe LLM Applications with Programmable Rails*. EMNLP 2023, System Demonstrations, pp. 431-445. [ACL Anthology](https://aclanthology.org/2023.emnlp-demo.40/)
6. Turing, A. M. (1950). *Computing Machinery and Intelligence*. Mind, 59(236), 433-460, October 1950. DOI: [10.1093/mind/LIX.236.433](https://doi.org/10.1093/mind/LIX.236.433)
7. Goodhart, C. (1975): original idea that statistical regularities collapse when you use them for control purposes. The popular wording "when a measure becomes a target, it ceases to be a good measure" was popularized by Marilyn Strathern (1997). See [Goodhart's law](https://en.wikipedia.org/wiki/Goodhart%27s_law).

