# ML Research Scout

A research agent that finds genuinely new, substantive developments in machine learning, deep learning, and computer vision, so you don't have to manually sift through recycled news and vendor marketing every week.

## What it does, and for whom

If you work in ML/CV (like I do, building [TrafficSmart](https://al-najaf.github.io/), a vehicle congestion detection system) and want a weekly pulse on what's actually new in the field, this agent does the searching and judgment for you: it runs live web searches on a topic you give it, evaluates whether what it found is actually substantive or just recycled/vendor content, runs follow-up searches on its own if the first pass is weak, and reports back honestly, including saying "nothing new" when that's the truth, instead of padding the answer.

This is for one user: me, running it about once a week. It is not a public-facing tool.

## Setup a stranger could follow

1. Create a free Claude account at [claude.ai](https://claude.ai).
2. Create a new **Project**, name it "ML Research Scout."
3. Open the Project's **Instructions** and paste in the system prompt below (see "Instructions Used").
4. Connect the **Exa** MCP connector to your Claude account (Settings → Connectors → search "Exa" → Connect). Exa provides live web search and page-fetching; it's free to connect.
5. Inside the Project, make sure Exa is toggled on for your chat (usually a tools/plus icon near the message box).
6. Start a new chat inside the Project and type a topic, e.g. `"object detection"` or `"traffic AI"`.

No code, no API keys to manage yourself, everything runs inside the Claude Project.

## Usage examples

**Input:**
```
object detection
```

**What happens:** the agent searches live for recent object detection developments, evaluates the results, and if they're thin or off-topic, runs up to 2 more searches with a refined angle before reporting back.

**Real example output (abridged):**
> Ran 3 searches (hit the cap), found nothing substantive from the last 1-2 weeks. Correctly separated a shipped release (YOLO26, ~7 months old) from an unconfirmed future one (YOLO27, not yet released, expected Sept 2026).

**Input:**
```
traffic AI
```

**What happens:** the agent recognized this topic was ambiguous (cybersecurity "bot traffic" vs. road/transportation traffic) on its own, and investigated both angles before reporting back.

## Architecture sketch

```
[You type a topic]
        |
        v
[Claude Project: ML Research Scout]
        |
        v
[Exa MCP connector: live web search]
        |
        v
[Agent evaluates results:
  substantive? recent? vendor claim or independent fact?]
        |
   ---- no, thin/off-topic ---->  [Run follow-up search, refined angle]
        |                                     |
        v                                     |
   yes, substantive  <--------------------------
        |
        v
[Final honest brief: findings + sources,
 or an honest "nothing found" if that's the truth]
```

Unlike a fixed workflow (my earlier FL-04 project ran exactly one search per topic, no matter the result), this agent decides for itself how many searches to run (capped at 3) and when it has enough to report, that's the actual "agent" behavior being demonstrated here, not just a scripted sequence.

## Instructions Used

```
You are a research scout for machine learning, deep learning, and computer
vision developments. Given a topic, search for genuinely new, substantive
developments from the last 1-2 weeks. Evaluate your own results: if they're
thin, off-topic, or just marketing content, run a follow-up search with a
refined angle before settling. Prioritize official sources (papers, company
engineering blogs, benchmarks) over aggregators. Distinguish vendor claims
from independently verified facts. Stop and produce the brief once you have
at least 2 genuinely substantive items, or after 3 search attempts,
whichever comes first. Never fabricate a source. If nothing substantive
exists, say so honestly. Never post, email, or publish anything, only
report back.
```

## Eval Results (v2)

Five eval cases were written before building (see FL-06 spec) and run for real against the live agent:

| # | Input | Expected | Actual result |
|---|---|---|---|
| 1 | "object detection" | 2+ recent items | Agent self-narrowed to YOLO, ran 3 searches, found nothing within the strict window, correctly distinguished shipped vs. unreleased models. **Honest-failure pass** |
| 2 | Broadened to RF-DETR/DETR variants | 2+ recent items | Correctly separated a vendor-reported benchmark from an independently verified ICLR 2026 acceptance. Again found nothing within the strict window. **Honest-failure pass** |
| 3 | "traffic AI" | At least 1 relevant result | Agent recognized topic ambiguity itself (cybersecurity vs. transportation) and searched both. Found nothing within the strict window; named the real vendors behind recycled coverage. **Honest-failure pass** |
| 4 | Niche/thin topic (via broad "computer vision" test) | Honest "nothing found" if applicable | Confirmed: agent does not pad results when nothing substantive exists |
| 5 | "3D object detection" (raw screen capture) | Real end-to-end run | Full run recorded live, ~32 seconds, topic entered, real tool-use step visible, concluded with an honest "nothing new at all" verdict |

**Headline finding:** across all 4 real runs, the agent never fabricated a source, never mistook old news for new, and consistently self-corrected its search strategy. It also surfaced a genuine flaw in the original spec, the "last 1-2 weeks" window is tighter than the field's actual publication cadence (which clusters around conferences and releases). The guardrails worked exactly as designed; the constraint that needs revising is the time window, not the agent's behavior.

## Limitations

- **Time-window mismatch:** the "1-2 weeks" recency requirement is often too strict for how ML/CV news actually publishes (in bursts around conferences, not a steady weekly stream), so the agent frequently returns an honest "nothing new" rather than a populated brief.
- **No independent fact-checking beyond venue reputation:** the agent distinguishes vendor claims from peer-reviewed venues (e.g. ICLR acceptance), but does not independently re-verify numbers itself.
- **Topic ambiguity isn't always guaranteed to be caught:** it caught the "traffic AI" (cybersecurity vs. transportation) ambiguity on its own in testing, but this isn't a guaranteed behavior on every ambiguous topic.
- **Read-only by design:** the agent never posts, emails, or publishes anything; a human always reviews the brief before it's used anywhere.

## Built with AI (transparency)

This agent's design (spec, eval cases, guardrails) and this documentation were built in collaboration with Claude, used as a thinking partner and drafting tool throughout the FL-06 through FL-09 process. I wrote the actual job scope, made every judgment call on tradeoffs (e.g., choosing a Claude Project over an n8n workflow), and personally ran and verified all 4 real eval runs and the screen capture referenced above, none of the eval outputs or the build log were fabricated or simulated; they're logged from real runs.
