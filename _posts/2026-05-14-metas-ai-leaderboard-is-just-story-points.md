---
layout: post
title: "Meta's AI Leaderboard Is Just Story Points With a Bigger Budget"
image: assets/images/post-token-legend.svg
author: "Amod"
categories: [ tech, engineering ]
tags: [ ai, agile, productivity, engineering-culture ]
comments: true
---

A Meta engineer built an internal leaderboard called "Claudeonomics." It ranked 85,000+ employees by how many AI tokens they consumed. Top performers earned titles: "Session Immortal" at the lower tier, "Token Legend" at the top.

In 30 days, Meta employees burned through 60 trillion tokens. Estimated cost at standard API rates: $900 million.

Some of those tokens went toward real work. A lot of them went toward running agents that produced nothing, generating throwaway code, and building projects nobody would ever ship. The point was not the output. The point was the leaderboard position.

Meta shut it down after internal data was shared externally.

---

I've been in enough sprint planning meetings to recognize this pattern.

The ritual goes like this: someone puts a ticket on the screen. Everyone stares at it. Someone says "three." Someone else says "five." Then fifteen minutes disappear arguing about the difference, and the ticket gets pointed at a four, a number that satisfies nobody and predicts nothing.

By quarter's end, the team's velocity looks great. Did we ship what mattered? That's a different question.

Story points were invented to measure complexity, not time. Somewhere along the way they became a delivery forecast, a team performance metric, a number managers put in slides. Velocity stopped measuring how well the team worked and started measuring how well the team pointed.

The two things are not the same.

---

There's a law for this. Goodhart's Law: when a measure becomes a target, it ceases to be a good measure.

It explains the Claudeonomics leaderboard. It explains sprint velocity. It explains lines of code as a productivity metric. The specific measure changes with the era. The failure mode is identical.

Organizations need to show progress upward. Managers need evidence for the quarterly review. AI adoption is the current mandate, and token counts are the only number that's easy to collect. So token counts become the target. Engineers aren't gaming the system out of laziness. They're doing exactly what the incentive structure asks them to do.

---

Recently I got pulled into architecture discussions on a codebase I had no prior context on. I used AI to understand the system, verify my reading of the code, and put together a proposal. No code came out of it. My idea may not even be the one we go with. But I could participate meaningfully in a decision that would have taken me weeks to get up to speed on otherwise. That has value. It just doesn't fit in a token count column.

The other side of this is also true. I am not a UI developer. I recently needed to make UI changes and they came out exactly as expected, concrete outcome and all. But I was deliberate about it. I planned the work carefully specifically to keep token consumption low. The goal was the UI change, not the tokens. The tokens were just a cost to manage.

Tokens are a cost, not a score.

---

Here's the thing: measuring AI adoption with token counts is not wrong. It's a reasonable starting signal. If usage is near zero, that tells you something.

The problem is when the measurement becomes the competition. HubSpot's CEO put it simply: outcome maxxing beats token maxxing. The leaderboard was not measuring AI adoption. It was measuring the human capacity to optimize for whatever number is in front of us.

We have always done this. We will do it again with whatever metric comes next.

Name it when you see it.

---

*Sources: [The Pragmatic Engineer](https://blog.pragmaticengineer.com/the-pulse-tokenmaxxing-as-a-weird-new-trend/), [Trending Topics EU](https://www.trendingtopics.eu/tokenmaxxing-is-ai-token-consumption-a-productivity-metric-or-vanity-trap/)*
