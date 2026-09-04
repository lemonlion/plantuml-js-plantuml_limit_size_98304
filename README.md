# PlantUML JS Engine — CDN Builds

Tagged builds of the PlantUML TeaVM JavaScript engine, served via jsDelivr. The repository started
as a fork carrying a patched diagram size limit (4096px raised to 98304px); from `v1.2026.8beta1`
onward the builds are **stock** — upstream added a `maxSvgSize` render option that replaces the
patch, so consumers pass the limit at render time instead.

## Versions

| tag | base | size limit | module format |
|---|---|---|---|
| `v1.2026.8beta1-0e4f452` | [plantuml/plantuml](https://github.com/plantuml/plantuml) master `0e4f452e` (1.2026.8beta1, MIT flavor, stock `npmPackage` build) | default 8192px; pass `{maxSvgSize: N}` to `render` | ES module — ends in `export { … as render, … as renderToString }`; no `plantumlLoad` |
| `v1.2026.6-patched` | npm [`@plantuml/core`](https://www.npmjs.com/package/@plantuml/core) 1.2026.6 (MIT) | patched to 98304px | ES module |
| `v1.2026.3beta6-patched` | js-plantuml 1.2026.3beta6 from `https://plantuml.github.io/plantuml/js-plantuml/` | patched to 98304px | classic script — defines `plantumlLoad` / `window.plantuml.render` |

### What's in `v1.2026.8beta1-0e4f452`

A stock build of upstream master at
[`0e4f452e`](https://github.com/plantuml/plantuml/commit/0e4f452e5fe7b6220344ad34115c7579cc96ad4c)
via `gradlew :plantuml-mit:npmPackage -Pci` (JDK 25, obfuscated). Relative to 1.2026.6/1.2026.7 it
carries the merged performance work on the Teoz sequence engine and Smetana
([#2834](https://github.com/plantuml/plantuml/issues/2834),
[#2866](https://github.com/plantuml/plantuml/issues/2866): PRs #2835–#2839, #2858–#2860 plus the
LiveBoxes/SkinParam caches), browser `!theme` support via `themes.js`
([#2848](https://github.com/plantuml/plantuml/pull/2848)), SVG background painting
([#2849](https://github.com/plantuml/plantuml/pull/2849)), the Smetana layout pragma and
viz-missing fallback ([#2852](https://github.com/plantuml/plantuml/pull/2852),
[#2861](https://github.com/plantuml/plantuml/pull/2861)), and the `maxSvgSize` render option.
This tag also ships the engine's lazily-fetched companions: `themes.js`, `emoji.js`,
`openiconic.js`.

## Usage

### Via jsDelivr CDN (`v1.2026.8beta1-0e4f452`, ES module)

```html
<script src="https://cdn.jsdelivr.net/gh/lemonlion/plantuml-js-plantuml_limit_size_98304@v1.2026.8beta1-0e4f452/viz-global.js"></script>
<script type="module">
  import { render } from "https://cdn.jsdelivr.net/gh/lemonlion/plantuml-js-plantuml_limit_size_98304@v1.2026.8beta1-0e4f452/plantuml.js";
  render(["@startuml", "a -> b: hello", "@enduml"], "targetElementId", { maxSvgSize: 98304 });
</script>
```

`viz-global.js` (Graphviz, used for non-sequence diagrams) is still a classic script — and from
this build it is optional: when `Viz` is absent the engine falls back to the built-in Smetana
layout engine. Consumers that need the engine inside a Web Worker or Node `vm` context (which
cannot evaluate `export` statements) can rewrite the trailing
`export{C as render,D as renderToString};` into an assignment — this is what
[Kronikol](https://github.com/lemonlion/Kronikol) does.

### Via jsDelivr CDN (`v1.2026.6-patched`, ES module)

```html
<script src="https://cdn.jsdelivr.net/gh/lemonlion/plantuml-js-plantuml_limit_size_98304@v1.2026.6-patched/viz-global.js"></script>
<script type="module">
  import { render } from "https://cdn.jsdelivr.net/gh/lemonlion/plantuml-js-plantuml_limit_size_98304@v1.2026.6-patched/plantuml.js";
  render(["@startuml", "a -> b: hello", "@enduml"], "targetElementId", {});
</script>
```

### Via jsDelivr CDN (`v1.2026.3beta6-patched`, classic)

```html
<script src="https://cdn.jsdelivr.net/gh/lemonlion/plantuml-js-plantuml_limit_size_98304@v1.2026.3beta6-patched/viz-global.js"></script>
<script src="https://cdn.jsdelivr.net/gh/lemonlion/plantuml-js-plantuml_limit_size_98304@v1.2026.3beta6-patched/plantuml.js"></script>
```

## License

The 1.2026.8beta1 and 1.2026.6 builds are MIT-licensed (`plantuml-mit` flavor). The older
1.2026.3beta6 build is from the GPL v2 js-plantuml distribution — see
[PlantUML License](https://plantuml.com/license).
