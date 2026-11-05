# SGP4 forks

# DRAFT

---

Document revisions

2026-11-05 v0.1

---


scratchpad:

https://github.com/DigitalArsenal/spacedatastandards.org/issues/33
https://github.com/orgs/tudat-team/discussions/120#discussioncomment-19352150
https://github.com/brandon-rhodes/python-sgp4/discussions/176#discussion-11217270
https://github.com/ssmichael1/satkit
https://github.com/orgs/tudat-team/discussions/120#discussioncomment-16881205
https://github.com/tudat-team/tudat-resources/issues/11

https://ccsds.org/publications/moims/
https://github.com/DigitalArsenal/spacedatastandards.org
https://celestrak.org/NORAD/documentation/gp-data-formats.php
https://www.movingsatellites.com/e_gps.html (probably to be discarded)

https://github.com/shashwatak/satellite-js
https://github.com/shashwatak/satellite-js
https://github.com/magnific0/SGP4 (dead, see https://github.com/magnific0/SGP4/issues/1)
https://github.com/aholinch/sgp4 (dead? https://github.com/aholinch/sgp4/issues/9)
https://www.projectpluto.com/sat_code.htm and https://github.com/Bill-Gray/sat_code
https://github.com/DigitalArsenal/spacedatastandards.org/issues/33
https://github.com/orgs/tudat-team/discussions/120#discussioncomment-19352150
https://github.com/brandon-rhodes/python-sgp4/ 
	- compare to https://github.com/CelesTrak/fundamentals-of-astrodynamics/tree/main/software/python
	- see https://github.com/brandon-rhodes/python-sgp4/discussions/176#discussion-11217270
https://github.com/ssmichael1/satkit
https://github.com/orgs/tudat-team/discussions/120#discussioncomment-16881205 and https://github.com/tudat-team/tudat-resources/issues/11


This project tries to create an inventory of SGP4 forks and convince those users to try to not reinvent the wheel...

The reference should be Vallado's https://github.com/CelesTrak/fundamentals-of-astrodynamics/tree/main/software/cpp/SGP4/SGP4

[This Celestrak publication](https://celestrak.org/publications/AIAA/2006-6753/AIAA-2006-6753.zip) is already a first invitation to go copy-pasting. 
BTW - it is unclear how it relates to the GitHub version. In other words, when publishing code, use a VCS (Git is highly recommended...), not a file format.

A interesting approach to avoid copy-pasta is described here: https://github.com/axd1967/vendor-branching

## related

- upcoming SDS's [OMM schema](https://spacedatastandards.org/#/schemas/OMM)? (although this will be less trivial to deal with : *TODO*)
	- https://github.com/DigitalArsenal/spacedatastandards.org/tree/main/src/cpp
	- https://github.com/DigitalArsenal/spacedatastandards.org/tree/main/dist/OMM
	- https://ccsds.org/publications/moims/
	- OTOH SDS will not go away soon... so maybe no immediate need for panic. but a vendor drop can also highlight *changes* that might have impact elsewhere in Stellarium
	- git clone --no-checkout --verbose --shallow-submodules --single-branch --filter=blob:none -o da/sds/gh https://github.com/DigitalArsenal/spacedatastandards.org
	- what about https://github.com/tudat-team ?
		- https://github.com/orgs/tudat-team/discussions/120
	- more links...
		- WASM: https://webassembly.github.io/spec/core/index.html
		- https://github.com/WebAssembly/wasi-sdk
		- https://webassembly.fr/en/
		- https://doc.qt.io/qt-6/wasm.html
		- https://algodaily.com/lessons/compiling-c-to-wasm-14b05d27
		- https://github.com/google/flatbuffers, https://flatbuffers.dev/


## SGP4 forks

- https://www.projectpluto.com/sat_code.htm
- https://pypi.org/project/sgp4/ 
	- https://github.com/brandon-rhodes/python-sgp4/discussions/176#discussion-11217270
- https://github.com/dnwrnr/sgp4/
- https://github.com/ssmichael1/satkit

## Issues

# Discussions

- https://github.com/DigitalArsenal/spacedatastandards.org/issues/33

## BUSY
## TODO
- list as much forks as possible
- discussions, issues


