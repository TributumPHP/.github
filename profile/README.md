
# TributumPHP – foundational design document

## 1. Purpose

**TributumPHP is an open-source initiative aimed at creating a unified fiscal integration platform for PHP across Europe.**

Tributum defines a common operational model that allows developers to:

* Create fiscal documents (invoices, credit notes, summaries, reports).
* Transmit information to tax authorities and exchange networks.
* Receive, validate, transform and process fiscal documents coming from third parties or administrations.
* Track submission results, document states and regulatory responses.

The goal is not to hide national differences, but to provide:

* A **single conceptual service layer**.
* **Consistent interfaces**.
* Extendable **country-specific adapters**.





## 2. Tributum as a service concept

**Tributum is not a single API — it is a service abstraction.**

Tributum defines a **logical service entry point** from which all fiscal operations start, regardless of national implementation details.

Conceptually:

```text
Tributum Service Layer
        |
Operations (create / submit / receive / query / validate)
        |
Standards (EN16931 / UBL / CII / local models)
        |
Country Adapters (Spain / Italy / Germany / France / PEPPOL / ...)
        |
External Authorities & Networks
```



### Tributum service responsibilities

Tributum coordinates **three categories of behavior**:



#### 1. Document lifecycle management

Handles:

* Draft creation.
* Domain validation.
* Format serialization.
* Signature orchestration.

Does NOT:

* Implement government-specific validations.
* Handle transport.



#### 2. Submission orchestration

Controls:

* Payload routing to correct adapter.
* Credential and certificate resolution.
* Transport retries.
* Receipt acknowledgment tracking.



#### 3. Intake and processing

Handles:

* Incoming documents (PEPPOL, SdI, portals).
* Receipt parsing.
* Semantic mapping into internal models.
* State synchronization.





## 3. Core abstraction layers

The Tributum architecture is expressed via **five separated layers**.



### Layer 1 — Fiscal domain model

A neutral, European model based on EN16931 semantics:

* Parties (supplier / buyer / intermediaries).
* Invoice elements.
* Tax rules.
* Monetary totals.
* References and identifiers.

This layer contains:

* Business objects only.
* No country rules.
* No transport logic.
* No XML.




### Layer 2 — Format serialization layer

Purpose:

Transform the domain model into structural payload formats.

Supported families:

* UBL 2.1 EN16931
* CII
* Facturae
* National derivatives



This layer handles:

* Schema mapping.
* Mandatory field derivation.
* Structural validations.





### Layer 3 — Gateway interface layer

Defines a uniform contract for any public authority or network integration.

Gateway responsibilities:

* Authentication.
* Transmission.
* Reception.
* Acknowledgement and error retrieval.
* Status querying.



Typical gateways:

* PEPPOL Access Point
* Spain — FACe / VeriFactu
* Italy — SdI
* France — Chorus Pro
* Germany — XRechnung endpoint




### Layer 4 — Orchestration layer (Tributum)

The core engine.

Responsibilities:

* Choose the correct gateway and serializer for each operation.
* Route payloads.
* Manage errors.
* Manage states.
* Deliver uniform results to clients.



Tributum does not know:

* XML dialect details.
* Country rules directly.

It works purely via:

```text
Serializers + Gateways
```




### Layer 5 — Application interface layer

Public API consumed by developers:

* PHP SDK.
* CLI tooling.
* Framework integrations.

This is where:

* Laravel integrations live.
* Symfony bridges live.
* Console tooling lives.





## 4. Operational flows

Tributum standardizes **three core workflows**.



### Flow 1 — Outbound document submission

```text
Draft → Validate → Serialize → Transmit → Track status
```



### Flow 2 — Inbound document intake

```text
Receive → Parse → Validate → Map to domain → Store or forward
```



### Flow 3 — Compliance reporting

```text
Extract fiscal data → Aggregate → Serialize → Submit reports
```




## 5. Tributum common operations

From the service standpoint, everything reduces to operations of this nature:

| Operation                   |
|  |
| Create document             |
| Validate document           |
| Serialize document          |
| Submit document             |
| Receive document            |
| Query status                |
| Retrieve error messages     |
| Retrieve acknowledgments    |
| Cancel or replace document  |
| Submit compliance summaries |



**Every country adapter must implement only the operations supported by its authority.**

Unsupported operations simply respond with capability errors.




## 6. Extension philosophy

Tributum must be:

* **Additive**: new countries and features do not break older adapters.
* **Composable**: adapters are independent packages.
* **Replaceable**: serializers and gateways can be swapped.

Country integrations and formats are never implemented in core.

They live as:

```
tributumphp/peppol
tributumphp/spain-verifactu
tributumphp/italy-sdi
tributumphp/france-chorus
```



## 7. Development road map

### Phase 1 — Foundation

* Formal specification of:

    * Core domain model.
    * Serializer interfaces.
    * Gateway contracts.
    * Tributum service API.

No implementations yet.




### Phase 2 — Proof of concept

* UBL serializer.
* Minimal PEPPOL gateway stub or emulator.

Goal:

* Demonstrate operational flow.





### Phase 3 — Spain pilot

* Facturae serializer.
* VeriFactu gateway adapter.
* FACe submission.

Goal:

* Validate real-world regulatory complexity.





### Phase 4 — Major EU integrations

* Italy (SdI)
* France (Chorus Pro)
* Germany (XRechnung)




### Phase 5 — Tooling & developer experience

* CLI client.
* Framework extensions.
* Validation tooling.
* Adapter scaffolding.




## 8. Governance model

TributumPHP operates as:

* Open governance OSS project.
* No single vendor ownership.
* RFC-style design proposals reviewed publicly.
* Versioned semantic contracts.


