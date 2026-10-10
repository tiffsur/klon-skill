# The Klon skill

Eleven Claude skills that clone any app. Free, MIT, no signup, no API key,
nothing to connect.

One reverse-engineers the app you want to clone. One rebuilds it. One tests it
for bugs. And one is the Entrepreneur: it reads what the app's users hate and
fixes it in yours, so you have an app you can sell.

In between, the others plan the stack and the database, rebuild the design
system, wire up auth and payments, score your clone against the original, give
it a name and a brand of its own, write the landing page and the store
listing, and put it live on your domain.

**It rebuilds what an app does, never what it owns.** Features and flows,
clean-room style. Not its code, its logo, its copy or its content. The
fine print is at the bottom, and the skills enforce it.

## Install

Paste this into Claude:

```
https://github.com/tiffsur/klon-skill

install skill
```

Or as a plugin, in Claude Code:

```
/plugin marketplace add tiffsur/klon-skill
/plugin install klon-skill@klon-skill
```

Claude Code namespaces plugin skills, so installed as a plugin they show up as
`/klon-skill:klon-recon` and so on. Copy the folders instead if you want
plain `/klon-recon`:

```bash
git clone https://github.com/tiffsur/klon-skill.git
cp -r klon-skill/klon-* ~/.claude/skills/
```

Project-local instead of global: copy the same folders into your repo's
`.claude/skills/`. No Claude Code at all? Paste any single `SKILL.md` at the
top of a chat and it runs as a mode. You lose the Python tools, but the
method works.

The tools need Python 3.8 or newer. Nothing to pip install.

## The eleven

| command | what it does |
| --- | --- |
| `/klon-recon` | Reverse-engineers any app: screens, flows, components, data model. From public pages, screenshots, store listings and your own account. |
| `/klon-architect` | Plans the stack, database schema and API for your clone. |
| `/klon-design` | Rebuilds the design system: colours, type, spacing, components. As tokens, with your own assets. |
| `/klon-build` | Rebuilds the app screen by screen from the recon map. |
| `/klon-backend` | Auth, database, payments and integrations. |
| `/klon-test` | Clicks through every flow and tests it for bugs. |
| `/klon-diff` | Compares your clone against the original. A parity score and what is missing. |
| `/klon-entrepreneur` | Reads what the app's users hate in real reviews and turns it into fixes and a positioning angle. |
| `/klon-brand` | Names and rebrands your version so it is yours. |
| `/klon-launch` | Landing page, pricing and App Store listing. |
| `/klon-deploy` | Ships it live on your own domain. |

## How to use it

Run them in order. Each one reads what the last one wrote, in a `klon/`
folder in your project.

```
recon -> architect -> design -> build -> backend -> test -> diff -> entrepreneur -> brand -> launch -> deploy
```

An example: cloning a scheduling app, the kind where you share a link and
people book a time with you.

1. **`/klon-recon`** with the app's URL. It reads the help center, the
   pricing page, the store listing and public walkthroughs, and you click
   through your own account with it. Out comes `klon/recon.md`: 18
   screens, 7 flows (guest books a meeting, host sets availability, guest
   reschedules...), the components, an inferred data model (users, event
   types, availability, bookings) and `features.csv`. The partner
   marketplace is marked out of scope: that is their network, not a feature.
2. **`/klon-architect`** picks Next.js, Postgres, Stripe and Resend, writes
   the schema (with a constraint so two guests can never book the same slot)
   and orders the build: the booking flow end to end first.
3. **`/klon-design`** measures the screenshots into tokens: colour roles,
   type scale, 8px spacing, the date picker and slot button specs. Open
   icons, an open font, your own words.
4. **`/klon-build`** builds the shell, then the booking flow, then every
   screen with all its states, ticking off `features.csv` as it goes.
5. **`/klon-backend`** adds sign up, Google Calendar through Google's own
   API with your keys, Stripe for paid bookings, reminder emails.
6. **`/klon-test`** writes a test plan from the 7 flows, Playwright specs
   for the happy paths and edge cases (time zones, a slot taken mid-booking,
   double submit), and logs bugs by severity until no S1 or S2 is open.
7. **`/klon-diff`** scores it. Features 86, all must-haves done, booking
   page layout 91. Missing: round-robin for teams, the website embed.
