# Registries of record — Canonical Payload Binding

**Status.** This document is the **interim registry of record** for the two
Canonical Payload Binding (CPB) registries, until RFC publication establishes the
corresponding IANA registries. The registries and their normative definitions are
in the Internet-Draft (`draft-mih-sokolov-scitt-payload-binding`, this
repository's `spec/`), **§11 (IANA Considerations)**. Registration policy:
**Specification Required** per [RFC 8126 §4.6]; a Designated Expert is required
for each registration. **Entries are immutable** — if a behavior change is
needed, a new entry MUST be registered; existing entries MUST NOT be modified
retroactively.

Change controller: **Action State Group, Inc.** (interim) → **IETF** on
publication. On working-group adoption, the provisional registry **moves with the
document** to a repository of the working group's choosing (draft §11).

**One registry home for the CPB document family.** These registries serve the
entire CPB family — this document and its companions — and this is the single
place any of them registers. Companion **mechanisms** stay in the companion
documents as normative text and are never registry entries: selective
disclosure, for example, is a transform that composes with any registered
canonicalization algorithm, so it adds no algorithm entry; countersignature
machinery likewise lives in its companion. A companion that introduces a new
controlled **vocabulary** adds a **new registry here** — same home, same
Specification-Required / PR-as-consent rule, same migration clause — rather than
scattering per-companion registries (e.g. a future Relation Types registry for
record relations: supersedes / confirms / corrects). A companion whose need is
simply a new **artifact type** registers in the existing Artifact Type Registry
(e.g. an erasure tombstone), adding no new structure. Per-companion registry
scattering would break decomposable verification the same way per-profile
invention of these facilities would — one registry home is the structural
guarantee. The home moves **as a unit** through adoption: this repository today →
the working group's repository on adoption → IANA at RFC publication.

**How entries change — PR as consent.** The tables below change **only** by pull
request with the named owner's approval. A canonicalization-algorithm or
artifact-type entry enters the record only once its semantics are pinned in a
publicly available specification and the owner confirms every owner-supplied
field. CPB editors MUST NOT fill in an owner's digest-context parameters on their
behalf. Proposed entries under discussion with their owners are tracked
separately in [`spec/cpb-provisional-registry.md`](spec/cpb-provisional-registry.md)
until confirmed; they enter the tables here on merge.

**Descriptive, not generative.** This file is DESCRIPTIVE of the registries
defined normatively in the Internet-Draft; it never generates new semantics. The
draft (§11) is normative; this file is the living interim record.

[RFC 8126 §4.6]: https://www.rfc-editor.org/rfc/rfc8126#section-4.6

---

## Payload Canonicalization Algorithm Registry

Records the canonicalization algorithms that may be used to compute
CANONICAL-DIGEST values. Registration template: **Name**, **Description**,
**Reference** (draft §11.1).

| Name | Description | Reference | Status |
|---|---|---|---|
| `jcs-n` | RFC 8785 JCS over a normalized JSON object (null, empty-array, and empty-object members removed bottom-up); SHA-256; lowercase hex | draft-mih-sokolov-scitt-payload-binding | Registered |
| `cde-n` | CDE/dCBOR normalization; SHA-256 | draft-mih-sokolov-scitt-payload-binding | **Reserved** (defined in a subsequent revision) |

## Artifact Type Registry

Records the artifact types that may appear in the `type` field of a typed digest
reference. Registration template: **Name**, **Digest Context** (the preimage rule
— field set selected, exclusion set applied — the canonicalization algorithm name
from the Algorithm Registry, and the output representation), **Reference** (draft
§11.2).

| Name | Digest Context | Reference | Status |
|---|---|---|---|
| `agent-action-capsule` | `jcs-n`; exclusion set `{capsule_id, chain}`; 64-char lowercase hex | draft-mih-scitt-agent-action-capsule | Registered — first payload profile |

Proposed Artifact Type entries awaiting their owners' confirmation are listed in
[`spec/cpb-provisional-registry.md`](spec/cpb-provisional-registry.md).


---

## Entry Status Vocabulary

Every registry entry carries a `status` field drawn from the following controlled vocabulary.
These values are the authoritative terms; registrars MUST use them verbatim.

| Status | Meaning |
|---|---|
| `owner-confirmed` | The profile's author or owner approved the entry text (via PR approval, email ack, or equivalent on-record confirmation). Highest-provenance status. |
| `third-party-documented` | Registered by someone other than the owner, from publicly pinned artifacts (spec revision + repo commit). Registrant is named in the entry. Owner has been notified and invited to review. Not yet confirmed by owner. |
| `provisional` | A reference resolves but the vector set is incomplete or the specification is insufficiently pinned. Entry is held in [`spec/cpb-provisional-registry.md`](spec/cpb-provisional-registry.md) until vectors and pinning are complete. |

Statuses are not permanent — see [Entry Lifecycle](#entry-lifecycle) below.

---

## Registration Ladder

Three rungs of provenance, from cleanest to minimum-viable:

**Rung 1 — Owner-authored.**
The profile's owner opens the PR and supplies all fields directly.
The registrar (CPB editor) reviews for completeness and correctness, then merges.
Entry enters the live tables with status `owner-confirmed`.
This is the cleanest provenance and the preferred path.

**Rung 2 — Third-party-documented.**
A third party (not the owner) registers from publicly available artifacts.
The third party MUST satisfy all [Third-Party Registration Rules](#third-party-registration-rules).
Entry enters the live tables with status `third-party-documented`.
Owner is notified by the registrar (via issue or direct contact) and invited to review.
Status upgrades to `owner-confirmed` upon any owner acknowledgment (PR approval or email on record).

**Rung 3 — Provisional.**
A reference exists but the vector set is incomplete, or the specification is insufficiently
pinned to support a complete Digest Context description.
Entry is tracked in [`spec/cpb-provisional-registry.md`](spec/cpb-provisional-registry.md),
not in the live tables, until the missing material lands.
Status is `provisional` until vectors and pinning are complete; then the entry may be
promoted to Rung 1 or Rung 2.

---

## Third-Party Registration Rules

Third-party registration (Rung 2) is permitted when the construction is publicly documented.
A third-party entry MUST:

1. **Pin its sources.** Name the exact specification revision (draft version or RFC number)
   and the repository commit hash from which the entry was derived.
   Example: "registered from `draft-example-foo-01`, commit `abc1234`."

2. **Name the registrant.** Include a self-attestation in the `Registrant` field.
   Example: "Registered by Action State Group from public documentation at
   `draft-example-foo-01` / commit `abc1234`."

3. **Make no conformance or endorsement claims about the owner.**
   The entry MUST NOT imply that the owner endorses this registry, vouches for the
   implementation, or has verified the entry.

4. **Cite only the owner's published vector sets.**
   Registrants MUST NOT fabricate test vectors for someone else's construction.
   If the owner has published no vectors, the entry is `provisional` (Rung 3), not Rung 2.

5. **Acknowledge the standing removal policy.**
   Owner objection removes or amends the entry, no questions asked.
   See [Removal and Correction](#removal-and-correction).

6. **Accept upgrade to `owner-confirmed` on any owner acknowledgment.**
   PR approval, email on record, or any other unambiguous owner ack upgrades the entry.

CPB editors MUST NOT fill in owner-supplied fields (Digest Context, vector references) on the
owner's behalf. If a required field cannot be sourced from public artifacts, the entry is
`provisional`.

---

## How to Register

### Step-by-step

1. **Fork** `action-state-group/scitt-payload-binding` on GitHub.
2. **Fill in the entry template** (see [Entry Template](#entry-template) below) for each
   registry table your entry appears in.
   - Owner-authored entries: fill all fields directly.
   - Third-party entries: fill all fields from public artifacts and complete the `Registrant`
     field with the self-attestation.
   - Provisional entries: file in `spec/cpb-provisional-registry.md`, not in the live tables.
3. **Open a pull request** against `main` on the upstream repository.
   PR title convention: `registry: add <name> to <Registry Name>`.
4. **CI must pass.** The repository CI gate checks structural validity of the registry tables.
   A PR with failing CI will not be merged.
5. **Maintainer review.** A CPB editor reviews for completeness, accuracy, and policy
   compliance. For third-party entries, the editor notifies the owner.
6. **Merge.** On approval, the entry moves into the live tables in `REGISTRY.md`.

### Entry Template

Add one row to the appropriate registry table per entry.
For new entries that are third-party or provisional, also add the `Registrant` column.

**Payload Canonicalization Algorithm Registry — new row:**

```
| `<name>` | <description of normalization algorithm, hash, and output format> | <draft or RFC reference> | `<status>` |
```

**Artifact Type Registry — new row (owner-authored or owner-confirmed):**

```
| `<name>` | `<algorithm>`; exclusion set `{<fields>}`; <output format> | <draft or RFC reference> | `<status>` |
```

**Artifact Type Registry — new row (third-party-documented), with Registrant column:**

```
| `<name>` | `<algorithm>`; exclusion set `{<fields>}`; <output format> | <draft or RFC reference> | `third-party-documented` | Registered by <registrant> from <spec-rev> / commit `<hash>` |
```

**Required fields for all entries:**

| Field | Required | Notes |
|---|---|---|
| Name | Yes | The controlled identifier used in the `type` field or algorithm name. |
| Description / Digest Context | Yes | For algorithms: normalization + hash + output. For artifact types: algorithm, exclusion set, output format. |
| Reference | Yes | Publicly available specification (Internet-Draft or RFC). |
| Status | Yes | One of `owner-confirmed`, `third-party-documented`, `provisional`. |
| Registrant | Third-party only | Self-attestation: "Registered by X from Y at commit Z." |
| Vectors | Third-party/provisional | Link to owner's published vector set, or state "none published — entry is provisional." |

---

## Entry Lifecycle

Entries move through statuses in one direction only (toward higher provenance):

```
provisional  →  third-party-documented  →  owner-confirmed
```

- **`provisional` → `third-party-documented`:** vectors land and source artifacts are
  sufficiently pinned; registrant opens a PR updating the status and moving the entry
  from `spec/cpb-provisional-registry.md` into the live tables.
- **`third-party-documented` → `owner-confirmed`:** owner acknowledges the entry (PR
  approval or email on record); registrar updates the status field and removes the
  `Registrant` self-attestation note (or retains it for provenance, per owner preference).
- **`owner-confirmed`:** terminal state for a live entry. Entries are immutable once
  owner-confirmed (see "Entries are immutable" in the policy header above). If behavior
  changes, a new entry MUST be registered rather than modifying the existing one.

No backward transitions. A `third-party-documented` entry does not revert to `provisional`
if new concerns arise — the registrant opens a correction PR instead (see below).

---

## Removal and Correction

### Owner-requested removal

An entry owner may request removal at any time, for any reason, by opening a pull request
or filing an issue. Removal is unconditional — no justification required.
The registrar will merge a removal PR promptly (within one working day if the request is
clearly from the owner).

Removed entries are not deleted from git history; they are moved to a `## Removed` section
at the bottom of the registry with a removal date and brief note (e.g. "removed at owner
request, 2026-07-28").

### Owner-requested correction

An owner who finds an error in their entry may open a correction PR at any time.
Corrections are an exception to the immutability rule — factual errors (wrong reference,
typo in name, incorrect Digest Context) may be corrected in place.
Behavioral changes (different canonicalization algorithm, different exclusion set) require
a new entry, not a correction.

### Third-party entry corrections

If a third-party entry contains an error, any party (owner, registrant, or CPB editor)
may open a correction PR. The same factual-vs-behavioral distinction applies.

### Upgrade to `owner-confirmed`

Any unambiguous owner acknowledgment — a PR approval, an email to the CPB editors list,
or a public statement by the owner that the entry is correct — upgrades the entry from
`third-party-documented` to `owner-confirmed`. The registrar updates the status field and
notes the acknowledgment (date and form).
