# SGP4 forks

This project tries to create an inventory of SGP4 forks and propose a Simple and Sound Software Engineering Approach to avoid the consequences of bluntly copy-pasting or re-inventing code.

Because the picture that appears is that many developers try to re-invent the warm water: see https://github.com/CelesTrak/fundamentals-of-astrodynamics/discussions/173.

# DRAFT
This document is still WIP

---

But I'm beginning to understand why: the original code was [published](https://celestrak.org/publications/) in a format ([PDF](https://celestrak.org/NORAD/documentation/spacetrk.pdf)) (1988) that was the norm back then but is not easy to ingest in software, so many developers went their way with the paper. The [update](https://celestrak.org/publications/AIAA/2006-6753/) (2006) mentions this flurry of new versions. **Nowadays the "reference" [Vallado's](https://github.com/CelesTrak/fundamentals-of-astrodynamics/) can be ingested by more efficient means than just copy-pasting.**

Bugs and contributions should be discussed there (please read their [CONTRIBUTING](https://github.com/CelesTrak/fundamentals-of-astrodynamics/blob/main/CONTRIBUTING.md)).

To avoid forking off so far that updates in Vallado's require manual labour, a suggested approach is to use [Vendor Branching](../../README.md).

The hard part (that most will not be able to overcome) is to rip out their code and start over from Vallado's. BUT ... not by simply copy-pasting code.

---

Document revisions

2026-11-05 v0.1

---

## A few SGP4 forks that I already found

- these should be relatively safe: https://github.com/CelesTrak/fundamentals-of-astrodynamics/network

## TODO

- https://en.wikipedia.org/wiki/Systems_Tool_Kit (STK)
- https://www.projectpluto.com/sat_code.htm
- https://pypi.org/project/sgp4/ 
	- https://github.com/brandon-rhodes/python-sgp4/discussions/176#discussion-11217270
- https://github.com/dnwrnr/sgp4/
- https://github.com/ssmichael1/satkit

- https://github.com/DigitalArsenal/spacedatastandards.org/issues/33
- https://github.com/orgs/tudat-team/discussions/120#discussioncomment-19352150
- https://github.com/brandon-rhodes/python-sgp4/discussions/176#discussion-11217270
	- https://github.com/karlhillx/python-sgp4
- https://github.com/ssmichael1/satkit
- https://github.com/orgs/tudat-team/discussions/120#discussioncomment-16881205
- https://github.com/tudat-team/tudat-resources/issues/11

- https://ccsds.org/publications/moims/
- https://github.com/DigitalArsenal/spacedatastandards.org
- https://celestrak.org/NORAD/documentation/gp-data-formats.php
- https://www.movingsatellites.com/e_gps.html (probably to be discarded)

- https://github.com/shashwatak/satellite-js
- https://github.com/magnific0/SGP4 (dead, see https://github.com/magnific0/SGP4/issues/1)
- https://github.com/aholinch/sgp4 (dead? https://github.com/aholinch/sgp4/issues/9)
- https://www.projectpluto.com/sat_code.htm and https://github.com/Bill-Gray/sat_code
- https://github.com/DigitalArsenal/spacedatastandards.org/issues/33
- https://github.com/orgs/tudat-team/discussions/120#discussioncomment-19352150
- https://github.com/brandon-rhodes/python-sgp4/ 
	- compare to https://github.com/CelesTrak/fundamentals-of-astrodynamics/tree/main/software/python
	- see https://github.com/brandon-rhodes/python-sgp4/discussions/176#discussion-11217270
- https://github.com/ssmichael1/satkit
- https://github.com/orgs/tudat-team/discussions/120#discussioncomment-16881205 and https://github.com/tudat-team/tudat-resources/issues/11

[This Celestrak publication](https://celestrak.org/publications/AIAA/2006-6753/AIAA-2006-6753.zip) is already a first invitation to go copy-pasting. 
BTW - it is unclear how it relates to the [GitHub version](https://github.com/CelesTrak/fundamentals-of-astrodynamics/tree/main/software). It is different, for sure; so it might be good to make GitHub the reference and remove such outdated documents. In other words, when publishing code, use a VCS (Git is highly recommended...), not a file format.

An interesting approach to avoid the copy-pasta is described [here](https://github.com/axd1967/vendor-branching).

## more related/todo

- upcoming SDS's [OMM schema](https://spacedatastandards.org/#/schemas/OMM)? (although this will be less trivial to deal with : *TODO*)
	- https://github.com/DigitalArsenal/spacedatastandards.org/tree/main/src/
	- https://github.com/DigitalArsenal/spacedatastandards.org/tree/main/dist/OMM
	- https://ccsds.org/publications/moims/
	- OTOH SDS will not go away soon... so maybe no immediate need for panic. but a vendor drop can also highlight *changes* that might have impact elsewhere in Stellarium
	- what works fr now: `git clone --no-checkout --verbose --shallow-submodules --single-branch --filter=blob:none -o da/sds/gh https://github.com/DigitalArsenal/spacedatastandards.org`
	- what about https://github.com/tudat-team ?
		- https://github.com/orgs/tudat-team/discussions/120
	- more links...
		- WASM: https://webassembly.github.io/spec/core/index.html
		- https://github.com/WebAssembly/wasi-sdk
		- https://webassembly.fr/en/
		- https://doc.qt.io/qt-6/wasm.html
		- https://algodaily.com/lessons/compiling-c-to-wasm-14b05d27
		- https://github.com/google/flatbuffers, https://flatbuffers.dev/

