# Zenovay status (backup)

The backup status page for Zenovay, served at **https://zenovay.github.io/status/**.
The main status page, with incidents and uptime history, is
**https://status.zenovay.com**.

## If you are here because status.zenovay.com will not load

You are in the right place. This page is checked from GitHub's servers and
hosted by GitHub, so nothing about it depends on Zenovay's own domain, hosting
or database. It keeps working in the outages it reports.

The current state is in [`summary.json` on the `gh-pages` branch](../../blob/gh-pages/summary.json):

- `states` has one entry per component. `operational` means the check ran and
  passed. `degraded` means the service answered but reported a problem.
  `outage` means it did not answer, or answered with an error, twice in a row.
  `unknown` means the checker could not get an answer (for example because our
  bot protection blocked it), which is not the same as broken.
- `generatedAt` is when the file was written. If it is more than an hour old,
  the checker is delayed and the results are out of date.

[`history.json`](../../blob/gh-pages/history.json) holds every state change,
and [`feed.xml`](../../blob/gh-pages/feed.xml) publishes them as an Atom feed.

For help, write to support@zenovay.com.

## How it works

| Path | What it is |
|---|---|
| `check.mjs` | Checks each site through a small static file it serves itself (static files pass our bot protection, page requests from a datacenter do not), and the API, sign-in service and databases through the API's health check at `/health.js`, which queries each of them. Compares with the previous run and writes the page. No dependencies. |
| `.github/workflows/status.yml` | Runs it on a schedule and publishes to the `gh-pages` branch. Holds no secret. |

Checks run every ten minutes, but GitHub often starts scheduled runs late, so
the page shows when each row was actually checked. It shows no uptime
percentage and collects no personal data.

To run it locally: `node check.mjs out`, then open `out/index.html`.
