# OntoMorph sample data

Synthetic fixtures for building and testing against the OntoMorph platform and
the HOLON clinical-knowledge API. Use these to develop against realistic shapes
without needing a live patient or a provisioned key.

> **None of this is real patient data.** Every twin, event, and identifier is
> fabricated for development. Do not treat any value here as clinically valid.

## Layout

| Directory | Contents |
| --- | --- |
| `twins/` | Synthetic digital twins — one JSON file per patient, containing their profile and a stream of `HealthEvent`s across body systems. |
| `grants/` | Demo grant tokens (and the claims they decode to) for connecting to the sample twins with [`@dtp/sdk`](https://www.npmjs.com/package/@dtp/sdk). |
| `holon/` | Example HOLON payloads — concept lookups, drug-interaction checks, and cross-vocabulary mappings — mirroring `@holon/client` responses. |

## Fixture shapes

**`twins/<id>.json`** — a twin and its events. Each event matches the
`HealthEvent` shape returned by twin-core; clinical fields live inside `data`:

```jsonc
{
  "twinId": "twin_demo_0001",
  "events": [
    {
      "id": "evt_0001",
      "twinId": "twin_demo_0001",
      "eventType": "observation",
      "occurredAt": "2026-01-04T09:12:00Z",
      "title": "LDL cholesterol",
      "data": { "system": "cardiovascular", "code": "LDL", "value": 168, "unit": "mg/dL" }
    }
  ]
}
```

**`grants/<id>.json`** — a demo grant token plus its decoded `GrantClaims`
(`grantId`, `twinId`, `systems`, `eventTypes`), so you can `dtp.twins.connect(token)`.

**`holon/*.json`** — captured `@holon/client` responses (search hits, interaction
checks, mapping translations) for offline development.

## Usage

Point your **test** credentials at these fixtures while building. A typical loop:

```ts
import { DTP } from "@dtp/sdk";
import grant from "./grants/twin_demo_0001.json" assert { type: "json" };

const dtp = new DTP({ apiKey: process.env.DTP_TEST_KEY }); // dtp_test_…
const twin = await dtp.twins.connect(grant.token);
const cardio = await twin.systems.get("cardiovascular");
```

See the developer docs at <https://developer.ontomorph.com/docs>.

## Status

Fixtures are being added incrementally. If a directory is empty, that dataset is
not published yet — track progress in the repository issues.
