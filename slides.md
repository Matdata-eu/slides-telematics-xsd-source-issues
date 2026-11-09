---
theme: default
title: "What the telematics sources get wrong"
info: |
  Issues in the telematics source schemas (TAF / TAP TSI XSDs, Appendix E, B.5, B.10, B.12, B.14, OSDM),
  the patterns behind them, the rules the telematics-ontology pipeline uses to cope,
  and the issues that remain for the schema owners.
highlighter: shiki
lineNumbers: false
drawings:
  persist: false
transition: slide-left
mdc: true
routerMode: hash
layout: cover
class: bn-cover
background: /assets/tracks-photo.jpeg
---

<div class="bn-cover-panel">

<img src="/assets/era-logo-inverted.svg" class="h-16 mb-8" alt="European Union Agency for Railways" />

# What the telematics sources get wrong

## Issues in the TAF / TAP schemas, how the ontology pipeline copes, and what is left for the sector

<div class="mt-6 opacity-90">
Mathias Vanden Auweele<br>
<span class="text-sm">European Union Agency for Railways (ERA)</span><br>
<span class="text-sm">Telematics ontology · status November 2026</span>
</div>

</div>

<style>
.bn-cover-panel h1 { font-size: 2.2rem !important; }
.bn-cover-panel h2 { font-size: 1.1rem !important; }
</style>

<!--
This deck is about the input, not the converter. The telematics part of the ERA ontology is generated from the TSI schemas, so every weakness of those schemas lands in the ontology. I'll show the patterns, what we already do about them in the pipeline, and what only the schema owners can fix.
-->

---

# Why the sources matter

<div class="grid grid-cols-2 gap-6 mt-4">
<div>

The sector chose to **convert the schemas automatically** and keep every message valid against the model, rather than build the ontology by hand.

<div class="card mt-4 text-sm">So the ontology is exactly as good as the schemas, plus what post-processing repairs. A converter translates; it cannot add meaning the schema never had.</div>

<div class="card warn-card mt-3 text-sm">Every repair in post-processing is a decision someone has to review, explain and maintain, and the message conversion has to follow it. A fix in the schema removes it for everyone.</div>

</div>
<div>

<div class="rosetta">

| Source | Format | Converter |
|---|---|---|
| TAF TSI 4.0.0 + Appendix E | XSD | xsd2rdf |
| TAP TSI B.5 (reservation) | XSD, 29 files | xsd2rdf |
| TAP TSI B.10 (PRM assistance) | XSD | xsd2rdf |
| TAP TSI B.12 (barcode) | ASN.1 | asn2rdf |
| TAP TSI B.14 (e-ticket data) | JSON Schema | jsonschema2rdf |
| OSDM online API | OpenAPI | openapi2rdf |
| NeTEx | XSD | xsd2rdf, separate bundle, not harmonised |

</div>

<div class="text-xs muted mt-2">ERA core reference for the merge: v3.3.4.</div>

</div>
</div>

<!--
Quick reminder of the starting point. Industry picked option A: convert as close to the schemas as possible. The price: whatever is ambiguous in the schema is ambiguous in the ontology.
Seven sources, four formats, four converters that share one model. NeTEx is out of scope today: converted and tidied, not harmonised.
-->

---

# The XSD sources in numbers

<div class="rosetta mt-2">

| | TAF 4.0.0 | Appendix E | B.5 | B.10 |
|---|---:|---:|---:|---:|
| named element declarations | 886 | 13 | 681 | 108 |
| … **without** `xs:documentation` | 120 (14 %) | 0 | **488 (72 %)** | **102 (94 %)** |
| enumeration values | 3,066 | 4 | 401 | 110 |
| … **without** their own documentation | 341 (11 %) | 4 (100 %) | **401 (100 %)** | 50 (45 %) |
| element names declared with **different types** | 16 | 0 | 34 | 6 |
| names that differ **only in case** | 3 | 0 | 2 | 0 |

</div>

<div class="grid grid-cols-2 gap-4 mt-4 text-sm">
<div class="card"><b>64</b> element names appear in more than one of these four sources. Each one is a question: same concept, or a coincidence of words?</div>
<div class="card warn-card">The documentation is the only place the meaning lives. Where it is missing, the ontology term has no definition, and nobody can check what a value means.</div>
</div>

<div class="text-xs muted mt-2">Counted on the schemas in <code>xsd-source/</code> of the telematics-ontology repository (TAF incl. its two code-list files).</div>

<!--
Measured directly on the XSDs.
TAF is reasonably documented: 14 percent of the elements have no documentation. B.5 and B.10 are not: three quarters and almost all elements are undocumented, and not one B.5 code has its own explanation.
Names declared with different types: the same element name with a string in one place and a structure or another type elsewhere. 16 in TAF, 34 in B.5.
And 64 names are shared between sources, which is where the merge gets hard.
-->

---

# The worklist: xsd-improvements