8. **`/klon-entrepreneur`** reads 140 real reviews across the App Store,
   G2, Capterra and Reddit. Top complaints: per-seat price jumps, no text
   reminders, guests confused by time zones. Each with counts and linked
   quotes. Fix plan: flat pricing, SMS reminders, a time zone confirm step.
   Angle: booking links for small teams that hate per-seat pricing.
9. **`/klon-brand`** names it (with the trademark and domain checks to
   run), gives it a new palette, a logo brief and a voice, then sweeps the
   codebase until nothing of the original is left.
10. **`/klon-launch`** writes the landing page around the angle, sets
    pricing against the original's public page, and lints the store listing.
11. **`/klon-deploy`** runs the preflight (tests, parity, sweep, listing),
    sets up production, gives you the DNS records for your domain, and ships
    it when you say go.

You can also run any one on its own. `/klon-entrepreneur` on an app you
are only thinking about cloning is a good way to find out if you should.

## The tools

Six of them, all standard-library Python. None touch the network.

```bash
python3 klon-diff/imgdiff.py original.png clone.png --out diff.png   # layout diff, ignores colour
python3 klon-diff/parity.py klon/features.csv                     # parity score + missing list
python3 klon-entrepreneur/reviews.py klon/reviews.csv             # what users hate, ranked, linked
python3 klon-design/contrast.py klon/design/tokens.json           # WCAG contrast on your tokens
python3 klon-brand/sweep.py . --avoid "Original App"                 # anything of the original left?
python3 klon-launch/listing.py klon/launch/listing.json           # store limits + copycat checks
```

**`imgdiff.py`** reads PNGs with no libraries, turns both screenshots into
edge maps and compares where things are, so your new colours do not count
against you. It tells you which regions differ, in the original's pixels.

**`parity.py`** weights must, should and could, counts partial as half, and
never scores what you left out on purpose or what you added. Missing
must-haves means not shippable, and it says so.

**`reviews.py`** drops every review without a link. Every quote it prints is
copied from a row you gave it, with that row's URL. Themes with under three
reviews or one source are marked thin.

**`sweep.py`** finds the original's name, domain and colours anywhere in your
code, including inside identifiers like `OriginalAppEmbed`, and blocks the
deploy until it is clean.

**`listing.py`** checks App Store and Google Play limits, ranking claims in
the title, wasted keyword characters, and the original's name anywhere in
your listing.

```bash
python3 -m unittest discover -s tests -v
```

## Fine print

**It rebuilds functionality and UX patterns, clean-room style.** It studies
what the app does and how people move through it, then writes everything
fresh. It never copies the target's source code, proprietary assets, logos,
trademarks, copy or private APIs.

**It only reads what you are allowed to read.** Public pages and your own
account. It never scrapes behind a login or against a site's terms, never
logs into anyone else's account, and never gets past a paywall. If your
account's terms forbid using it to build a competitor, it says so and sticks
to public sources.

**It always rebrands before launch.** New name, new palette, new logo, new
words. `/klon-deploy` will not ship until the sweep is clean.

**"Clone any app" means the features and the flow.** Not the content, the
network or the licences an app owns. You can rebuild a music app's player,
playlists and sharing. You cannot clone its catalogue.

**No guarantee of a "perfect" clone.** Results depend on how complex the app
is. A booking tool is weeks. A spreadsheet engine is not. `/klon-recon`
sizes it honestly before you start, and `/klon-diff` gives you a real
number, not a vibe.

**The Entrepreneur never makes things up.** No invented reviews, quotes or
numbers. Every claim has a link, and the sample size is stated.

**Check before you sell.** Run the trademark checks `/klon-brand` lists,
read the terms of anything you used, and talk to a lawyer before launch if
there is money on the line. This is not legal advice.

## Files

```
klon-recon/         SKILL.md, recon-map.md, features.csv (the matrix template)
klon-architect/     SKILL.md, architecture.md
klon-design/        SKILL.md, tokens.json, contrast.py
klon-build/         SKILL.md
klon-backend/       SKILL.md
klon-test/          SKILL.md, test-plan.md, bug-report.md, e2e.example.spec.ts
klon-diff/          SKILL.md, imgdiff.py, parity.py
klon-entrepreneur/  SKILL.md, reviews.py, themes.json
klon-brand/         SKILL.md, sweep.py
klon-launch/        SKILL.md, listing.py, listing.example.json
klon-deploy/        SKILL.md, preflight.md
tests/                 the tests for every tool
```

Your own files live in `klon/` in your project. The skills read each
other's.


## License

MIT. Take it, change it, ship it.
