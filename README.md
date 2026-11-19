# Open recipe exchange — first groundwork

This repository holds an early, tentative proposal for an globally interoperable way to exchange recipes as machine readable and structured scientific data.

It is the **very first groundwork**, not a finished specification. We are calling for international collaboration and contribution to turn it into a shared standard that works across food-composition databases, recipe systems, research, public health, education and industry.

“Intelligent” here means that the data should be explicit and machine-actionable: a receiving system should be able to distinguish an ingredient from a prepared sub-recipe, preserve exact quantities and states, follow nested compositions safely, identify external food codes and their releases, and understand where assertions came from—without guessing from labels or presentation text.

This is an independent proposal. **It is not an adopted EuroFIR, EFSA or other international standard, and no endorsement is claimed.** Names, identifiers and values in the example are illustrative except where an external source is explicitly cited.

## What is here

- [`recipe-record.schema.json`](recipe-record.schema.json) — an illustrative JSON Schema 2020-12 profile with reusable `$defs`, recursive recipe composition, release-qualified code references and governed extensions.
- [`recipe-record.example.json`](recipe-record.example.json) — an example that conforms to the schema. It contains a cooked tomato base nested inside a tomato, basil and garlic sauce, alongside FoodEx2 classifications and structured source references.

The schema deliberately focuses on the exchange skeleton. It does **not** yet constitute the complete EuroFIR component-value model, a nutrient calculator, or all rules needed for portable calculation replay. Catalogue membership, logical graph validation, unit commensurability, mass balance and nutrient calculations require semantic validation beyond JSON Schema.

The FoodEx2 notation in the example reproduces an EFSA training example. Its present catalogue release and status are not asserted, so the example honestly records an unknown release. Such a record is useful for discussing transport and identity, but is not fully release-pinned or calculation-ready.

## An open invitation

No single organization or discipline can define this well alone. We warmly welcome:

- food-composition and nutrition scientists;
- recipe, database and interoperability developers;
- terminology and classification specialists, including FoodEx2, LanguaL and FoodOn expertise;
- standards, provenance, licensing, security and archival specialists;
- people working with real recipes in different languages, cuisines, measurement traditions and regulatory settings;
- implementers willing to challenge the proposal with independent parsers and realistic examples.

Reviews, criticisms, counterexamples, alternative models, test fixtures and prototype implementations are all valuable contributions. Particularly useful early contributions are nested and shared sub-recipes, alternatives, edible portions, yield and retention, missing-data semantics, multilingual labels, release migration and safe round trips between independently developed systems.

The goal is not to preserve this first draft unchanged. It is to discover, together, what a durable and genuinely international recipe-exchange standard should be.

## Validate the example

A JSON Schema 2020-12 validator with format assertions is required. With Python and the `jsonschema` package installed, run from the repository root:

```sh
python3 - <<'PY'
import json
from pathlib import Path
from jsonschema import Draft202012Validator, FormatChecker

schema = json.loads(Path('recipe-record.schema.json').read_text(encoding='utf-8'))
example = json.loads(Path('recipe-record.example.json').read_text(encoding='utf-8'))

Draft202012Validator.check_schema(schema)
errors = sorted(
    Draft202012Validator(schema, format_checker=FormatChecker()).iter_errors(example),
    key=lambda error: list(error.absolute_path),
)
for error in errors:
    location = '/'.join(map(str, error.absolute_path)) or '<document>'
    raise SystemExit(f'{location}: {error.message}')
print('Schema and example are valid.')
PY
```

For an isolated local setup, create a virtual environment inside this folder and install `jsonschema` there before running the command.

## Design principles for this first draft

1. **One explicit logical model.** JSON is the proposed lossless exchange representation, with semantics defined independently of any single application or database.
2. **Recipes may contain recipes.** Nested prepared components remain complete immutable versions, with amounts measured at the point of use and cycle/depth checks handled by a semantic validator.
3. **Identity, classification and mapping are different.** A national food record is not silently made equivalent to a FoodEx2 or other external classification.
4. **Reproducibility needs releases.** Code references and dependencies carry exact release information or an explicit, attributable unknown-release state—never a fabricated `latest`.
5. **Sources remain flexible but meaningful.** Bare DOIs, DOI URLs and other persistent identifiers are supported alongside typed source URLs, without requiring network retrieval during parsing.
6. **A closed core and open contributions.** Unknown fields fail strict structural validation, governed extensions have explicit ownership, and the entire proposal remains open to informed challenge and revision.

The README and schema are starting points for discussion, not claims of final authority.