<div class="grid grid-cols-4 gap-4 mt-6 text-center">
<div class="ans">
<div class="sub">items on the worklist</div>
<div class="text-3xl font-bold mt-1">36</div>
<div class="sub">plus one umbrella item</div>
</div>
<div class="ans">
<div class="sub">filed in May–June 2025</div>
<div class="text-3xl font-bold mt-1">17</div>
<div class="sub">first conversion</div>
</div>
<div class="ans">
<div class="sub">filed in October 2026</div>
<div class="text-3xl font-bold mt-1">19</div>
<div class="sub">refactor review sessions</div>
</div>
<div class="ans hl">
<div class="sub">fixed in a schema release</div>
<div class="text-3xl font-bold mt-1">0</div>
<div class="sub">one closed on the ontology side</div>
</div>
</div>

<div class="grid grid-cols-6 gap-2 mt-6 text-center text-sm">
<div class="ans"><div class="sub">TAF</div><b class="text-xl">27</b></div>
<div class="ans"><div class="sub">B.5</div><b class="text-xl">7</b></div>
<div class="ans"><div class="sub">B.10</div><b class="text-xl">6</b></div>
<div class="ans"><div class="sub">B.14</div><b class="text-xl">3</b></div>
<div class="ans"><div class="sub">Appendix E</div><b class="text-xl">1</b></div>
<div class="ans"><div class="sub">OSDM</div><b class="text-xl">1</b></div>
</div>

<div class="text-xs muted mt-3 text-center">Items per source; an item that names two sources counts for both. Work items labelled <code>xsd-improvements</code> in the telematics-ontology repository, 9 November 2026.</div>

<!--
Every time the pipeline has to work around the source, we file an xsd-improvements item: what is wrong, what we ask the schema owner, and what the ontology does meanwhile.
36 items. Half from the first conversion in 2025, half from the review sessions of last month. None has been fixed in a schema release yet; the oldest has been open for 18 months.
TAF dominates because it is the biggest and the most reviewed, not necessarily the worst: B.5 and B.10 got fewer items but have far less documentation.
-->

---
clicks: 1
---

# Eight patterns behind the 36 items

<CategoryBars :step="$clicks" />

<!--
I grouped the items by the pattern behind them, not by source.
Code lists come first: meanings written in prose instead of per value, codes in free-text fields, patterns instead of enumerations.
Then the structural ones: one concept modelled several ways, datatypes that disagree, one name with two meanings.
1 And this is what we did: 20 of the 36 are worked around by a pipeline rule. The other 16 are left as the source says: either because the fix is a typo the schema owner should make, or because guessing would be worse, like units nobody documented.
-->

---
layout: section
---

# The patterns

## With the clearest examples

---

# Same name, different meaning: MessageReference

<div class="grid gap-5 mt-3" style="grid-template-columns: 1.35fr 1fr">
<div class="rosetta">

| `MessageReference/…` | TAF 4.0.0 | Appendix E |
|---|---|---|
| `MessageType` | 4-digit code, e.g. `3003` | root element name, e.g. `TrainCompositionMessage` |
| `MessageDateTime` | generated by the common interface | *received* local time of the remote LI |
| `MessageIdentifier`, `MessageTypeVersion` | same | same |

</div>
<div class="text-sm">

Both schemas declare a **global `MessageReference`** with the same four children. Same names, two meanings.

<div class="card mt-3">In the bundle today: <b>one class with 7 properties and 2 node shapes</b> (<code>messageType</code>, <code>messageTypeCode</code>, <code>messageTypeName</code>, <code>messageDateTime</code>, <code>messageReceivedDateTime</code>, …).</div>

<div v-click class="card mt-3">And the Appendix E name <b>is</b> the label of the TAF code: <code>3003</code> = "TrainCompositionMessage". One lookup would make it one property. One mismatch: <code>ChangeofTrackMessage</code> vs <code>ChangeOfTrackMessage</code>.</div>

</div>
</div>

<div class="card warn-card mt-4 text-sm"><b>Ask:</b> Appendix E uses distinct names (e.g. <code>MessageTypeName</code>, <code>MessageReceivedDateTime</code>) or TAF's definitions, so a message reference means one thing everywhere. <span class="muted">#113</span></div>

<!--
The clearest homonym. Appendix E reuses TAF's MessageReference block, but MessageType is a code in TAF and a name in Appendix E, and MessageDateTime is a different event.
The pipeline renamed the Appendix E ones, so the class now carries seven properties and two shapes.
1 And then we found that the Appendix E name equals the label of the TAF code, so the two could become one property with a lookup. That proposal is under discussion. Only the schema owner can make it clean at the source.
-->

---

# Same name, different meaning: more cases

<div class="grid grid-cols-2 gap-4 mt-3 text-sm">
<div class="prob">

### 🌍 `CountryCode`
TAF `LocoTypeNumber/CountryCode` is a **UIC numeric** code (`^[0-9]{2}$`); in `Carriers` it is an **ISO** 2-letter code. TAF also has `CountryCodeISO`. <span class="muted">#116</span>

</div>
<div class="prob">

