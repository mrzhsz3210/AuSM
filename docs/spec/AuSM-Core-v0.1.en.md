# AuSM Core Spec v0.1

**Language**: English | [简体中文](AuSM-Core-v0.1.md)  
**Full name**: Autobiographical Snapshot Memory (AuSM)  
**Document type**: Public Technical Draft  
**Status**: Draft; open to discussion and implementation, not a validated cross-platform standard or a research conclusion  
**Original proposer and draft author**: [@mrzhsz3210](https://github.com/mrzhsz3210)  
**First public draft date**: 2026-11-08  
**First reference Profile**: Memory Diary Profile v0.1  
**Machine-readable Schema**: [AuSM-Core-v0.1.schema.json](../../schemas/AuSM-Core-v0.1.schema.json)  

> The AuSM name, core structure, and initial specification were proposed by @mrzhsz3210. This statement records the origin of the approach and draft. It does not claim that snapshots, summarization, first-person narratives, retrieval, reflection, or context injection were invented by this project, and it does not exclude prior or subsequent related work.
>
> The Chinese document is the initial source draft. This English version is intended to be semantically equivalent; subsequent revisions should update both versions together.

## Abstract

AuSM is a context-continuity architecture for LLM-based conversational agents. An agent narrates shared experiences in the first person, preserves the past as traceable autobiographical history, maintains its current understanding as a bounded and rewritable snapshot, and hands off that snapshot together with selected historical material at context boundaries, subject to scope and budget constraints.

AuSM Core defines platform-neutral objects, semantics, invariants, lifecycle rules, an exchange model, and conformance requirements. Implementations may use ZIP, SQLite, PostgreSQL, object storage, or other backends, as well as different APIs, message formats, and model providers.

**This draft pursues portability and verifiability. It does not assume that AuSM is superior to other memory approaches for every task, model, or scale.**

---

## 1. Problem, Goals, and Non-Goals

### 1.1 Problem

The active context of a conversational agent may be interrupted by context limits, session resets, process migration, or model replacement. AuSM addresses the following question:

> How can a completed interaction be transformed into continuity state that a subsequent agent can read, update, question, and trace back to evidence, rather than reducing the interaction to static user fields or indiscriminately reloading the entire history?

### 1.2 Goals

1. Preserve shared experiences rather than only user attributes.
2. Distinguish what was recorded at the time from what is understood now.
3. Allow lossy snapshot updates while retaining a path to historical evidence.
4. Bound the default handoff working set so that historical growth does not automatically imply equal growth in per-turn context.
5. Give explicit user instructions, corrections, confirmations, and rejections distinct semantics.
6. Allow memory data to be exported, imported, and verified across hosts.
7. Make platform independence falsifiable through a second implementation and conformance testing.

### 1.3 Non-Goals

AuSM Core is not intended to be:

- a complete raw-event log or an immutable compliance audit system;
- a general-purpose knowledge base, a dedicated retrieval algorithm, or a replacement for vector databases;
- a highly concurrent transactional database, a multi-primary synchronization protocol, or a CRDT;
- a mechanism for model training, fine-tuning, or internal long-term memory in model parameters;
- proof of AI consciousness, genuine feelings, or an agent's metaphysical identity;
- a truth system that guarantees recall of every possible phrasing or the correctness of every fact;
- an unconditional promise of applicability to every AI platform.

---

## 2. Terminology and Core Invariants

### 2.1 Definitions

| Term | Definition |
| --- | --- |
| **Autobiographical history** | First-person experience records produced by an agent at particular points in time, together with their provenance, timestamps, and scopes; this is the evidence layer for what was recorded at the time. |
| **Snapshot** | A replaceable current view formed at a logical point in time from available history for subsequent handoff; it is neither raw history nor required to be complete. |
| **Consolidation** | The process of turning new experiences or materials into persistent autobiographical history and updating the current snapshot; history must not be silently rewritten because current understanding changes, whereas the snapshot may be replaced. |
| **Handoff** | The process of delivering the current snapshot and budgeted historical material to a subsequent context at a context boundary while retaining on-demand access to evidence. |
| **Evidence tracing** | The process of resolving a snapshot, anchor, retrieval result, or reflection back to specific historical records and sources. |
| **Four dimensions** | Four optional writing guides—experience, observation, self-change, and future considerations—not four mandatory storage fields. |
| **Agent self-report** | A first-person narrative generated by a particular model under a particular persona and history; it is preservable text, not proof of consciousness or genuine feelings. |
| **Explicit instruction** | Content through which a user explicitly asks the agent to remember, not forget, pay attention in the future, or correct itself; it must not be silently discarded merely because the model chooses not to write. |
| **Scope** | The boundary that determines which principals, conversations, or groups may read and write a memory. |
| **Derived index** | A retrieval aid generated from authoritative history that can be deleted and rebuilt; it is not a second source of truth. |
| **Profile** | A concrete AuSM implementation contract for a particular host, backend, or runtime environment. |
| **Migration semantic losslessness** | Preservation of normalized content and references for the declared and actually exported objects after successful validation and import; containers, signatures, and packaging metadata may differ. |

### 2.2 The Four Writing Dimensions

| Dimension | Definition |
| --- | --- |
| **Experience** | What actually happened during the interval, including quotations, times, or events that locate the experience. |
| **Observation** | The agent's understanding of the user, the relationship, and their changes; an observation is not automatically a user fact. |
| **Self-change** | Changes in the agent's narration, judgment, or manner of interaction that it considers worth preserving, treated as self-report. |
| **Future considerations** | Matters that the current agent wants a subsequent agent to preserve, watch, verify, or reconsider; these are not irrevocable system instructions. |

### 2.3 Core Invariants

Every system claiming to implement the complete AuSM Core lifecycle MUST satisfy the following:

1. **History is not overwritten by current understanding.** Consolidation MUST NOT silently rewrite existing historical narratives. Explicit user deletion, controlled migration, and declared retention policies are exceptions.
2. **Snapshots are replaceable.** A new snapshot creates a new revision, and a failed update preserves the previous valid snapshot.
3. **Evidence remains traceable.** Long-term anchors in a snapshot SHOULD resolve to history or source evidence. If they cannot, their uncertain status MUST be explicit.
4. **Handoff is separate from storage.** A context budget only reduces the current working set; it does not directly delete persistent history.
5. **Explicit instructions take precedence.** A model MUST NOT discard an explicit user instruction or correction merely by judging it unimportant or choosing not to write.
6. **Provenance is distinct from truth.** Provenance, integrity, user confirmation, and factual correctness are different properties and MUST NOT be inferred from one another.
7. **Temporal states are distinct.** A record that was valid in the past is not rewritten as false because current state changes, nor may it continue to masquerade as current state without an update.
8. **Scope cannot be bypassed.** Retrieval, handoff, export, and background consolidation MUST NOT violate the applicable access boundary.
9. **Indexes are rebuildable.** Index failure MUST NOT require modification of authoritative history, and model-assisted retrieval MUST NOT generate new evidence.
10. **Self-reports retain their producer.** Agent self-reports MUST retain producer or version information and MUST NOT be presented as user facts.

---

## 3. Host Capability Contract

AuSM does not require AstrBot, QQ, Python, a particular model vendor, a vector database, or local deployment. A host only needs the capabilities required by its declared implementation level.

### 3.1 Capabilities

| ID | Capability | Minimum requirement |
| --- | --- | --- |
| **H1: Event access** | Read ordered conversation events, distinguishing at least roles, content, and principals, with a stable cursor or event identifier | Reading exported chat logs offline is permitted |
| **H2: Time and scope** | Determine event order and assign private, group, or custom scopes to the memory in question | Incomplete time information MUST remain uncertain; exact timestamps MUST NOT be fabricated |
| **H3: Persistence** | Store and reload history, snapshots, and provenance relationships | No particular database or file format is required |
| **H4: Generative consolidation** | Invoke an LLM or equivalent generative mechanism to produce first-person history and current snapshots | Execution may occur offline outside the host |
| **H5: Context handoff** | Inject the working set into a subsequent model request, or allow the user to provide handoff content explicitly to a subsequent agent | Automatic and manual handoff MUST be declared separately |
| **H6: Boundary detection** | Detect resets, model replacement, context exhaustion, or manually designated handoff points | Manual triggering is permitted when automatic detection is unavailable |
| **H7: Access control** | Enforce scope rules for reading, writing, retrieval, handoff, and export | Private and shared boundaries MUST NOT rely solely on prompts |
| **H8: Export and import** | Emit and validate standards-based exchange objects | Host extensions are permitted if core semantics are preserved |

### 3.2 Implementation Levels

| Level | Required capabilities | Permitted claim |
| --- | --- | --- |
| **Exchange-only** | H3, H8 | Can exchange AuSM data; cannot claim a complete automatic lifecycle |
| **Assisted AuSM** | H1–H5, H7, H8; boundaries designated by the user | Can implement manually triggered or manually delivered AuSM |
| **Full AuSM** | H1–H8 | Can implement automatic boundary detection and handoff |

Background schedulers, vector retrieval, model tool calls, multimodal content, encryption, and web interfaces are optional capabilities and MUST NOT be required by Core.

---

## 4. Platform-Neutral Data Model

### 4.1 Exchange Document

The Core exchange root object is `AuSMExchange/0.1`; the accompanying JSON Schema defines its complete structure.

| Object | Required semantics |
| --- | --- |
| `history` | Ordered autobiographical records containing narratives, scopes, recording times, and evidence references |
| `snapshots` | Snapshot revisions containing current text, revision numbers, producers, and supporting history |
| `anchors` | Traceable long-term details with active or superseded status |
| `confirmations` | Pending, confirmed, rejected, or deferred reflection states assigned by a user or authorized principal |
| `extensions` | Namespaced host extensions that MUST NOT change the meaning of core fields |

The last valid revision in `snapshots` is the current snapshot. An implementation may persist only the current snapshot and a bounded revision history, but an export MUST accurately declare what is actually retained.

### 4.2 Identifiers, Time, and Extensions

- IDs MUST be unique within their respective `history`, `snapshots`, `anchors`, and `confirmations` collections. An `evidence.id` MUST be unique within its containing history record. Cross-system references SHOULD use a host namespace or stable URI and MUST NOT rely on array positions.
- Core exchange timestamps use RFC 3339 `date-time` values with a timezone offset. Conversion from naive local time MUST specify the timezone. Unknown precision MUST be represented through an extension rather than fabricated UTC.
- `scope.kind` permits `private`, `group`, and `custom`. A Profile defines custom authorization semantics; Core does not infer them.
- Extension keys SHOULD use a reverse domain or an explicit namespace controlled by their owner. A receiver may ignore an extension it does not understand, but MUST NOT promote it to a core fact.
- Empty arrays, empty strings, and `null` are not interchangeable in core fields; the Schema defines the permitted representation.

### 4.3 Reference Integrity

1. An internal evidence reference has the form `<history_id>#<evidence_id>` and identifies evidence within a history record. `evidence.ref` may additionally contain a host-local locator or external URI, but does not replace the core reference.
2. Every item in `snapshots[].based_on` MUST reference an existing `history[].id`.
3. `snapshots[].anchor_refs` and `confirmation_refs` MUST reference existing IDs in their respective collections.
4. `anchors[].evidence_refs` and `confirmations[].evidence_refs` MUST resolve to at least one evidence item in a history record. That item MAY identify an explicit external source. An unresolvable record MUST NOT be presented as evidence-traced.
5. `snapshots[].previous` MUST reference an earlier revision. The first revision uses `null`, and cycles are forbidden.
6. A `superseded` anchor MUST have a non-null `supersession`; an `active` anchor MUST have `supersession: null`. A `confirmed` confirmation MUST have a non-null confirming principal and timestamp; other states MUST NOT fabricate confirmation metadata.

### 4.4 Physical Details Outside Core

Profiles define directory layouts, database tables, compression, signature formulas, encryption, tokenizers, retrieval algorithms, token estimation, prompts, scheduling, user interfaces, capacity values, and backward-compatibility procedures.

Core only requires that these choices preserve the core invariants and that any interoperability or boundary implications be documented.

---

## 5. Lifecycle

### 5.1 Consolidation

```text
new events + explicit instructions + optional external material
                         ↓
             determine increment and scope
                         ↓
             write / skip / retry / degrade
                         ↓
       append history with provenance and evidence
                         ↓
       create a snapshot revision or keep the old one
```

1. An implementation MUST identify processed increments or explicitly mark possible overlap when its cursor becomes invalid.
2. A model may choose not to write ordinary content, but explicit instructions MUST have an independent preservation path.
3. If history is stored successfully but snapshot generation fails, the history may remain and the previous valid snapshot MUST remain. The implementation MUST NOT claim a global transaction unless its Profile actually provides one.
4. Consolidation may absorb older brief records, but it may only add an explicit absorption relationship; it MUST NOT delete their original narratives.
5. External material MUST retain its source and MUST NOT be silently presented as the agent's direct experience.

### 5.2 Snapshot Updates

- A Profile may update snapshots incrementally, rewrite them completely, or use another declared strategy.
- Every update creates an increasing revision and does not rewrite historical narratives.
- A snapshot may omit details, so a path back to history MUST remain available.
- A new snapshot SHOULD record its model or equivalent producer. Model replacement MUST NOT erase the producer metadata of older snapshots.
- When fact-level reflection requires confirmation, it may enter active confirmations only after approval by an authorized principal.

### 5.3 Semantic Conflicts

| Conflict | Core rule |
| --- | --- |
| Historical state differs from current state | Preserve history as past state and express current state in the snapshot |
| A user corrects an earlier understanding | Preserve the correction, update current understanding, and do not overwrite old history |
| An anchor becomes invalid | Append supersession metadata and exclude it from the default working set |
| External sources disagree | Preserve sources and evidence; ranking does not determine truth |
| Multiple snapshot revisions exist | Use the latest valid revision as current and retain older revisions according to declared policy |

Core does not provide a natural-language truth solver. Unresolved conflicts MAY remain uncertain or require user adjudication.

### 5.4 Handoff

A Profile assigns concrete budgets to the following priority order:

1. the current snapshot;
2. active confirmations and anchors;
3. recent history or lightweight excerpts;
4. older evidence relevant to the current query;
5. full text explicitly requested by the user.

The working set MUST respect scope and budget. By default, the system does not traverse all history and does not turn total history size directly into per-turn token cost. Manual handoff MUST also declare what was actually delivered; data availability MUST NOT be misreported as model exposure.

### 5.5 Retrieval and Derived Indexes

AuSM does not exclude RAG, FTS, vector retrieval, or reranking. Core defines their position in the architecture: **retrieval provides on-demand evidence, while snapshots provide current understanding; neither may impersonate the other.**

Retrieval results MUST originate from authoritative history or explicit external sources. Models may rewrite queries or rank candidates but MUST NOT generate nonexistent evidence. After index deletion, the system MUST be able to rebuild the index from authoritative data or fall back to index-free reading.

### 5.6 Export, Import, and Deletion

- An export manifest MUST declare included and excluded objects and MUST NOT use “all data” ambiguously.
- Import MUST be validated before installation and MUST NOT silently overwrite conflicting existing data.
- Migration semantic losslessness applies only to exported core objects and references, not to omitted runtime state.
- Explicit user deletion is a legitimate exercise of data ownership. Deletion propagation, tombstones, and cross-device replay policy belong to Profiles and MUST be documented.
- Keys, credentials, passwords, and access tokens MUST NOT appear in ordinary exchange documents. If migration is necessary, they require a separate secure channel.

---

## 6. Conformance

### 6.1 Claim Levels

| Claim | Requirement |
| --- | --- |
| **Core-Structural** | Exchange documents pass Schema and reference-integrity validation |
| **Core-Lifecycle** | All applicable C-01 through C-15 tests pass, with reasons published for any inapplicable test |
| **Core-Interchange** | Two different host Profiles read and write the same golden corpus, preserve core semantics, and independently pass Structural and Lifecycle validation |
| **Platform-independent implementation** | At least two different hosts complete Core-Interchange; until then, the accurate claim is “designed for platform independence” |

Two hosts need not have different authors. A second adapter written by the original author tests portability; an independent third-party implementation additionally tests whether the specification is understandable. These forms of evidence are not interchangeable.

### 6.2 Minimum Test Set

| ID | Test | Passing condition |
| --- | --- | --- |
| **C-01** | Schema validation | Valid documents pass; missing required fields, invalid enums, and incorrect types fail |
| **C-02** | Historical append | Existing historical narratives remain unchanged after consolidation |
| **C-03** | Snapshot revision | Revisions increase and prior revisions are retained according to declared policy |
| **C-04** | Snapshot failure | Model or storage failure does not overwrite the previous valid snapshot |
| **C-05** | Explicit instructions | Explicit instructions survive even when the model chooses not to write |
| **C-06** | Evidence references | Snapshot, anchor, and confirmation references resolve without dangling links |
| **C-07** | Temporal updates | Old states retain historical meaning while new revisions express current state |
| **C-08** | Anchor supersession | Superseded records remain stored but leave the default working set |
| **C-09** | Handoff budget | Exceeding the budget reduces only the working set, not authoritative storage |
| **C-10** | Scope isolation | Private and distinct group data do not leak across retrieval, handoff, or export |
| **C-11** | Index rebuild | An index can be rebuilt from authoritative data or bypassed after deletion |
| **C-12** | Migration round trip | Normalized content and references of included objects remain equivalent |
| **C-13** | No fabricated retrieval evidence | Query expansion and reranking do not create evidence outside authoritative history |
| **C-14** | Cross-model handoff | A new model preserves producer metadata and source boundaries when reading old records |
| **C-15** | Extension round trip | Unknown extensions are not misread as core facts and are preserved or removed according to the Profile declaration |

### 6.3 Golden Corpus

Conformance implementations SHOULD publish at least one synthetic corpus covering:

- ordinary cross-session plans;
- explicit user instructions and corrections;
- completed plans and expired states;
- conflicting external sources;
- the distinction between agent self-report and user facts;
- two private scopes and one explicitly shared scope;
- insufficient context budget;
- model A writing and model B taking over;
- unanswerable questions and required abstention;
- export, import, and repeated import.

A golden corpus MUST NOT depend on real user privacy or contain only cases that are easy for AuSM.

---

## 7. Claims, Boundaries, and Evidence Status

### 7.1 Contracts Provided by Core

- Distinct autobiographical history, current snapshots, anchors, and confirmation states;
- Verifiable history invariants, scope rules, evidence tracing, and migration rules;
- An exportable, platform-neutral exchange model;
- A portability objective that can be tested through multi-host conformance;
- A system design that can be evaluated against baselines under identical models and budgets.

### 7.2 Non-Guarantees

- No guarantee of lossless transformation from raw conversation to autobiographical history;
- No guarantee that a snapshot contains every important detail;
- No guarantee of factual correctness, complete semantic recall, or reproducible model output;
- No guarantee that every natural-language conflict can be resolved automatically;
- No guarantee of unbounded scale, arbitrary concurrency, or cross-device multi-primary synchronization;
- No guarantee of long-term memory inside model parameters;
- No guarantee that agent self-reports correspond to genuine feelings;
- No guarantee that AuSM outperforms summarization, profiles, or RAG on every continuity task;
- No guarantee that one implementation demonstrates universal platform support.

### 7.3 Current Evidence Status

As of the draft date:

- Memory Diary Profile provides a real AstrBot reference implementation;
- Core-Structural has an independent Schema draft;
- A cross-host golden corpus and automated Core-Interchange tests remain to be completed;
- A second host and an independent third-party implementation remain to be completed;
- Public research evidence from matched-budget baselines, ablations, scale tests, and longitudinal blind evaluation has not yet been produced.

The accurate current statement is therefore:

> **AuSM has a platform-neutral Core specification draft and one reference implementation, and is progressing toward multi-host interoperability and matched-budget empirical evaluation.**

It MUST NOT be described as having already proven universal platform applicability or superiority over existing memory systems.

---

## 8. Versioning, Citation, and Discussion

### 8.1 Versioning Rules

- Changes to Core object semantics, invariants, exchange fields, or compatibility rules MUST update the specification version and document migration behavior.
- Profiles may evolve independently but MUST NOT promote host parameters into Core requirements.
- Conflicts between code and specification MUST be recorded publicly; the more favorable marketing interpretation MUST NOT be selected silently.
- Every normative guarantee MUST map to a test; unverified matters remain research questions.

### 8.2 Suggested Citation

```text
mrzhsz3210. Autobiographical Snapshot Memory (AuSM) Core Spec v0.1:
A platform-independent draft for autobiographical history, mutable snapshots,
and evidence-linked context handoff. 2026-11-08.
```

A formal citation SHOULD include a permalink to this document at a specific repository commit so that readers can verify its version and date.

### 8.3 Discussion

Issues and pull requests are welcome for terminology, object models, Profiles, interoperability tests, negative results, and alternative approaches. Claims of comparative advantage SHOULD include reproducible evidence; recording the origin of the AuSM name does not replace a related-work review.

---

**One-sentence definition**: AuSM preserves shared experiences as autobiographical history, expresses current understanding through rewritable snapshots, and maintains cross-context continuity through evidence tracing and bounded handoff.

**License**: Unless otherwise stated, all original content in this repository—including the prose specification, JSON Schema, examples, and current or future validation code—is licensed under CC BY 4.0. See the repository LICENSE file.
