# PlantUML JS Engine — Patched Size Limit

Patched build of the PlantUML TeaVM JavaScript engine with the hardcoded 4096px diagram size limit
increased to 98304px.

## What's changed

The official `plantuml.js` (compiled from Java via [TeaVM](https://teavm.org/)) has a hardcoded
4096×4096 pixel maximum diagram size. Diagrams exceeding this fail with:

```
java.lang.RuntimeException: Diagram too large for browser rendering: WxH (max 4096)
```

This patched version replaces the two `4096.0` comparisons in the compiled bytecode with `98304.0`,
allowing diagrams up to 98304×98304 pixels, and updates the error message's `(max 4096)` to
`(max 98304)` to match.

## Versions

| tag | base | module format |
|---|---|---|
| `v1.2026.6-patched` | npm [`@plantuml/core`](https://www.npmjs.com/package/@plantuml/core) 1.2026.6 (MIT) | ES module — ends in `export { … as render, … as renderToString }`; no `plantumlLoad` |
| `v1.2026.3beta6-patched` | js-plantuml 1.2026.3beta6 from `https://plantuml.github.io/plantuml/js-plantuml/` | classic script — defines `plantumlLoad` / `window.plantuml.render` |

## Usage

### Via jsDelivr CDN (`v1.2026.6-patched`, ES module)

```html
<script src="https://cdn.jsdelivr.net/gh/lemonlion/plantuml-js-plantuml_limit_size_98304@v1.2026.6-patched/viz-global.js"></script>
<script type="module">
  import { render } from "https://cdn.jsdelivr.net/gh/lemonlion/plantuml-js-plantuml_limit_size_98304@v1.2026.6-patched/plantuml.js";
  render(["@startuml", "a -> b: hello", "@enduml"], "targetElementId", {});
</script>
```

`viz-global.js` (Graphviz, used for non-sequence diagrams) is still a classic script. Consumers that
need the engine inside a Web Worker or Node `vm` context (which cannot evaluate `export` statements)
can rewrite the trailing `export{C as render,D as renderToString};` into an assignment — this is what
[Kronikol](https://github.com/lemonlion/Kronikol) does.

### Via jsDelivr CDN (`v1.2026.3beta6-patched`, classic)

```html
<script src="https://cdn.jsdelivr.net/gh/lemonlion/plantuml-js-plantuml_limit_size_98304@v1.2026.3beta6-patched/viz-global.js"></script>
<script src="https://cdn.jsdelivr.net/gh/lemonlion/plantuml-js-plantuml_limit_size_98304@v1.2026.3beta6-patched/plantuml.js"></script>
```

## License

`@plantuml/core` 1.2026.6 is MIT-licensed. The older 1.2026.3beta6 build is from the GPL v2
js-plantuml distribution — see [PlantUML License](https://plantuml.com/license).