### 💶 `Amount`
**Euro cent** in B.5, **currency units** in TAF, under the same name. OSDM and B.12 use minor units again. <span class="muted">#109</span>

</div>
<div class="prob">

### 🧭 `Direction`
Two different B.5 code lists are both used as "Direction" and share the codes `D` and `U`. <span class="muted">#112</span>

</div>
<div class="prob">

### 🔀 One name, value and link
After the merge, **57 names** are still a value in one place and a structure in another, so they keep a `DP` / `OP` suffix: <code>amountDP</code> / <code>amountOP</code>, <code>companyDP</code> / <code>companyOP</code>, …

</div>
</div>

<div class="card mt-4 text-sm">The converter takes the <b>name</b> as the identity of the concept. In a schema a name only has to be unique in its context, so every homonym needs a person to split it. The pipeline has <b>13 rules</b> that give such terms their own names (202 term changes).</div>

<!--
Three more homonyms from the worklist, and one number from the bundle: 57 names that are both a plain value and a link somewhere, which the pipeline cannot merge automatically.
Each split is a human decision, written down as a rule.
-->

---

# One concept, several structures

<div class="grid grid-cols-3 gap-3 mt-2 text-sm tight">
<div class="concept">

### 📍 Coordinates, 3 ways
`GeographicalCoordinates` (string, unused), `GeographicCoordinates` (lat/long/alt/SRID) and `Position` in the wagon status (decimals). <span class="muted">#34</span>

</div>
<div class="concept">

### 👥 `Customer` vs `Customers`
Two elements with almost the same children. <span class="muted">#26</span>

</div>
<div class="concept">

### 🏷️ `RID_Class`
Declared twice: once an enumeration, once a plain string, same value list. <span class="muted">#33</span>

</div>
<div class="concept">

### 🚆 The train
B.5 / B.10 identify the train of a stop by `@TrainNb` only; TAF has a full train identification (RU, TRID, OTN, date). <span class="muted">#103</span>

</div>
<div class="concept">

### 📅 Date or date-time?
`AcceptanceDate` is `xs:date` in one TAF message and `xs:dateTime` in another; B.5 `DepartureDate` likewise. <span class="muted">#36 #119</span>

</div>
<div class="concept">

### 🏢 Company code
An **integer** in B.5, a **4-character** code in B.10 and TAF. <span class="muted">#114</span>

</div>
</div>

<div class="card mt-4 text-sm">Also missing: a common type for a <b>message</b>. The pipeline adds <code>era:Message</code> as the superclass of every message root (rule 91). <span class="muted">#40</span></div>

<style>
.tight .concept { padding: 8px 12px; line-height: 1.35; }
.tight .concept h3 { font-size: 1.05rem; margin: 0 0 4px; }
.tight .concept p { margin: 3px 0; }
</style>

<!--
The opposite problem: one concept, several shapes. Coordinates three ways, the train identified two ways, the same date with two datatypes.
The pipeline maps coordinates to one GeoSPARQL point and the B.5/B.10 train to era:Train, but the datatype mismatches stay: we can't pick one without breaking valid messages.
-->

---
clicks: 2
---

# Containers: XML nesting becomes ontology

<WrapperChain :step="$clicks" />

<div v-click="2" class="grid grid-cols-2 gap-4 mt-4 text-sm">
<div class="card"><b>40 classes</b> in the bundle have exactly one property, a link to the next class: <code>ReferenceOTN</code>, <code>CarriersInvolved</code>, <code>AssociatedAttachedTimingAtLocation</code>, <code>PassengerData</code>, …</div>
<div class="card warn-card">Not a converter artefact: the converter is faithful to the nesting. Flattening is possible per case in post-processing, but the cause stays in the schema.</div>
</div>

<!--
This is what people mean when they say the ontology looks like XML. ReferenceOTN wraps OperationalTrainNumberIdentifier, which wraps the number.
1 Each container becomes a class and a link, five terms for one value, and none of the intermediate ones has a definition.
2 There are 40 such single-link classes. Same family: TrainActivityType, which is a class labelled "type" that describes an activity. Each case needs a decision: a pipeline rule, an xsd-improvements item, or both.
-->

---

# Missing definitions

<div class="grid grid-cols-3 gap-4 mt-4 text-center">
<div class="ans">
<div class="sub">classes without a definition</div>
<div class="text-3xl font-bold mt-1">371 / 932</div>
<div class="sub">40 %</div>
</div>
<div class="ans">
<div class="sub">object properties without one</div>
<div class="text-3xl font-bold mt-1">732 / 1,129</div>
<div class="sub">65 %</div>
</div>
<div class="ans">
<div class="sub">datatype properties without one</div>
<div class="text-3xl font-bold mt-1">330 / 1,244</div>
<div class="sub">27 %</div>
</div>
</div>

<div class="grid grid-cols-3 gap-3 mt-5 text-sm">
<div class="concept">

### 🔤 Abbreviations
156 labels contain an all-caps abbreviation, rarely expanded: `ITU` becomes "IT u", `ILU`, `OTN`, `COM`.

</div>
<div class="concept">

