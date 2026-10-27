---
title: 'Publish or Perish: I Built a Flappy Bird Clone About Rejected Papers'
date: 2026-10-27
permalink: /posts/2026/10/phd-flappy-publish-or-perish/
tags:
  - humor
  - python
  - javascript
  - games
  - procrastination
---

Somewhere between a stalled analysis script and a review that opened with "the novelty is insufficient," I built a video game instead of fixing either. This is [PhD Flappy: Publish or Perish](/phd-flappy/index.html) — a Flappy Bird clone where you play a PhD student trying to climb from Earth's surface all the way to deep space, while a gauntlet of reviewers blocks the way like so many pipes in the original game.

## Play it

<p style="text-align:center; margin-bottom: 0.5em;">
  <a href="/phd-flappy/index.html" target="_blank" rel="noopener">Open it fullscreen in a new tab</a>, or play it right here:
</p>

<iframe src="/phd-flappy/index.html" width="100%"
        style="max-width:480px; aspect-ratio:480/800; display:block; margin:0 auto 1.5em; border:3px solid #222; border-radius:10px;"
        loading="lazy"></iframe>

Tap, click, or hit space to flap. Turkish and English are both available — pick one on the start screen before you begin your academic career.

## The premise

Every obstacle is a reviewer standing between you and the next stage of your career. Clear enough of them and the background itself promotes you: you start on the ground among city rooftops, climb through clouds and atmosphere, cross the edge of space, reach orbit, and eventually drift among nebulae and gas giants, which is roughly how it feels to get a paper through four rounds of revisions. Get too close to a reviewer and the game is blunt about what happens, closing with one of several lines that will sound familiar to anyone who has had a paper rejected: *"Minor revision," rejected eight months later.* *Reviewer 2 never replied, paper stuck in limbo.* *Related work is missing — despite 40 references.*

Every so often the game also stops to ask you a short astronomy question, multiple choice, click to answer. Getting it right is worth a few bonus citations. It felt wrong to make an astronomy joke game and not make sure at least one person remembers what a light-year actually measures.

## How it's built

The first version was a Python script using [pygame](https://www.pygame.org/) — gravity, flapping, pipes, the usual. That version still exists and runs fine from a terminal if you ever want a break from a terminal. But pygame doesn't run in a browser without a fairly heavy WebAssembly export step, and a game that lives in a `.py` file is not a game you can drop into a blog post. So the real version here is a full rewrite in plain HTML5 canvas and JavaScript: one self-contained file, no build step, no dependencies, and no server beyond whatever serves a static file — which describes this site exactly. It also runs on a phone, which the Python version categorically does not.

## Why

Mostly because an afternoon spent drawing a tiny pixel-art PhD student dodging pixel-art reviewers was more fun than the afternoon I was supposed to be having. If you recognize yourself in any of the rejection messages, that's the point — and if you beat a high score worth bragging about, the astronomy questions are there to confirm you deserve it.
