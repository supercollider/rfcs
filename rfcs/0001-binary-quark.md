- Title: Distributing quarks with binary components
- Date proposed: 2019-12-04
- RFC PR: https://github.com/supercollider/rfcs/pull/7

# Summary

Quarks currently do not support C++ components (or other compiled languages), such as UGens, buffer fills, or server side commands. This RFC proposes a means to support these.

# Motivation

Supporting quarks with binary components enable all 3rd party code distribution to be done with quarks, providing quark features such as automated installation and dependency management to binary extensions. It would potentially enable the retiring of the sc3-plugins repository in the future, simplifying extension installation by providing a single method of installation. Done well, a drastic improvement in easily available sounds and tools can be expected, as well as an increase in productivity.


# Specification

Three phases of implementation or proposed in order to focus development on smaller chunks and prove their utility before moving to the next.

## 1. Core phase

*The goal of the core phase is to get a system ready that builds binary quarks for a group of early testers on different Linux systems*

A quark called `binary` is created that can be depended on by quarks that wish to distribute binaries.

The binary quark checks quarks for the presence of a CMakeLists.txt file, and executes a CMake build after installation if they do. Supercollider headers of an appropriate sclang version are downloaded before build as required. It is assumed that the quark maintainer will set any options to default to the value used for distribution.

A proof of concept for an installable quark with a build script: [ExampleBuildQuark](https://github.com/capocasa/ExampleBuildQuark)

## 2. Expansion phase

The build-from-source approach is evaluated in OSX, with the quark packaging system automatically prompting to install a dependency through the App Store, and automatically downloading another. This approach is evaluated by a group of initial testers on OSX. If it is found to be satisfactory, some newish users can also be exposed to the system. If it works well enough, with some help, this approach is decided to be used. If it does not work well enough, OSX binaries the build-approach for OSX is considered failed and binaries will be distributed during the following phase. 

# 3. Binary distribution phase

A binary repository is created, and questions on where to host it who should have control, and who performs builds are answered. 32bit and 64bit binaries are transpiled from Linux and possibly OSX using a boilerplate CMakeLists.txt. If the expansion phase showed that source builds on OSX are a significant usability problem during the Expansion builds, and 64bit (or perhaps also 32bit or fat binaries) OSX binaries are added to Windows ones for binary distrubtion.

## Nitty gritty details

Some details were omitted from above for easier understanding:

- The "secret sauce" from pure data's Deken that allows binary extensions to be distributed effectively is an automation of tagging the binary for its architecture, OS version, and exeutable format, in great detail (e.g. 10+ architecures). This proposal suggests to at attempt source distribution so CMake can do that. If source distribution doesn't pan out, then this should be ported to sclang from the pure data's lisp version, which is obviously much harder to get right than letting cmake handle it.

- Some improvements to quarks were suggested, such as dependencies by version and OS, as well as sclang-version-specific quark installations. These will be done in phase 1 as needed.

- Preparation for building from source on Linux involves installing a build package and cmake. On OSX, it involves using a terminal command to trigger installation of the xcode-command-line-tools package in the app store, as well as installing binary cmake. The latter can probably be automated by the quark system and placed outside the path so it doesn't conflict with anything on the machine, such as from homebrew, an OSX package system.

- Since Nokogiri gave up on source builds on Windows, this proposal will too. It is still unclear how exactly a binary repository would work, and who would control it. It is also unclear who will do the builds- the package maintainer, another volunteer, or a CI system. The latter would be preferred by the RFC, if a volunteer can be found to maintain the CI system for windows builds. Github integration would be great. If builds are done by the package maintainer, github release would work. If it is to be done by a third party, then it is unclear where it could be efficiently uploaded.

- Some kind of "confirmed working on OSX/Windows/Linux" feature would be nice, since package maintainers are unlikely to be able to test on three platforms. That would make it more obvious how well packages are tested. This would have to happen *after* phase 3 though.

# Drawbacks

This approach defers some questions to a later phase, however that seems hard to avoid given the scope is so large.


# Unresolved Questions

- This plan of action attempts to reconcile all points raised in discussion for the end result. Has this been successful, or should further points be addressed?


Preliminary [discussion on the sc-dev mailing list](https://www.listarc.bham.ac.uk/lists/sc-dev/thrd10.html#59665)
Seperate related [discussion on the sc-dev mailing list regarding the sc3-plugins repository](https://www.listarc.bham.ac.uk/lists/sc-dev/msg58832.html)