### 📝 XML, not meaning
"Moved from passengercomplextypes.xsd"; "ObjectType has to be used to differ between train and path id". 151 comments are under 25 characters.

</div>
<div class="concept">

### ❓ Core classes
`DesignDataSet` (42 wagon properties), `AdministrativeDataSet`, `PathInformation`, `Booking`, `Price`: no definition at all.

</div>
</div>

<div class="text-xs muted mt-3">Bundle built from the pipeline on 7 November 2026, terms in the ERA namespace. 232 terms have more than one English comment, one per source. <span>#153</span></div>

<!--
The documentation numbers from the XSD slide, seen from the ontology. Four in ten classes and two thirds of the object properties have no definition.
Some definitions exist only in sector documentation, like RNE TIS for ROTN. So we will have to write some definitions ourselves, and mark them as such. The order of sources is under discussion: legislation, then sector standards, then our own.
-->

---

# Code lists

<div class="grid grid-cols-2 gap-3 mt-2 text-sm tight">
<div class="concept">

### 📖 Meanings in prose
Code meanings sit in the documentation of the **type**, not on each value. Label and definition are merged in one text, or a label runs over 80 characters. <span class="muted">#30 #79</span>

→ rules move <b>366</b> code meanings onto the concepts and split <b>64</b> long labels

</div>
<div class="concept">

### ✏️ Codes in free text
`CheckingResults` is three patterns, `TrainActivityType` a string, B.5 `LocationSubsidiaryTypeCode` free while B.10 enumerates it. <span class="muted">#29 #49 #115</span>

→ rule 90 turns free-text code fields into concept schemes and links (27 changes)

</div>
<div class="concept">

### 🏷️ Codes without labels
TAF `RunningStatus` 06–09 have no proper label; B.14 defines `KOS` and `PS` twice, with broken encoding. <span class="muted">#107 #146</span>

</div>
<div class="concept">

### 🔁 Same list, many copies
Lists shared between sources or with ERA core arrive as separate schemes with overlapping codes.

→ <b>276</b> schemes and <b>2,546</b> concepts after merging; for <b>572</b> concepts the label is still just the code

</div>
</div>

<style>
.tight .concept { padding: 8px 12px; line-height: 1.35; }
.tight .concept h3 { font-size: 1.05rem; margin: 0 0 4px; }
.tight .concept p { margin: 3px 0; }
</style>

<!--
Code lists are the biggest category and the one where the pipeline does the most. Moving meanings from prose to concepts, splitting labels, turning free text into schemes, merging same-meaning lists.
But 572 concepts still have nothing but their code as label: the schema never says what they mean.
-->

---

# Units, datatypes and misleading names

<div class="grid grid-cols-2 gap-4 mt-3 text-sm">
<div class="prob">

### 📏 No unit
TAF `SeatingDeviceHeight`, `HandBrakeBrakedWeight` ("coding in 404-2"), B.10 `ChangeTime`, OSDM `roofWidth`, B.14 `roofRackHeight`. <span class="muted">#118 #147</span>

The pipeline adds `era:unitOfMeasure` where the unit is documented (rules 93 and 110, 105 changes) and **does not guess** the others.

</div>
<div class="prob">

### ⏱️ A duration that is not one
`InterruptionDuration` holds the earliest and latest **end** date-time, not a length of time. <span class="muted">#120</span>

</div>
<div class="prob">

### 🔢 Booleans and wrong types
`0|1` patterns instead of `xs:boolean`; B.10 `PrmStationDescrType/Station` is typed `ServiceCodeType`. <span class="muted">#28 #108</span>

</div>
<div class="prob">

### ✍️ Wrong text
The height of a loading unit is documented as "Total external width"; typos such as "Not Jet Confirmed", "AddidtionalInformation". <span class="muted">#106 #31 #32 #35 #48</span>

</div>
</div>

<!--
Smaller items, but each one misleads a reader or a tool. A tool that maps names to vocabularies would align InterruptionDuration with a duration, which is wrong.
For units we made a rule: annotate where the schema says the unit, never guess from a UIC leaflet.
-->

---

# Dead weight and lost structure

<div class="grid grid-cols-3 gap-4 mt-4 text-sm">
<div class="concept">

### 🗑️ Unused types
26 named types in B.5 that no B.5 element uses, some clashing with TAF types of the same name (`DimensionValue`, `CustomerCode`, `RunningStatus`). <span class="muted">#105</span>

→ rule 60 keeps <b>396</b> terms out of the bundle

</div>
<div class="concept">

### 👻 Unused elements
`EndLocation`, `GeographicalCoordinates`; OSDM hypermedia links and a deprecated duplicate. <span class="muted">#58 #34</span>

→ <b>90</b> terms dropped by rules 85 and 96

</div>
<div class="concept">

### 🔢 Order lost
`xs:sequence` doesn't say which repeated elements are ordered by meaning. In RDF the order of `PlannedJourneyLocation` is gone. <span class="muted">#92</span>

→ `era:sequenceNumber` added by rules 98 and 109 (26 terms)

