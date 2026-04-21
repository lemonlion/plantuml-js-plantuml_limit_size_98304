# PlantUML JS Engine — Patched Size Limit

Patched build of the [PlantUML TeaVM JavaScript engine](https://plantuml.github.io/plantuml/js-plantuml) with the hardcoded 4096px diagram size limit increased to 98304px.

## What's changed

The official `plantuml.js` (compiled from Java via [TeaVM](https://teavm.org/)) has a hardcoded 4096×4096 pixel maximum diagram size. Diagrams exceeding this throw:

```
java.lang.RuntimeException: Diagram too large for browser rendering: WxH (max 4096)
```

This patched version replaces the two `4096.0` comparisons in the compiled bytecode with `98304.0`, allowing diagrams up to 98304×98304 pixels.

## Usage

### Via jsDelivr CDN

```html
<script src="https://cdn.jsdelivr.net/gh/lemonlion/plantuml-js-plantuml_limit_size_98304@v1.2026.3beta6-patched/viz-global.js"></script>
<script src="https://cdn.jsdelivr.net/gh/lemonlion/plantuml-js-plantuml_limit_size_98304@v1.2026.3beta6-patched/plantuml.js"></script>
```

## Source version

Based on PlantUML JS engine version **1.2026.3beta6** from `https://plantuml.github.io/plantuml/js-plantuml/`.

## License

PlantUML is licensed under GPL v2. See [PlantUML License](https://plantuml.com/license).
