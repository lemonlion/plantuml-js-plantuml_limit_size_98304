# PlantUML JS Engine — CDN Builds

Tagged builds of the PlantUML TeaVM JavaScript engine, served via jsDelivr. The repository started
as a fork carrying a patched diagram size limit (4096px raised to 98304px); from `v1.2026.8beta1`
onward the builds are **stock** — upstream added a `maxSvgSize` render option that replaces the
patch, so consumers pass the limit at render time instead.

## Versions

| tag | base | size limit | module format |
|---|---|---|---|
| `v1.2026.10beta1-f44b054-teavm0.17.0-dev-2` | [plantuml/plantuml](https://github.com/plantuml/plantuml) master `f44b054d1` (1.2026.10beta1, MIT flavor, stock `npmPackage` build) compiled with TeaVM 0.17.0-dev-2 | default 8192px; pass `{maxSvgSize: N}` to `render` | ES module, as below; and the Wasm GC engine with the same API (`plantuml-wasm.js`), also packed into one script (`plantuml-wasm-bundle.js`) |
| `v1.2026.8beta1-0e4f452` | [plantuml/plantuml](https://github.com/plantuml/plantuml) master `0e4f452e` (1.2026.8beta1, MIT flavor, stock `npmPackage` build) | default 8192px; pass `{maxSvgSize: N}` to `render` | ES module — ends in `export { … as render, … as renderToString }`; no `plantumlLoad` |
| `v1.2026.6-patched` | npm [`@plantuml/core`](https://www.npmjs.com/package/@plantuml/core) 1.2026.6 (MIT) | patched to 98304px | ES module |
| `v1.2026.3beta6-patched` | js-plantuml 1.2026.3beta6 from `https://plantuml.github.io/plantuml/js-plantuml/` | patched to 98304px | classic script — defines `plantumlLoad` / `window.plantuml.render` |

### What's in `v1.2026.10beta1-f44b054-teavm0.17.0-dev-2`

A stock build of upstream master at
[`f44b054d1`](https://github.com/plantuml/plantuml/commit/f44b054d1) via `gradlew :plantuml-mit:npmPackage -Pci`
(JDK 25), with one change to the build: TeaVM 0.17.0-dev-2, from TeaVM's own repository
(`https://teavm.org/maven/repository`), in place of 0.16.0. That TeaVM build carries the fix for the regular
expression stack overflow reported in [teavm#1295](https://github.com/konsoletyper/teavm/issues/1295)
([teavm#1296](https://github.com/konsoletyper/teavm/pull/1296)), so a long arrow label or participant name no longer
runs the engine out of stack in a Web Worker. Nothing in PlantUML's sources is patched.

The files:

- `plantuml.js`: the JavaScript engine.
- `plantuml-wasm.js`, `plantuml.wasm` and `plantuml.wasm-runtime.js`: the package's Wasm GC engine, with the same API
  as `plantuml.js`. It needs Wasm GC (Chrome 119, Firefox 120, Safari 18.2 or later), and its runtime builds
  functions with `new Function`, so a content security policy has to allow `'unsafe-eval'` as well as
  `'wasm-unsafe-eval'`. Where either is missing, use `plantuml.js`.
- `plantuml-wasm-bundle.js`: the three files above packed into one script by
  [Kronikol](https://github.com/lemonlion/Kronikol), for a host that takes the engine as text (a classic Web Worker,
  a Node `vm` context): the runtime in function scope, `plantuml.wasm` embedded as base64 with its own decoder, and
  the package's `plantuml-wasm.js` unchanged but for its `import` line, its `import.meta` default and its `export`
  keywords. It ends in the same `export{… as render,… as renderToString}` as `plantuml.js`, starts loading the
  module at once, and sets `globalThis.__plantumlEngineReady`, a function whose promise settles when the module is
  ready or cannot load. The wrapper's crash recovery comes with it: a trap or a stack overflow fails the pending
  requests and the next call loads a fresh instance.
- `viz-global.js` (byte-identical to the one in `@plantuml/core` 1.2026.8), `themes.js`, `emoji.js`,
  `openiconic.js` and the package's `LICENSE` (MIT).

The text files are as the build wrote them on Windows, with the carriage returns upstream's own npm releases carry.

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

### Via jsDelivr CDN (`v1.2026.10beta1-f44b054-teavm0.17.0-dev-2`, Wasm GC)

```html
<script type="module">
  import { render } from "https://cdn.jsdelivr.net/gh/lemonlion/plantuml-js-plantuml_limit_size_98304@v1.2026.10beta1-f44b054-teavm0.17.0-dev-2/plantuml-wasm.js";
  render(["@startuml", "a -> b: hello", "@enduml"], "targetElementId", { maxSvgSize: 98304 });
</script>
```

`plantuml.js` at the same address is the JavaScript engine, for a runtime without Wasm GC.

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

The 1.2026.10beta1, 1.2026.8beta1 and 1.2026.6 builds are MIT-licensed (`plantuml-mit` flavor). The older
1.2026.3beta6 build is from the GPL v2 js-plantuml distribution — see
[PlantUML License](https://plantuml.com/license).