</div>
</div>

<div class="card mt-5 text-sm">Not published ≠ not there: the types stay in the schema, so every new version of the pipeline has to keep filtering them out.</div>

<!--
Things that should not be in the ontology at all, and something that is missing from it: the order of repeated elements, which only lives in the position in the XML document.
-->

---
layout: section
---

# How the pipeline copes

## 67 rules and 31 policies

---

# Post-processing in layers

<div class="layers mt-4">
  <div class="layer"><b>10-raw</b><span>converter output, never edited</span></div>
  <div class="layer"><b>20-clean</b><span>source repairs, no ERA knowledge</span><em>12 rules</em></div>
  <div class="layer hl"><b>30-harmonised</b><span>align with ERA core and across sources</span><em>42 rules</em></div>
  <div class="layer"><b>40-bundle</b><span>merge, suffixes, provenance, IRIs</span><em>13 rules</em></div>
  <div class="layer"><b>50-checks</b><span>OWL, SHACL, ERA conflicts, messages</span><em>15 checks</em></div>
  <div class="layer"><b>60-docs</b><span>generated Pages site</span></div>
</div>

<div class="grid grid-cols-3 gap-4 mt-6 text-sm">
<div class="concept">

### One rule, one decision
A SPARQL update or a small script, with a header that names its work items and its policy.

</div>
<div class="concept">

### Counted
Each rule states how many terms it changes per source (`expects: taf=12, b5=0`). The build fails when that changes.

</div>
<div class="concept">

### Traceable
Every rename lands in the OWL, SHACL, SKOS and provenance, and the message conversion follows it.

</div>
</div>

<style>
.layers { display: grid; grid-template-columns: repeat(6, 1fr); gap: 6px; }
.layer { background: var(--c-surface); border: 1px solid var(--c-stroke); border-top: 4px solid var(--c-blue); border-radius: 8px; padding: 8px; text-align: center; font-size: 0.8rem; line-height: 1.25; }
.layer.hl { border-top-color: var(--c-amber); background: var(--c-amber-bg); }
.layer b { display: block; font-size: 0.95rem; margin-bottom: 4px; }
.layer span { color: var(--c-ink-mod); display: block; }
.layer em { display: block; font-style: normal; font-weight: 700; margin-top: 4px; color: var(--c-heading); }
</style>

<!--
The source is never edited: the raw converter output is kept, and every repair is a numbered rule in a layer.
20-clean repairs what is broken in the source itself. 30-harmonised is where the meaning is added. 40-bundle merges the sources.
Each rule says how many terms it should change, so a new schema version that changes the picture breaks the build instead of slipping through.
-->

---

# The rules, by what they repair

<div class="rosetta mt-1 rules">

| Theme | Rules | Terms changed | Examples |
|---|---:|---:|---|
| Repair the source text | 6 | 464 | code meanings onto concepts (366), long labels split (64), broken encoding, wrong Height text |
| Remove dead weight, mark deprecated | 5 | 493 | unused B.5 types (396), OSDM hypermedia links (88), "not used" / "obsolete" → `owl:deprecated` |
| Give homonyms their own names | 13 | 202 | Appendix E message reference, B.10 PRM classes (46), B.5 code-list splits (37), case-only clashes |
| Code lists | 6 | 264 | free text → concept scheme, ERA subsidiary location types, merge same-meaning lists (59) |
| Reference data → RINF IRIs | 8 | 209 | `LocationIdent` points at the RINF location; company codes → RINF organisation (125) |
| ERA core namesakes | 3 | 12 | reuse `era:length`, rename what only shares a name (`era:tokenValue`) |
| Standard patterns | 13 | 354 | `era:Message` (63), OWL-Time periods, units (105), GeoSPARQL points, sequence numbers, ISO codes |
| Merge the bundle (40-bundle) | 13 | mechanical | DP/OP suffix where one kind, unions of domains, shape source segments, ERA concept IRIs, provenance |

</div>

<div class="text-xs muted mt-2">20-clean and 30-harmonised rules on <code>main</code> (88e7c9b); "terms changed" is the sum of each rule's <code>expects</code> counts over all sources.</div>

<style>
.rules table { font-size: 0.74rem; }
</style>

<!--
The 54 rules of 20-clean and 30-harmonised, grouped by what they repair. Together they make about 2,000 counted changes to the generated terms. Most of them are a direct answer to one of the patterns we just saw.
The 40-bundle rules are mechanical: they change thousands of triples, but they don't add meaning.
-->

---
clicks: 2
---

# Where the effort goes

<div class="text-sm muted -mt-2">The pipeline was meant to convert the schemas and fit them into ERA core. Most of the work compensates for the sources.</div>

