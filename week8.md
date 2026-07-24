# Week 8 - Learning Experiment: Local Testing vs. Live-Deploy Testing

## Learning Activities & Resources

This week, instead of just doing the set practical, I ran a small experiment on my own
workflow, using the scientific method.

**Background:** In Week 5 (PeerSkill child theme), most of my time was lost to a
deployment problem, not a coding problem — a corrupted zip and a stuck InfinityFree
folder that I only discovered *after* uploading to the live server. That got me
wondering whether testing changes locally first, before ever touching the live host,
would actually save time — or whether it's just extra setup overhead that doesn't pay
off for small changes.

**Hypothesis:** Testing WordPress theme changes on a local server (e.g. Local by
Flywheel, or a Docker/XAMPP stack) before uploading to InfinityFree will let me find
and fix bugs faster than my previous method of editing and testing directly on the
live host via FTP/File Manager.

**Test (method):**
1. Pick two comparable small theme tasks of similar difficulty — e.g. Task A: add a
   new custom post-type template; Task B: add a matching-difficulty custom widget
   area.
2. For Task A, use my old method: edit the files, zip, upload to InfinityFree via
   the web File Manager, refresh the live site, check for errors, and repeat the
   edit–upload–check loop until it works.
3. For Task B, use the new method: install a local WordPress environment, make the
   same class of change there, iterate until it works locally (refreshing
   `localhost` instead of the live site), and only upload to InfinityFree once,
   at the end, to confirm it also works live.
4. For both tasks, log a timestamp at the start, at each upload/refresh cycle, and
   at the point the feature is confirmed working. Count: (a) total elapsed time,
   and (b) number of upload/refresh cycles needed.
5. Compare total time and cycle count between Task A and Task B.

**Result:**
- Task A (live-host method — new custom post-type template): 47 minutes total,
  6 upload/refresh cycles. Most of the time was spent waiting on file uploads
  through InfinityFree's File Manager and re-checking the live site after each
  small fix, including one cycle lost to a caching issue where an old version of
  the template kept showing until I did a hard refresh.
- Task B (local-first method — matching-difficulty custom widget area): 34
  minutes total, including roughly 12 minutes of one-time local environment
  setup (Local by Flywheel). Once running locally, I went through 5 fix-and-check
  cycles in about 9 minutes combined, since each refresh was instant, then did a
  single live upload at the end (3 minutes) which worked first try.
- Task B was faster overall despite the setup overhead, and the difference would
  have been larger if I hadn't needed to install the local environment for the
  first time. This supports the hypothesis: the local-first method reduces
  wasted time mainly by removing the upload/DNS/caching delay from the
  edit-test-fix loop, not by making the coding itself faster. I'd expect the gap
  to widen further on bigger, multi-file changes where more cycles are needed.

## Estimated Hours of Explicit Learning Activity
Approximately 2–3 hours (including local environment setup, which is a one-time
cost that should pay off in future weeks)

## Content Insights
- Setting up a local WordPress environment took longer than expected the first
  time, but it's a one-off cost — future weeks won't need to repeat it.
- The upload/refresh loop on InfinityFree involves real wall-clock waiting (file
  upload speed, occasional caching), which local testing removes almost entirely.
- One local-vs-live discrepancy did show up: my local environment defaulted to
  PHP 8.2, while InfinityFree runs PHP 8.1, so a `readonly` property I tried
  using locally wasn't the issue this time — but it's a reminder that "works
  locally" isn't a guarantee, and checking the host's PHP version before relying
  too heavily on local testing is worth doing early.

## Career/Employability/Learning Insights
This experiment mattered more for *how* I work than for what I learned about
WordPress specifically. Testing changes locally before pushing to a live/shared
host is standard practice in real development teams — it's the same principle
behind staging environments and CI pipelines, just at a much smaller scale. Given
that Week 5's biggest time-sink was a live-deployment failure that local testing
would have caught immediately, this feels like a workflow change worth keeping
for the rest of the course, not just a one-off experiment.
