# SGP4 forks

From https://orbitalradar.com/glossary/sgp4:
> SGP4 is **the standard algorithm** that turns a [deprecated, now OMM] TLE into a satellite's predicted position at any moment, modelling Earth's oblateness, atmospheric drag and lunisolar effects. Nearly every tracking app and screening system runs it — with typical kilometre-scale accuracy that degrades as the TLE ages.

See also [Wikipedia](https://en.wikipedia.org/wiki/Simplified_perturbations_models)

This project tries to create an inventory of SGP4 forks and propose a Simple and Sound Software Engineering Approach to avoid the consequences of bluntly copy-pasting or re-inventing code.

Because the picture that appears is that many developers try to re-invent the warm water: see 

	- https://github.com/CelesTrak/fundamentals-of-astrodynamics/discussions/173
	- https://github.com/DigitalArsenal/spacedatastandards.org/issues/5

**As a result, new users are overwhelmed by implementations that try to call themselve a reference.**

Forks are flooding the Internet and each vendor tries to position them as *THE* source. They create a dependency that could be fatal once they disappear. (See the [KIK case](../../README.md).)

I'm beginning to understand why: the original code was [published](https://celestrak.org/publications/) in a format ([PDF](https://celestrak.org/NORAD/documentation/spacetrk.pdf)) (1988) that was the norm back then but is not easy to ingest in software, so many developers went their way with the paper. The [update](https://celestrak.org/publications/AIAA/2006-6753/) (2006) mentions this flurry of new versions. **Nowadays the "reference" [Vallado's](https://github.com/CelesTrak/fundamentals-of-astrodynamics/) can be ingested by more efficient means than just copy-pasting.**

To avoid forking off so far that updates in Vallado's require manual labour, a suggested approach is to use [Vendor Branching](../../README.md). The hard part - should you accept the mission - is to rip out your own code and start over from Vallado's. BUT ... not by simply copy-pasting code.

If you think of writing an SGP propagator, please consider the [VB approach](../../README.md).

Maybe this approach will encourage users to explore the later models (SGP8/SDP8).

Bugs and contributions should be discussed there (please read their [CONTRIBUTING](https://github.com/CelesTrak/fundamentals-of-astrodynamics/blob/main/CONTRIBUTING.md)).

Note that SGP4 users might also have the need to decode OMM (successor to the deprecated TLE) data. SDS provides the routines needed to decode such data, and either the VB approach *or a package manager* should be used to decode that data.

GitHub forks should be safe if they keep merging...

- https://github.com/CelesTrak/fundamentals-of-astrodynamics/network

But they too have to be merged somehow in their own project, and that's where Vendor Branching kicks in.

---

Document revisions

2026-11-07 v0.2

- SDS OMM format

2026-11-05 v0.1

---

## Celestrak: the SGP4 reference

At least for the small fish, I'd say. I cannot imagine [SPICE](https://naif.jpl.nasa.gov/naif/toolkit.html), [STK](https://en.wikipedia.org/wiki/Systems_Tool_Kit), NORAD or USAF even taking the slightest notice of the VB approach...

But even [this Celestrak publication](https://celestrak.org/publications/AIAA/2006-6753/AIAA-2006-6753.zip) is already a first invitation to go copy-pasting. BTW - it is unclear how it relates to the [GitHub version](https://github.com/CelesTrak/fundamentals-of-astrodynamics/tree/main/software). **It is different**, for sure; so maybe Celestrak's GitHub could be made the reference and outdated documents removed. In other words, when publishing code, use a VCS (Git is highly recommended...), not a file format. The various copies should be centralised somehow.

Note that I'm not a scientist and have participated in one single academic paper only (as an associated developer), so I might not fully understand the "paper" community.

## TLE/OMM decoders of SDS

TLE data is a major data source for SGP4.

SDS provide the schema as well as decoders for various formats that replace the legacy TLE format. Therefore, if possible, use the VB approach ratger than bluntly copy-pasting their artefacts.

See also

- https://celestrak.org/NORAD/documentation/gp-data-formats.php
- https://github.com/DigitalArsenal/spacedatastandards.org
- https://ccsds.org/publications/moims/
- https://github.com/DigitalArsenal/spacedatastandards.org/issues/33

- upcoming SDS's [OMM schema](https://spacedatastandards.org/#/schemas/OMM)
	- https://github.com/DigitalArsenal/spacedatastandards.org/tree/main/src/
	- https://github.com/DigitalArsenal/spacedatastandards.org/tree/main/dist/OMM
	- OTOH SDS will not go away soon... so maybe no immediate need for panic. But a vendor drop can also highlight *changes* that might have impact elsewhere in your project
	- what works for now: `git clone --no-checkout --verbose --shallow-submodules --single-branch --filter=blob:none -o da/sds/gh https://github.com/DigitalArsenal/spacedatastandards.org`
	- more links...
		- WASM: https://webassembly.github.io/spec/core/index.html
		- https://github.com/WebAssembly/wasi-sdk
		- https://webassembly.fr/en/
		- https://doc.qt.io/qt-6/wasm.html
		- https://algodaily.com/lessons/compiling-c-to-wasm-14b05d27
		- https://github.com/google/flatbuffers, https://flatbuffers.dev/

## Issues raised in other projects

- https://github.com/tudat-team
	- https://github.com/tudat-team/tudat-resources/issues/11
	- https://github.com/orgs/tudat-team/discussions/120#discussioncomment-16881205
	- https://github.com/orgs/tudat-team/discussions/120#discussioncomment-19352150
- https://www.projectpluto.com/sat_code.htm = https://github.com/Bill-Gray/sat_code
	- see also https://github.com/CelesTrak/fundamentals-of-astrodynamics/discussions/173#discussioncomment-19186803
- https://pypi.org/project/sgp4/ = https://github.com/brandon-rhodes/python-sgp4/ 
	- compare to https://github.com/CelesTrak/fundamentals-of-astrodynamics/tree/main/software/python
	- https://github.com/brandon-rhodes/python-sgp4/discussions/176#discussion-11217270
	- https://github.com/karlhillx/python-sgp4

## Probable

- https://github.com/dnwrnr/sgp4/
- https://github.com/ssmichael1/satkit
- https://github.com/shashwatak/satellite-js

- https://pypi.org/search/?q=sgp4&o=-created&c=Topic+%3A%3A+Scientific%2FEngineering+%3A%3A+Astronomy
- https://pypi.org/search/?q=OMM&o=-created&c=Topic+%3A%3A+Scientific%2FEngineering+%3A%3A+Astronomy
- https://pypi.org/search/?q=satellite&o=-created&c=Topic+%3A%3A+Scientific%2FEngineering+%3A%3A+Astronomy

- [Wikipedia](https://en.wikipedia.org/wiki/Simplified_perturbations_models) also mentions revisions of the models.

## Probably dead... 

- https://www.movingsatellites.com/e_gps.html
- https://github.com/aholinch/sgp4 (dead? https://github.com/aholinch/sgp4/issues/9)
- https://github.com/magnific0/SGP4 (dead, see https://github.com/magnific0/SGP4/issues/1)