<div class="effort mt-4">
  <div class="erow" v-for="r in [
    { t: 'Repair the source', d: 'text, code meanings, encoding, unused and deprecated terms', n: 958, r: 12, k: 'src' },
    { t: 'Harmonise what the sources did not', d: 'homonyms split, clashing names given their own terms', n: 202, r: 13, k: 'src' },
    { t: 'Code lists', d: 'free text to schemes, copies merged, ERA lists reused', n: 264, r: 6, k: 'mix' },
    { t: 'Ontology patterns', d: 'era:Message, OWL-Time, units, GeoSPARQL, order, ISO codes', n: 354, r: 13, k: 'mix' },
    { t: 'Fit into ERA core', d: 'RINF IRIs for locations and organisations, ERA core namesakes', n: 220, r: 10, k: 'era' },
  ]" :key="r.t">
    <div class="elbl"><b>{{ r.t }}</b><span>{{ r.d }}</span></div>
    <div class="etrack"><div class="ebar" :class="[r.k, { lit: ($clicks >= 1 && r.k === 'src') || ($clicks >= 2 && r.k === 'era') }]" :style="{ width: (100 * r.n / 958) + '%' }" /><span class="en">{{ r.n }} <small>· {{ r.r }} rules</small></span></div>
  </div>
</div>

<div class="grid grid-cols-2 gap-4 mt-4 text-sm">
<div v-click="1" class="card warn-card"><b>~1,160 of ~2,000</b> counted changes, by 25 of the 54 rules, only repair or harmonise the sources. Code lists and patterns are partly compensation too: coordinates three ways, no order, no units, no common message type.</div>
<div v-click="2" class="card">Only <b>~220 changes</b> (about a tenth) are the ERA core fitting the pipeline was built for. The 40-bundle merge rules (DP/OP suffixes, shape segments, domain unions) exist because the sources reuse names.</div>
</div>

<style>
.effort { display: flex; flex-direction: column; gap: 8px; }
.erow { display: grid; grid-template-columns: 330px 1fr; gap: 14px; align-items: center; }
.elbl { text-align: right; line-height: 1.15; font-size: 14px; }
.elbl span { display: block; font-size: 10.5px; color: var(--c-ink-mod); }
.etrack { display: flex; align-items: center; gap: 8px; }
.ebar { height: 22px; border-radius: 5px; background: var(--c-stroke); transition: background .5s; }
.ebar.lit.src { background: var(--c-amber); }
.ebar.lit.era { background: var(--c-blue); }
.ebar.mix { background: color-mix(in srgb, var(--c-amber) 35%, var(--c-stroke)); }
.en { font-weight: 700; font-size: 14px; white-space: nowrap; }
.en small { font-weight: 400; color: var(--c-ink-mod); }
</style>

<!--
The question behind this slide: what is the pipeline actually doing? It was built to convert the TSI schemas and fit them into ERA core.
These are the 54 rules of 20-clean and 30-harmonised, with the number of term changes each rule declares.
1 The top two rows are pure compensation for the sources: repairing them and harmonising what they never harmonised. More than half of all changes. Code lists and patterns are partly that too.
2 The ERA core fitting, the original job, is about a tenth. And even the mechanical merge rules exist because the sources reuse names for different things.
-->

---

# Every decision is a written policy

<div class="grid grid-cols-3 gap-4 mt-3 text-center">
<div class="ans"><div class="sub">policies</div><div class="text-3xl font-bold mt-1">31</div><div class="sub">24 harmonisation · 5 message conversion · 2 pipeline</div></div>
<div class="ans hl"><div class="sub">accepted</div><div class="text-3xl font-bold mt-1">7</div><div class="sub">work item closed by the ontology owner</div></div>
<div class="ans"><div class="sub">awaiting acceptance</div><div class="text-3xl font-bold mt-1">24</div><div class="sub">implemented, work item still open</div></div>
</div>

<div class="grid grid-cols-2 gap-x-6 mt-4 text-sm pol">
<div>

**Accepted**
- `LocationIdent` stays a reference and points at the RINF location
- Keep the DP/OP suffix until after the merge, drop it only for one kind
- Free-text code fields become a concept scheme
- Every link to ERA core flagged with `era:coreInterface`
- No `owl:FunctionalProperty`; cardinality only in SHACL
- Provenance without PROV-O
- TAF company codes refer to the RINF organisation and its role

</div>
<div>

**Awaiting acceptance (selection)**
- Reuse an ERA core property only when meaning and domain fit
- A reused name is one term unless a reviewed rule splits it
- Reuse, match, merge or split code lists by codes and meaning
- Types no element uses are not published
- Order of repeated elements is `era:sequenceNumber`
- Deprecated source terms stay, archaic, with their replacement
- NeTEx is a separate bundle in its own namespace

</div>
</div>

<style>
.pol ul { margin-top: 2px; } .pol li { margin: 1px 0; line-height: 1.3; }
</style>

<!--
Every rule implements a policy, and a policy is only accepted once its work item is closed by the ontology owner. Seven are accepted, 24 are implemented and waiting.
The policies are generated into the Pages site, with examples and the rules that implement them.
-->

---

# What it buys today

