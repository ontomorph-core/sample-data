# OntoMorph sample data

Synthetic fixtures for building and testing against the OntoMorph platform and
the HOLON clinical-knowledge API. Use them to develop against realistic shapes
without a live patient or a provisioned key.

> None of this is real patient data. Every twin, event, and identifier is
> fabricated for development. Do not treat any value here as clinically valid.

> **The [wiki](https://github.com/ontomorph-core/sample-data/wiki) has the full guide:** getting started, concepts, guides, use cases, API reference, and FAQ.

## Concepts

A quick map of what lives here and why.

**Digital twin.** A living model of one patient's body, organized by body system
and built from their health events. The `twins/` fixtures are synthetic twins.

**Health event.** One timestamped entry on a twin, such as a lab result or an
observation. Clinical fields live inside the event's `data` object.

**Grant token.** The signed consent a patient issues to authorize access to one
twin. The `grants/` fixtures let you call `dtp.twins.connect(token)` without a
real consent flow.

## Layout

| Directory | Contents |
| --- | --- |
| `twins/` | Synthetic digital twins, one JSON file per patient, each holding a profile and a stream of `HealthEvent`s across body systems. |
| `grants/` | Demo grant tokens, and the claims they decode to, for connecting to the sample twins with [`@ontomorph/dtp-sdk`](https://www.npmjs.com/package/@ontomorph/dtp-sdk). |
| `holon/` | Example HOLON payloads (concept lookups, drug-interaction checks, cross-vocabulary mappings) mirroring `@ontomorph/holon-client` responses. |

## Fixture shapes

**`twins/<id>.json`** holds a twin and its events. Each event matches the
`HealthEvent` shape returned by twin-core, with clinical fields inside `data`:

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

**`grants/<id>.json`** holds a demo grant token plus its decoded `GrantClaims`
(`grantId`, `twinId`, `systems`, `eventTypes`), so you can `dtp.twins.connect(token)`.

**`holon/*.json`** holds captured `@ontomorph/holon-client` responses (search hits,
interaction checks, mapping translations) for offline development.

## Usage

Point your test credentials at these fixtures while building. A typical loop:

```ts
import { DTP } from "@ontomorph/dtp-sdk";
import grant from "./grants/twin_demo_0001.json" assert { type: "json" };

const dtp = new DTP({ apiKey: process.env.DTP_TEST_KEY }); // dtp_test_…
const twin = await dtp.twins.connect(grant.token);
const cardio = await twin.systems.get("cardiovascular");
```

See the developer docs at <https://developer.ontomorph.com/docs>.

## Status

Fixtures are added incrementally. An empty directory means that dataset is not
published yet. Track progress in the repository issues.
