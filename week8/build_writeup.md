# Build Write-Up — TrafficSmart Portfolio

## The stack I chose, and why

Static HTML/CSS, hosted free on GitHub Pages (`al-najaf.github.io`). I considered three options before deciding (full reasoning in `Three_Roads.docx`): a no-code builder like Framer, a Python-based Streamlit app, and a hand-written static site. I picked static HTML because my actual needs were simple, 4 pages, real screenshots, a written case study, no live interactive demo yet, and a static site is the most stable, lowest-maintenance option for that scope. Streamlit would have played to my Python background, but it runs a live server I'd have to maintain and makes portfolios look like data apps rather than designed sites, working against the identity kit I'd already built.

## The hardest thing that broke

Two real breaks, both caught by actually testing rather than assuming:

1. **A mobile layout bug on the Contact page.** The button row (LinkedIn/GitHub/CV) used `display: flex` with no `flex-wrap`, so on a narrow phone screen the buttons had no way to wrap to a second line and could overflow off-screen. Found by reviewing the actual CSS during "Open It on Your Phone," not by guessing.
2. **An oversized image quietly hurting load time.** My About page photo was 638KB at 1024×1024px despite displaying as a 160px circular avatar. Every other image on the site was 30-85KB; this one was a real outlier I only caught by checking actual file sizes rather than eyeballing "does it look fine."

Both were fixed directly: added `flex-wrap: wrap`, and compressed the photo to 500×500 at quality 82, a 96% size reduction with no visible quality loss.

## What I'd build next

**Basic Needs Justice** — a system helping Pakistani citizens report and track exploitation or unfair treatment around basic needs (groceries, petrol, water pricing). It's a deliberately narrow starting slice of a bigger long-term interest (helping ordinary people defend their rights against whoever has more money or power), with land disputes and case-tracking named honestly as future additions rather than attempted all at once. A concrete reminder is set in Google Calendar, 4 weeks out, to start it.

## Where AI did the heavy lifting

Claude, inside a dedicated "ML Portfolio Build" Project (holding my proof statement, voice card, identity kit, and content map as standing context), interviewed me question-by-question about TrafficSmart, the real problem, my actual technical decisions, and what genuinely went wrong, then turned my honest, unpolished answers into the three-beat case study that's live on the Work page today. Claude also helped design and pressure-test the site's structure before a single page was built, and helped build the working contact form's backend wiring (Formspree integration) once I'd chosen the approach. Every real decision (what to include, which stack to pick, what counts as "done well") was mine; Claude's role was thinking partner and builder, not author.