<div class="grid grid-cols-3 gap-4 mt-6 text-center">
<div class="ans">
<div class="sub">ERA conflict checks, non-NeTEx</div>
<div class="text-3xl font-bold mt-1">0 errors</div>
<div class="sub">4 warnings, 12 info (against ERA core v3.3.4)</div>
</div>
<div class="ans">
<div class="sub">example messages that fit the model</div>
<div class="text-3xl font-bold mt-1">98 / 137</div>
<div class="sub">TAF, Appendix E, B.5, B.10 (RINF lookup mocked)</div>
</div>
<div class="ans">
<div class="sub">terms where telematics meets ERA core</div>
<div class="text-3xl font-bold mt-1">75</div>
<div class="sub">flagged <code>era:coreInterface</code></div>
</div>
</div>

<div class="card warn-card mt-6 text-sm">Many of the 39 messages that don't fit are the "structure" examples that show every element empty, plus intentionally invalid test messages. There are <b>no example messages</b> for B.12, B.14 and OSDM yet.</div>

<!--
Numbers from the pipeline run of 8 November. The checks against ERA core are clean of errors. 98 of the 137 example messages convert and validate.
The gap: we can't test B.12, B.14 and OSDM against real messages, because we don't have any.
-->

---
layout: section
---

# What is left

## Known issues to raise with the schema owners

---

# Remaining issues in the sources (1/2)

<div class="rosetta mt-1 left">

| Issue | Source | Ask | Pipeline today |
|---|---|---|---|
| `MessageType` / `MessageDateTime` mean other things in Appendix E | App. E | distinct names, or TAF's definitions | own names; merge proposed |
| `CountryCode` is UIC numeric in one place, ISO elsewhere | TAF | `NumericCountryCode`; `CountryCodeISO` | split |
| `Amount` in euro cent vs currency units | B.5, TAF | one unit, or a distinct name | split |
| Two "Direction" code lists sharing `D` and `U` | B.5 | one list, or distinct names | — |
| Date vs date-time for one element (`AcceptanceDate`, `DepartureDate`) | TAF, B.5 | one datatype | — |
| Company code integer vs 4 characters | B.5 | the TAF company code | RINF organisation |
| Station typed `ServiceCodeType`; `0\|1` instead of boolean | B.10, TAF | correct types | partly |
| Coordinates 3 ways; `Customer` / `Customers`; `RID_Class` twice | TAF | one structure each | partly |
| Train in a B.5 / B.10 stop identified by `@TrainNb` only | B.5, B.10 | reuse the TAF train identification | `era:Train` |

</div>

<style>
.left table { font-size: 0.72rem; }
.left td:nth-child(2), .left td:nth-child(4) { white-space: nowrap; }
</style>

<!--
The list for the sector, part one: meaning and structure. Each row has a work item with the details and a proposal.
-->

---

# Remaining issues in the sources (2/2)

<div class="rosetta mt-1 left">

| Issue | Source | Ask | Pipeline today |
|---|---|---|---|
| Code meanings in the type documentation, not per value | TAF, B.5, B.10 | `xs:documentation` on every enumeration value | moved by a rule |
| Codes in free text or patterns (`CheckingResults`, `TrainActivityType`, B.5 subsidiary type) | TAF, B.5 | enumerations | concept schemes |
| `RunningStatus` 06–09 unlabelled; `KOS` / `PS` defined twice | TAF, B.14 | labels, one definition | labelled; repaired |
| Quantities without a unit | TAF, B.10, B.14, OSDM | state the unit | left without |
| `InterruptionDuration` holds an end window | TAF | a name that says so | kept |
| Containers such as `ReferenceOTN`, `TrainActivityType` | TAF | flatter structures, clear names | kept |
| Order of repeated elements only by position | TAF | explicit sequence number | `era:sequenceNumber` |
| 26 unused B.5 types; unused `EndLocation` | B.5, TAF | remove | filtered |
| Typos and wrong texts | TAF, B.10 | fix | Height fixed |
| No definitions for 1,433 terms; abbreviations not expanded | all | definitions, expanded names | under discussion |
| No example messages | B.12, B.14, OSDM | real or test messages | — |

</div>

<!--
Part two: documentation, code lists and the rest. The definitions row is the biggest in effort: 1,433 terms in the bundle without a definition.
-->

---
layout: center
class: text-center
---

# Seven asks for schema authors

<div class="grid grid-cols-2 gap-x-8 gap-y-2 mt-6 text-left text-sm asks">
<div class="tk">1️⃣ <b>One name, one meaning</b> across all TAF / TAP schemas; a new meaning gets a new name.</div>
<div class="tk">2️⃣ <b>One concept, one structure</b>: reuse shared types (train, company, location, coordinates) instead of copies.</div>
<div class="tk">3️⃣ <b>Document every element and every code value</b>, with abbreviations spelled out.</div>
<div class="tk">4️⃣ <b>Enumerations for codes</b>, not free text or patterns.</div>
<div class="tk">5️⃣ <b>State the unit</b> of every quantity, in the type or the documentation.</div>
<div class="tk">6️⃣ <b>Name things after what they are</b>, not after a container or a role; avoid wrappers with one child.</div>
<div class="tk">7️⃣ <b>Make order explicit</b> where it carries meaning, and remove what nothing uses.</div>
<div class="tk">✅ Each repaired item <b>deletes a pipeline rule</b> and makes the next conversion simpler.</div>
</div>

