---
layout: post
title: Artifacts are not governance
description: Some learnings on the organization of software development, from remote, open-source-style teams to on-site corporate ones.
permalink: /blog/artifacts-are-not-governance
---

Recently, I started a new job. That meant moving from a fully remote position back to an on-site, hybrid setup in Dublin. After a couple of years with a lot of change and self-created craziness, this felt like a good moment to pretend to be a sociologist, and think about how the way we work changes engineering dynamics.

I've been lucky in my career as a software developer to work at good companies, places I would recommend to a friend. But each of them had a different relationship with remote work, asynchronous communication and engineering governance. So I want to make some personal observations. They are a compass for myself as I move through my career, and maybe a useful read for other developers and for the people who make engineering decisions.

## 1. Two organizations at the same time

In every software company, as in any human institution, two structures exist side by side:

- **The formal organization:** hierarchies, reporting lines, roadmaps, KPIs, performance reviews and business priorities.
- **The informal organization:** how developers actually talk to each other, solve problems, share knowledge and get work done day to day.

Software has two things that make this special. First, the open source movement gave us a strong blueprint for the informal organization. Developers naturally move towards open source principles: autonomy, asynchronous collaboration, peer review, and a deep love for the craft. Second, our tools record almost everything: Git commits, pull requests, public Slack threads, code review comments.

### Artifacts are not governance

Many times I have believed, and I think it's the case for other developers too, that because something is recorded, it is part of the formal structure of the company.

It is not. A commit log or a long Slack discussion is just a trail. Your code review being public to the team, or your refactoring PR being merged, doesn't mean management knows about it, is governing it, or has aligned it with the business strategy. The informal organization in software is very well documented, which makes it easy to confuse with the formal one. But it is still informal.

This is true even when managers are part of the development cycle, approving PRs or asking for changes. Much of that still sits outside the formal organization.

## 2. Not just remote vs. on-site

It's easy to turn this into a "remote vs. on-site" debate, but it's more of a spectrum.

What matters more than location is how close an organization is to open source dynamics or to traditional corporate structures. Small companies, asynchronous remote setups and developer-centric teams drift towards open-source-style self-organization. On-site or very centralized companies lean towards formal corporate structure.

Both ends fail in their own way:

| | Open-source-leaning / remote | Corporate / on-site |
| --- | --- | --- |
| Main driver | Autonomy, craft, spontaneous collaboration | Business priorities, deadlines, roadmaps |
| Visibility | Lots of written trail (code, text), little social transparency | High social transparency, clear formal reporting |
| Main failure mode | Unclear structure, silos, orphaned code | Initiative gets stifled, heavy process, passion for the craft fades |
| Code review | Peer-driven, voluntary | Process-driven, SLAs, mandatory sign-offs |

In a very remote or open-source-leaning environment, good practices like written documentation and public codebases come almost for free. But without formal structure, alignment falls apart. You can go months without knowing who your actual manager is, which work is formally assigned to you, and which work you just picked up yourself.

## 3. Cliques: from trust to friction

In open source projects, informal cliques, small inner circles, form naturally around time zones, languages or core maintainer groups. In public open source this is almost healthy. Trust is hard to build asynchronously, so maintainers review first the people they know and trust.

When this dynamic moves into a commercial company, it becomes a problem.

In a company, a clique can show up the same way it does in open source, with asymmetric code reviews. But it also shows up in business terms: access to certain spaces, unequal access to information, or informal power to make decisions.

In open source, ignoring a PR from a stranger because you don't have time or context is normal. In commercial software, where there is money, dependencies between teams and delivery deadlines, letting informal cliques gatekeep code reviews creates friction and deep silos.

## 4. The "useful developer" trap

When an organization doesn't give clear formal direction, proactive developers fall into a quiet trap: what Sean Goedecke calls being ["addicted to being useful"](https://www.seangoedecke.com/addicted-to-being-useful/).

Without clear reporting lines or strategic guidance, engineers do what feels natural in an open-source-like environment. They look for unassigned problems, fix small bugs, refactor random modules, and keep busy. It feels productive because you are producing code. But because the work isn't connected to the priorities of the formal organization, you end up with no leverage, no sponsor for your career progression, and no real impact on the product.

AI coding tools make this worse. They make it very cheap to produce more PRs, more code and more documentation, so the trail grows faster than ever, while it gets harder to tell whether anyone asked for that work or whether it moves the business forward.

## 5. Finding the balance

The main lesson I take from working in different setups is that neither extreme works on its own.

**If you are in a remote, open-source-leaning team:** you don't need more hackathons or 80/20 creative time. Your daily work already feels like an ad-hoc hackathon. What you need is formal clarity: explicit reporting lines, transparent evaluation, clear review, and an active effort to break informal cliques.

**If you are in an on-site, corporate-heavy team:** the risk is the opposite, that rigid processes kill technical passion. Here, internal hackathons, communities of practice and InnerSource models are useful release valves for engineering creativity and love for the craft.

**As an individual developer**, especially in the first kind of team, the fix for the "useful developer" trap is to move your work into the formal organization yourself:

- Before starting something, ask "who asked for this?". If the answer is nobody, decide if it's worth doing anyway, knowing it probably won't count.
- Connect your work to a stated priority: a roadmap item, a team goal, a known problem someone with authority cares about.
- Tell your manager explicitly what you are doing and why. A merged PR is not telling them.

Knowing which way your workplace tends to fail is the first step to navigating your career inside it, and to building an engineering culture that respects both the formal reality of the business and the informal craft of writing software.
