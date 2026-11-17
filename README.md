# Skills for Designers and Engineers

[![skills.sh](https://skills.sh/b/nklswbr/skills)](https://skills.sh/nklswbr/skills)

Skills that help designers and engineers make better products: cut deeper, catch what breaks before users do, and hold the work to your own rules.

Agents are good at adding. They add a setting nobody asked for, a reward that pays for the wrong behavior, a card inside a card. These skills push the other way: subtraction when refining, adversarial thinking when testing, and judgment against your own rules instead of the agent's taste.

## Install

```bash
npx skills@latest add nklswbr/skills
```

## Why use it?

**Agents add by default.** Ask one to improve something and it reaches for another part, another step, another line of copy. People do the same: we systematically overlook subtractive changes. `subtract` makes removal the first option it considers, and counts what it saves.

**Demo users play nice. Real users play the rules.** A referral program, a free trial, a review system, a leaderboard: each one looks fair until someone does the math on it. `break-game-theory` does that math first, with the players you didn't picture.

**Taste doesn't transfer, rules do.** "Make it on brand" means nothing to the next agent. A DESIGN.md does. `judge` holds the work up against yours, quotes the rule for every failure, and turns every gap it finds into a new rule, so the same mistake doesn't come back.

## Reference

- **[subtract](skills/subtract)** — Move forward by taking things away. Inventories a screen, flow, feature, copy, or module, runs each part down the ladder (delete, merge, default, hide, shorten, keep), and reports the cuts with before/after counts.
- **[break-game-theory](skills/break-game-theory)** — Stress-tests your app's incentives. Plays your app as its most rational, self-interested users (multi-accounters, colluders, snipers, griefers, the company itself), proves the worst exploits in dev, and proposes mechanism fixes that make honesty the best strategy.
- **[judge](skills/judge)** — Judge a page or component against your DESIGN.md. Every finding quotes the rule it breaks; mechanical rules are checked with code, judgment rules by eye; gaps in the document come back as proposed rules. Built on the [design.md approach from Vercel](https://vercel.com/blog/how-our-agents-build-on-brand-pages-with-design-md).

## Structure

```
skills/
  <name>/
    SKILL.md        # the skill: frontmatter + workflow
    *.md            # references the skill loads when it needs them
```

Each skill does one thing, has hard rules, a phased workflow with completion criteria, a fixed output format, and stops before changing anything until you say so.

## License

MIT