<style>
.asks .tk { padding: 10px 14px; font-size: 14px; line-height: 1.35; }
</style>

<!--
If the TSI schemas get revised in the change control process, these seven rules would make the next conversion much better.
Every work item has a concrete proposal, ready to hand to the schema owners.
-->

---
layout: center
class: text-center
---

# Questions?

<div class="text-left text-lg leading-relaxed mt-8 inline-block">

Mathias Vanden Auweele · for the European Union Agency for Railways (ERA)

<div class="mt-6 text-base">
xsd2rdf on PyPI: <a href="https://pypi.org/project/xsd2rdf/" class="font-bold">pypi.org/project/xsd2rdf</a><br>
Also on PyPI: <a href="https://pypi.org/project/asn2rdf/">asn2rdf</a> · <a href="https://pypi.org/project/jsonschema2rdf/">jsonschema2rdf</a> · <a href="https://pypi.org/project/openapi2rdf/">openapi2rdf</a><br>
ERA ontology: <a href="https://gitlab.com/era-europa-eu/public/interoperable-data-programme/era-ontology/era-ontology" class="font-bold">gitlab.com/era-europa-eu/…/era-ontology</a>
</div>

</div>

---

# Appendix: the 36 worklist items

<div class="rosetta appx">

| # | Item | Source | Pattern | Rule |
|---|---|---|---|---|
| 26 | `Customer` vs `Customers` | TAF | structures | — |
| 27 | attribute vs simple-type child | TAF | names | — |
| 28 | `0\|1` pattern vs boolean | TAF | datatypes | — |
| 29 | `CheckingResults` patterns → enumeration | TAF | code lists | h90 |
| 30 | annotation on each enumeration value | TAF | code lists | c70, c90 |
| 31 | `AlertMessage` typos | TAF | text | — |
| 32 | `AddidtionalInformation` typo | TAF | text | — |
| 33 | `RID_Class` twice | TAF | structures | h61 |
| 34 | coordinates 3 ways | TAF | structures | c85, h96 |
| 35 | `MessageRoutingID` typo | TAF | text | — |
| 36 | `AcceptanceDate` date vs date-time | TAF | datatypes | — |
| 40 | no common message type (closed) | TAF | structures | h91 |
| 41 | "type of" values not a code list | TAF | code lists | — |
| 48 | "Not Jet Confirmed" | B.10 | text | — |
| 49 | `TrainActivityType` → enumeration | TAF | code lists | h90 |
| 50 | `TrainActivityType` → `…Code` | TAF | names | — |
| 58 | unused `EndLocation` | TAF | dead | — |
| 79 | label and definition merged | TAF | code lists | c10 |

</div>

<style>
.appx table { font-size: 0.62rem; line-height: 1.1; }
.appx th, .appx td { padding: 0.12rem 0.4rem !important; }
</style>

---

# Appendix: the 36 worklist items (cont.)

<div class="rosetta appx">

| # | Item | Source | Pattern | Rule |
|---|---|---|---|---|
| 92 | no explicit order | TAF | dead / lost | h98, h109 |
| 103 | B.5 / B.10 train vs TAF train | B.5, B.10 | structures | b05 |
| 105 | 26 unused B.5 types | B.5 | dead | c60 |
| 106 | Height documented as width | TAF | text | c40 |
| 107 | `RunningStatus` 06–09 unlabelled | TAF | code lists | c40 |
| 108 | `Station` typed `ServiceCodeType` | B.10 | datatypes | h85 |
| 109 | `Amount` cent vs units | B.5, TAF | homonyms | h63, h105 |
| 112 | two "Direction" lists | B.5 | homonyms | — |
| 113 | Appendix E `MessageType` / `MessageDateTime` | App. E, TAF | homonyms | h62 |
| 114 | company code integer vs 4 characters | B.5, B.10, TAF | datatypes | h80 |
| 115 | subsidiary type free in B.5, enumerated in B.10 | B.5, B.10 | code lists | h81 |
| 116 | `CountryCode` UIC vs ISO | TAF | homonyms | h61, h107 |
| 117 | names that differ only in case | TAF, B.14 | names | h104 |
| 118 | quantities without unit | TAF, B.10 | units | — (on purpose) |
| 119 | `DepartureDate` date vs date-time | B.5 | datatypes | — |
| 120 | `InterruptionDuration` is an end window | TAF | names | — |
| 146 | B.14 `KOS` / `PS` twice, broken encoding | B.14 | code lists | c95 |
| 147 | roof width / height without unit | OSDM, B.14 | units | — (on purpose) |

</div>

<style>
.appx table { font-size: 0.62rem; line-height: 1.1; }
.appx th, .appx td { padding: 0.12rem 0.4rem !important; }
</style>

<div class="text-xs muted mt-1">Rules: c = 20-clean, h = 30-harmonised, b = 40-bundle, by number.</div>
