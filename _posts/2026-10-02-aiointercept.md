---
layout: post
title: How I ended up maintaining an aiohttp mocking library
description: The story of aiointercept, from pain points with aioresponses to a library recommended in the aiohttp tracker.
permalink: /blog/aiointercept
---

This year I started to write and maintain [aiointercept](https://pypi.org/project/aiointercept/), a library to mock aiohttp. This is the story of how that happened, and everything related to it.

[aioresponses](https://github.com/pnuckowski/aioresponses) is the most popular library in the ecosystem for this task. I started to use it professionally and I had a few pain points, and development had stalled, so they were not getting fixed. First I tried to help on the library itself. Then I tried to move it to other maintainers, so it would not be lost. In the end I received advice on how to build a library with a better approach, and I built it.

## aioresponses and why it breaks

The first commit of aioresponses is from 17 October 2016. The way it works is simple: it creates aiohttp's `ClientResponse` objects by hand and gives them to your code, without any real request.

The problem is that `ClientResponse` is internal to aiohttp. The aiohttp documentation says users never create it. So every time aiohttp changes that class, aioresponses breaks.

This happened early. On 20 April 2017 an aiohttp release broke aioresponses for the first time ([PR #59](https://github.com/pnuckowski/aioresponses/pull/59/changes)). In June 2018 the response changed again ([PR #98](https://github.com/pnuckowski/aioresponses/pull/98)). I don't think this is anybody's fault. It is the approach: if you depend on the internals of another library, you will break when they change.

## Using aioresponses and trying to help

I started to use aioresponses professionally in April 2025. The API is nice and I liked it. But I had some pain points. One was that failed assertions were hard to read, so on 4 September 2025 I asked for better formatting in [issue #276](https://github.com/pnuckowski/aioresponses/issues/276).

Nothing moved for months. In March 2026 I asked directly whether the project was maintained ([issue #281](https://github.com/pnuckowski/aioresponses/issues/281)). On 3 March I also suggested moving the repository to aio-libs ([discussion #45](https://github.com/orgs/aio-libs/discussions/45)). I think this is the correct thing to do before forking or writing something new: ask first.

The answer from aio-libs was that they could host it, if there was someone interested in maintaining it. But [Dreamsorcerer](https://github.com/Dreamsorcerer) and [webknjaz](https://github.com/webknjaz) also told me that the aiohttp maintainers advise against this kind of mocking. They recommend sending real requests to a test server. At first I thought that was not practical. Later I understood they were right.

## Writing aiointercept

On 13 March 2026 I did a proof of concept: an API like aioresponses, but using a real server. The client makes a real request, with real headers and real serialization, and a local `aiohttp.web` server answers it. Nothing depends on aiohttp internals, so there is nothing for aiohttp to break.

It worked faster than I expected, and that started to convince me.

Meanwhile, aioresponses became active again, and I want to be fair about that. [pnuckowski](https://github.com/pnuckowski) merged a PR of mine on 12 April ([PR #280](https://github.com/pnuckowski/aioresponses/pull/280)) and said the next day that the project was maintained. I continued helping there. In April I also suggested changes to how redirects work ([issue #283](https://github.com/pnuckowski/aioresponses/issues/283)).

Version 0.1.0 of aiointercept went up on PyPI on 16 April. On 4 May I released 0.1.1 and showed it to webknjaz. webknjaz replied with a list of things I was doing wrong in my packaging and CI. That helped me a lot.

On 23 May, with version 0.1.4, I told aio-libs that the project was working. I hoped it would be useful to other people, but I did not expect any success.

## The aiohttp 3.14 break (June 2026)

At the start of June 2026 aiohttp released 3.14, and it was incompatible with aioresponses. `ClientResponse` got a new required argument, `stream_writer`, and aioresponses did not pass it ([issue #289](https://github.com/pnuckowski/aioresponses/issues/289)). The same problem as 2017, 2018 and other times.

There was a quick workaround, and some projects used it and still use it. But it means patching aioresponses, and if you have to patch your mocking library, that is not a good sign. Also, 3.14 was a security update ([GHSA-jg22-mg44-37j8](https://github.com/advisories/GHSA-jg22-mg44-37j8)), so staying on the old aiohttp was not a good option. A test library should never be the reason you cannot install a security fix.

On 5 June somebody opened an issue on aiohttp about the incompatibility ([issue #12815](https://github.com/aio-libs/aiohttp/issues/12815)). Some people said a minor version should not break things. The maintainers answered that nobody is supposed to create that class, and I agree with them. If every internal thing that somebody uses becomes a promise, a library can never change.

In that issue [bdraco](https://github.com/bdraco) and Dreamsorcerer suggested aiointercept as the answer. I was surprised. My library was two months old and it was being recommended in the aiohttp tracker.

After that things moved fast:

- 9 June: the first pull request from another person, [agroebe](https://github.com/agroebe), was merged and released as 0.1.6.
- 11 June: a couple of projects migrated and surfaced issues from use cases I had not covered.
- 12 June: A new release, with 6 changes in just 3 days, and a second contributor.


## What happened after

The first migrations taught me a lot. Every project used mocks in a different way, and each one found something I had not thought about. Some issues were bugs in aiointercept. Others were bugs in their tests that the fake responses had been hiding. I think finding those is a good thing, and it is what really convinced me: real projects had bugs that were covered by the mocking approach of aioresponses.

One example is redirects: aioresponses does not use the same redirect machinery as aiohttp ([issue #283](https://github.com/pnuckowski/aioresponses/issues/283)). Another is traces, an old aioresponses issue ([issue #246](https://github.com/pnuckowski/aioresponses/issues/246)) that one project fixed on its side when it moved to aiointercept ([ai-dial-sdk PR #411](https://github.com/epam/ai-dial-sdk/pull/411)).

In one project, the code expected a specific exception when the server answered with a 500. In reality it would receive a `ClientResponse` with status 500 and no exception. The test was passing only because they were giving that same exception to aioresponses to raise. The mock was confirming something that never happens.

With a real server these things show up, because it is aiohttp itself doing the work.

The latest release of aioresponses is from 23 June 2026. The same day a fork appeared, [aioresponses-ng](https://pypi.org/project/aioresponses-ng/). It has the same approach as aioresponses, so I think it can break in the same way. But I like that there is a maintained fork for the people who want to keep their tests as they are, and I hope it stays maintained.

In July 2026 I stopped using aiohttp professionally. It is a bit funny, because that was the month the downloads started to grow. That did not change anything for the project: I still maintain aiointercept, and I am still taking part in the aio-libs community.

## PyPI statistics

aiointercept has about 405,000 downloads since the first upload on 16 April 2026. It passed 100,000 a month in July. The growth started the week after aiohttp 3.14, and the busiest day was 29 July with about 17,700 downloads.

<figure class="bar-chart" aria-label="aiointercept monthly downloads, April to September 2026">
  <figcaption>aiointercept downloads per month, mirrors excluded</figcaption>
  <div class="bar-chart-plot">
    <div class="bar-col" tabindex="0" data-tip="Apr 2026 · 120 (from 16 Apr)"><div class="bar" style="height:0.08%"></div><span class="bar-x">Apr</span></div>
    <div class="bar-col" tabindex="0" data-tip="May 2026 · 635"><div class="bar" style="height:0.44%"></div><span class="bar-x">May</span></div>
    <div class="bar-col" tabindex="0" data-tip="Jun 2026 · 15,474"><div class="bar" style="height:10.7%"></div><span class="bar-x">Jun</span></div>
    <div class="bar-col" tabindex="0" data-tip="Jul 2026 · 109,343"><div class="bar" style="height:75.4%"></div><span class="bar-x">Jul</span></div>
    <div class="bar-col" tabindex="0" data-tip="Aug 2026 · 144,991"><div class="bar" style="height:100%"><span class="bar-label">145k</span></div><span class="bar-x">Aug</span></div>
    <div class="bar-col" tabindex="0" data-tip="Sep 2026 · 125,932"><div class="bar" style="height:86.9%"></div><span class="bar-x">Sep</span></div>
  </div>
</figure>

<details markdown="1">
<summary>Show the numbers</summary>

| Month | Downloads |
| --- | ---: |
| Apr 2026 (from 16 Apr) | 120 |
| May 2026 | 635 |
| Jun 2026 | 15,474 |
| Jul 2026 | 109,343 |
| Aug 2026 | 144,991 |
| Sep 2026 | 125,932 |

</details>

To be honest about the size: aioresponses is still about 44 times bigger, with around 5.6 million downloads in September. It went down from about 14.5 million in May, but I can't say that is because of aiointercept. My library went from 0.1% of its downloads in June to 2.3% in September, which is only a small part of that drop.

Also, downloads are mostly CI machines, not people. What the numbers tell me is that a few projects with a lot of CI moved to aiointercept and stayed. I am happy with that.

<p class="small">Sources: pypistats.org for <a href="https://pypistats.org/packages/aiointercept">aiointercept</a>, <a href="https://pypistats.org/packages/aioresponses">aioresponses</a> and <a href="https://pypistats.org/packages/aioresponses-ng">aioresponses-ng</a>; release dates from <a href="https://pypi.org/project/aiointercept/#history">PyPI</a>.</p>

## What I learned

This whole history taught me a lot: about aiohttp, about packaging and releasing, about how other people test their code, and about what it means to maintain something that others depend on.

I expect to contribute more to the Python ecosystem in general, and to aio-libs in particular.

<details markdown="1">
<summary>Full timeline</summary>

| Date | Event | Link |
| --- | --- | --- |
| 28 Sep 2026 | aiointercept 0.1.12, latest release | [PyPI](https://pypi.org/project/aiointercept/#history) |
| Jul 2026 | I stop using aiohttp professionally |  |
| 5 Jul 2026 | Last release of aioresponses-ng |  |
| 23 Jun 2026 | Latest release of aioresponses; first release of aioresponses-ng |  |
| Jun 2026 | I open another aioresponses fix | [PR #294](https://github.com/pnuckowski/aioresponses/pull/294) |
| 11 Jun 2026 | A couple of projects migrate and find issues in new use cases |  |
| 9 Jun 2026 | First PR from another person (agroebe) merged and released as 0.1.6 |  |
| 5 Jun 2026 | Incompatibility raised on aiohttp; bdraco and Dreamsorcerer recommend aiointercept | [aiohttp #12815](https://github.com/aio-libs/aiohttp/issues/12815) |
| Early Jun 2026 | aiohttp 3.14, a security update, breaks aioresponses | [issue #289](https://github.com/pnuckowski/aioresponses/issues/289), [advisory](https://github.com/advisories/GHSA-jg22-mg44-37j8) |
| 23 May 2026 | I tell aio-libs the project is working (0.1.4) |  |
| 4 May 2026 | aiointercept 0.1.1; shown to webknjaz |  |
| 16 Apr 2026 | aiointercept 0.1.0 uploaded to PyPI | [PyPI](https://pypi.org/project/aiointercept/#history) |
| Apr 2026 | I suggest redirect changes in aioresponses | [issue #283](https://github.com/pnuckowski/aioresponses/issues/283) |
| 13 Apr 2026 | pnuckowski says aioresponses is maintained |  |
| 12 Apr 2026 | pnuckowski merges a PR I authored | [PR #280](https://github.com/pnuckowski/aioresponses/pull/280) |
| 13 Mar 2026 | Proof of concept: an aioresponses-like API on a real server |  |
| 3 Mar 2026 | I propose moving aioresponses to aio-libs | [discussion #45](https://github.com/orgs/aio-libs/discussions/45) |
| Mar 2026 | I ask whether aioresponses is maintained | [issue #281](https://github.com/pnuckowski/aioresponses/issues/281) |
| 4 Sep 2025 | I ask for better-formatted assertions | [issue #276](https://github.com/pnuckowski/aioresponses/issues/276) |
| Apr 2025 | I start using aioresponses professionally |  |
| Jun 2018 | aiohttp's response changes again | [PR #98](https://github.com/pnuckowski/aioresponses/pull/98) |
| 20 Apr 2017 | First aiohttp release that breaks aioresponses | [PR #59](https://github.com/pnuckowski/aioresponses/pull/59/changes) |
| 17 Oct 2016 | First aioresponses commit |  |

</details>
