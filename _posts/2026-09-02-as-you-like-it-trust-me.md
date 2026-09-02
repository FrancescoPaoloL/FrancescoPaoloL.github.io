---
layout: default
title: "As You Like It, Trust Me"
date: 2026-09-02
categories: ["AI Security", "LLM"]
tags: [ai-security, llm-security, trust-model, generative-ai]
---

A few days ago I reread a piece [Umberto Eco](https://en.wikipedia.org/wiki/Umberto_Eco) wrote in 1975. It's satire about the crisis of cinema caused by the home video recorder, set in an imagined future Eco called 1993.

The piece is called *Do your movie yourself*. It ended up in the expanded edition of *Diario minimo*, and for decades it's been read as a joke about DIY culture.

Eco's premise is simple: once the video recorder goes mainstream, arthouse cinema and underground cinema both fall into crisis together. Anyone can watch "their own film" instead of going to a theater, and to do that, they buy a kit.

The kit contains a "plot pattern": a multiple-subject structure built around the style of a recognizable director ([Antonioni](https://en.wikipedia.org/wiki/Michelangelo_Antonioni), though the mechanism works for anyone with a recurring visual grammar).

The base sentence for Antonioni goes: "Una distesa desolata. Ella si allontana." ("A desolate expanse. She walks away.") Every word carries an alphabetic superscript pointing to a package of interchangeable variants. Combine the variables and you get, by Eco's count, 15,751 distinct Antonioni films.

It's worth being precise about what this mechanism actually is, because it's anything but vague: deterministic slot-filling, a template with a finite, pre-written set of substitutions per variable. The output space is closed and enumerable in advance, one by one, before anyone generates a single film.

Anyone with a passing familiarity with modern literature will recognize the logic. It's the same move as [Raymond Queneau](https://en.wikipedia.org/wiki/Raymond_Queneau)'s *[Cent mille milliards de poèmes](https://en.wikipedia.org/wiki/A_Hundred_Thousand_Billion_Poems)*, or the [Oulipo](https://en.wikipedia.org/wiki/Oulipo)'s combinatorial games, a tradition Eco knew as a semiotician, not as a casual moviegoer. It's no accident the piece comes out of that lineage rather than out of technological speculation: Eco is applying potential literature to cinema, not imagining a statistical model.

Now it would be easy to read this text as if Eco had gotten his hands on an LLM paper because of pattern generator like Eco's produces enumerable output: you could, in principle, list all 15,751 films before generating a single one.

A language model doesn't work that way; it doesn't fill fixed slots.

It samples from a probability distribution learned over billions of examples, every token is conditioned on everything that came before, and the output space is neither explicitly enumerated nor realistically enumerable in advance.

A closed menu on one side, a cook improvising with no fixed recipe book on the other.

But where Eco really nails it's the cultural diagnosis. The core point of the piece is that these kits turn filmmaking into a practice open to anyone, and everyone ends up watching their own film instead of sharing a theater.

That's exactly the conversation we're having today about automated generation of text, images, and music "in the style of": the same promise, that anyone can produce something resembling the work of a master.

Eco built that with a deck of combinatorial cards, applied to Antonioni.

And here's the part that actually matters to anyone working in security. Eco's generator has, by construction, a fully explicit trust model. You know in advance what it can produce, because the output space is finite and sits in front of you before you generate anything. You can audit all of it. You can formally prove what it will never spit out.

An LLM has an implicit trust model by definition. There's no explicit list of what it can produce, so trust isn't established once and for all by inspecting the output space. It has to be built and monitored continuously while the system runs.

It's the same leap anyone faces when they drop an LLM into a pipeline that makes decisions or takes actions: moving from a closed, bounded system, auditable on paper, to a system with an open-ended output distribution, where trust has to be built at runtime because the perimeter of behavior isn't an explicit spec and isn't realistically enumerable in advance.

Eco predicted the desire perfectly: the urge to mass-produce "in the style of" someone, turning an author's craft into a consumption procedure.

What he couldn't predict, in 1975, is that fifty years later we'd build an engine capable of satisfying that desire with no cage at all. The move, in the end, is from trust by construction to trust by observation.

