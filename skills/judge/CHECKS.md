# Mechanical Checks

Ready-made checks for the rules DESIGN.md files usually contain. Run only the ones that match a rule in your Phase 1 checklist, and adapt the values (token lists, allowed fonts, banned characters, widths) to what the document actually says.

All snippets run in the page, so they work with any browser tooling: Playwright's `page.evaluate`, a browser MCP's JavaScript tool, or the devtools console.

---

## Harness

A minimal Playwright runner that screenshots each width and runs every check. Save it outside the project's source (e.g. a scratch folder) and delete it afterwards unless the user wants to keep it as a CI check.

```js
// judge-run.mjs — node judge-run.mjs <url> [outDir]
import { chromium } from 'playwright';
import { mkdirSync, writeFileSync } from 'node:fs';
import { checks } from './judge-checks.mjs';

const [url, outDir = './judge-out'] = process.argv.slice(2);
const widths = [1440, 768, 375]; // use the widths DESIGN.md names, if any
mkdirSync(outDir, { recursive: true });

const browser = await chromium.launch();
const results = {};
for (const width of widths) {
  const page = await browser.newPage({ viewport: { width, height: 900 } });
  await page.goto(url, { waitUntil: 'networkidle' });
  await page.screenshot({ path: `${outDir}/${width}.png`, fullPage: true });
  results[width] = {};
  for (const [name, fn] of Object.entries(checks)) {
    results[width][name] = await page.evaluate(fn);
  }
  await page.close();
}
await browser.close();
writeFileSync(`${outDir}/results.json`, JSON.stringify(results, null, 2));
console.log(JSON.stringify(results, null, 2));
```

`judge-checks.mjs` exports `checks`, an object of the functions below. Each takes no arguments and returns plain JSON, so edit the constants at the top of each one.

---

## Colors outside the tokens

Rule shapes: "Use only the palette tokens", "No raw hex values", "Use color only from the published variables."

```js
export const offTokenColors = () => {
  // Fill from DESIGN.md: either the literal values, or read the CSS variables.
  const tokenVars = ['--background', '--foreground', '--muted', '--accent', '--border'];
  const root = getComputedStyle(document.documentElement);
  const toRgb = (v) => { const d = document.createElement('div'); d.style.color = v; document.body.appendChild(d); const c = getComputedStyle(d).color; d.remove(); return c; };
  const allowed = new Set(tokenVars.map((v) => toRgb(root.getPropertyValue(v).trim())).concat(['rgba(0, 0, 0, 0)']));
  const hits = {};
  for (const el of document.querySelectorAll('body *')) {
    const s = getComputedStyle(el);
    for (const prop of ['color', 'backgroundColor', 'borderTopColor']) {
      const v = s[prop];
      if (prop === 'borderTopColor' && s.borderTopWidth === '0px') continue;
      if (!allowed.has(v)) (hits[v] ??= []).length < 3 && hits[v].push(`${el.tagName.toLowerCase()}${el.className && typeof el.className === 'string' ? '.' + el.className.trim().split(/\s+/).join('.') : ''} (${prop})`);
    }
  }
  return hits; // { "rgb(…)": ["selector (prop)", …] }
};
```

## Fonts outside the allowed list

Rule shapes: "Use only Inter and Geist Mono", "Mono only for code, paths, and timestamps."

```js
export const fonts = () => {
  const allowed = ['Inter', 'Geist Mono']; // from DESIGN.md
  const monoFaces = ['Geist Mono'];
  const codeTags = new Set(['CODE', 'PRE', 'KBD', 'SAMP', 'TIME']);
  const offList = new Set(), monoMisuse = [];
  for (const el of document.querySelectorAll('body *')) {
    if (![...el.childNodes].some((n) => n.nodeType === 3 && n.textContent.trim())) continue;
    const first = getComputedStyle(el).fontFamily.split(',')[0].replace(/["']/g, '').trim();
    if (!allowed.includes(first)) offList.add(first);
    if (monoFaces.includes(first) && !el.closest('code, pre, kbd, samp, time') && !codeTags.has(el.tagName))
      monoMisuse.length < 10 && monoMisuse.push(el.textContent.trim().slice(0, 60));
  }
  return { offList: [...offList], monoMisuse };
};
```

## Banned characters and phrases

Rule shapes: "No em dashes", "Don't say 'seamless'", "No authoring-process narration."

```js
export const bannedText = () => {
  const banned = [/—/g, /\bseamless(ly)?\b/gi, /\bAs an AI\b/gi]; // from DESIGN.md
  const text = document.body.innerText;
  return banned.map((re) => ({ pattern: String(re), count: (text.match(re) || []).length, sample: (text.match(new RegExp(`.{0,30}${re.source}.{0,30}`, re.flags.replace('g', ''))) || [''])[0] }))
    .filter((r) => r.count);
};
```

## Uppercase or tracked eyebrows

Rule shapes: "No all-caps eyebrows, kickers, or overlines."

```js
export const eyebrows = () => [...document.querySelectorAll('body *')]
  .filter((el) => {
    const s = getComputedStyle(el);
    const txt = el.innerText?.trim() ?? '';
    const tracked = parseFloat(s.letterSpacing) > 0.5;
    return txt && txt.length < 40 && el.children.length === 0 && (s.textTransform === 'uppercase' || (txt === txt.toUpperCase() && /[A-Z]{3}/.test(txt) && tracked));
  })
  .slice(0, 15).map((el) => el.innerText.trim());
```

