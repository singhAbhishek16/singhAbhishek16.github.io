+++
title = "Data Pipeline"
#
# Set menu to "main" to add this page to
# the main menu on top of the page
#
weight = 50
# menu = "main"
+++

### The Full Story

Zoom-bombing cases were a rising pattern during COVID — a problem worth solving for. This solution is a proactive attempt 
to build a reliable way to collect repeat offenders' data & feed them to Trust Score, a separate system that scores users on 
their behaviors so the company can act on serial offenders. This turned out to be a better approach than treating every incident like
it's the first time.

The honeypots were real Zoom meetings, launched through the Zoom SDK, with fake participants to make them feel legit — if a bad actor 
could tell it was a setup, the whole thing was useless. I ran the whole thing through a headless Chromium browser on locked-down, 
dedicated infra, not accessible from public internet. Application is designed to make everything hands-off: meetings got scheduled, 
launched, and closed automatically based on rules Trust & Safety laid out, with auth built in so only the right people could see 
what got collected.

I was responsible for backend, frontend, automation and authentication design. Trust & Safety had signed off before launch. Then priorities 
shifted higher up, and it got shelved before it ever went live.