## Nested cards

Rule shapes: "No nested cards", "One surface level per section."

```js
export const nestedCards = () => {
  const isCard = (el) => {
    const s = getComputedStyle(el);
    const bordered = s.borderTopWidth !== '0px' && s.borderTopStyle !== 'none';
    const shadowed = s.boxShadow !== 'none';
    const filled = s.backgroundColor !== 'rgba(0, 0, 0, 0)' && s.backgroundColor !== getComputedStyle(document.body).backgroundColor;
    return parseFloat(s.borderTopLeftRadius) > 0 && (bordered || shadowed || filled) && el.offsetWidth > 120 && el.offsetHeight > 60;
  };
  const cards = [...document.querySelectorAll('body *')].filter(isCard);
  return cards.filter((c) => cards.some((p) => p !== c && p.contains(c)))
    .slice(0, 10).map((c) => c.innerText.trim().slice(0, 50));
};
```

## Narrow tables

Rule shapes: "Tables get full content width", "Right-align numeric columns."

```js
export const tables = () => [...document.querySelectorAll('table')].map((t) => {
  const container = t.parentElement.closest('main, section, article') ?? t.parentElement;
  const ratio = t.offsetWidth / container.clientWidth;
  const misaligned = [...t.querySelectorAll('td')].filter((td) => /^[\s$€£%+\-−.,\d]+$/.test(td.innerText) && td.innerText.trim() && getComputedStyle(td).textAlign !== 'right' && getComputedStyle(td).textAlign !== 'end').length;
  return { caption: (t.caption?.innerText || t.innerText).trim().slice(0, 40), widthRatio: +ratio.toFixed(2), numericCellsNotRightAligned: misaligned };
});
```

## Horizontal overflow

Rule shapes: "Works at 320px", "No horizontal scroll." Run at the narrowest width.

```js
export const overflow = () => {
  const vw = document.documentElement.clientWidth;
  return [...document.querySelectorAll('body *')]
    .filter((el) => el.getBoundingClientRect().right > vw + 1 && getComputedStyle(el).position !== 'fixed')
    .filter((el, _, all) => !all.some((p) => p !== el && p.contains(el)))
    .slice(0, 10).map((el) => `${el.tagName.toLowerCase()}: ${Math.round(el.getBoundingClientRect().right - vw)}px past edge`);
};
```

## Contrast

Rule shapes: "Body text meets WCAG AA", "No tiny muted prose." Checks text against the nearest opaque background; gradients and images need eyes.

```js
export const contrast = () => {
  const parse = (c) => (c.match(/[\d.]+/g) || []).map(Number);
  const lum = ([r, g, b]) => { const f = (v) => { v /= 255; return v <= 0.03928 ? v / 12.92 : ((v + 0.055) / 1.055) ** 2.4; }; return 0.2126 * f(r) + 0.7152 * f(g) + 0.0722 * f(b); };
  const bgOf = (el) => { for (let e = el; e; e = e.parentElement) { const c = parse(getComputedStyle(e).backgroundColor); if (c.length === 3 || (c.length === 4 && c[3] === 1)) return c.slice(0, 3); } return [255, 255, 255]; };
  const fails = [];
  for (const el of document.querySelectorAll('body *')) {
    if (![...el.childNodes].some((n) => n.nodeType === 3 && n.textContent.trim())) continue;
    const s = getComputedStyle(el);
    const size = parseFloat(s.fontSize), bold = +s.fontWeight >= 700;
    const L1 = lum(parse(s.color).slice(0, 3)), L2 = lum(bgOf(el));
    const ratio = (Math.max(L1, L2) + 0.05) / (Math.min(L1, L2) + 0.05);
    const need = size >= 24 || (bold && size >= 18.66) ? 3 : 4.5;
    if (ratio < need) fails.length < 15 && fails.push({ text: el.textContent.trim().slice(0, 40), ratio: +ratio.toFixed(2), need, fontSize: size });
  }
  return fails;
};
```

## Tap targets

Rule shapes: "Touch targets at least 44px", "Buttons usable on mobile." Run at the mobile width.

```js
export const tapTargets = () => [...document.querySelectorAll('a, button, [role=button], input, select, textarea, summary')]
  .map((el) => ({ el, r: el.getBoundingClientRect() }))
  .filter(({ r }) => r.width && (r.width < 44 || r.height < 44))
  .slice(0, 15).map(({ el, r }) => `${(el.innerText || el.getAttribute('aria-label') || el.tagName).trim().slice(0, 30)}: ${Math.round(r.width)}×${Math.round(r.height)}`);
```

## Classes outside the public API

Rule shapes: "Use only the published `vbg-*` classes", "Don't invent utility classes." Fill the allowed prefixes or list from DESIGN.md.

```js
export const unlistedClasses = () => {
  const prefix = 'brand-'; // the published class prefix
  const allowed = new Set([/* exact class names DESIGN.md lists */]);
  const used = new Set([...document.querySelectorAll('[class]')].flatMap((el) => [...el.classList]));
  return [...used].filter((c) => c.startsWith(prefix) && allowed.size && !allowed.has(c));
};
```

---

## Turning a finding into a permanent check

When a mechanical failure shows up twice, it belongs in CI rather than in the next judge run. Move the check into the project's test suite (Playwright test, Storybook test runner, or a lint rule for source-level checks like banned characters or raw hex values), point it at the pages it applies to, and add one line to DESIGN.md noting the check exists.
