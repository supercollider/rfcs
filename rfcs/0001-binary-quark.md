- Title: Distributing quarks with binary components
- Date proposed: 2019-12-04
- RFC PR: https://github.com/supercollider/rfcs/pull/7

# Summary

Quarks currently do not support C++ components (or other compiled languages), such as UGens, buffer fills, or server side commands. This RFC proposes a means to support these.

# Motivation

Supporting quarks with binary components enable all 3rd party code distribution to be done with quarks, providing quark features such as automated installation and dependency management to binary extensions. It would potentially enable the retiring of the sc3-plugins repository in the future, simplifying extension installation by providing a single method of installation. Done well, a drastic improvement in easily available sounds and tools can be expected, as well as an increase in productivity.


# Specification

Three phases of implementation or proposed in order to focus development on smaller chunks and prove their utility before moving to the next.

## Core phase

*The goal of the core phase is to get a system ready that builds binary quarks for a group of early testers on different Linux systems*

A quark called `binary` is created that can be depended on by quarks that wish to distribute binaries.

The binary quark checks quarks for the presence of a CMakeLists.txt file, and executes a CMake build after installation if they do. Supercollider headers of an appropriate sclang version are downloaded before build as required. It is assumed that the quark maintainer will set any options to default to the value used for distribution.

A proof of concept for an installable quark with a build script: [ExampleBuildQuark](https://github.com/capocasa/ExampleBuildQuark)

## Expansion phase

The build-from-source approach is evaluated in OSX, with the quark packaging system automatically prompting to install a dependency through the App Store, and automatically downloading another. This approach is evaluated by a group of initial testers on OSX. If it is found to be satisfactory, some newish users can also be exposed to the system. If it works well enough, with some help, this approach is decided to be used. If it does not work well enough, OSX binaries the build-approach for OSX is considered failed and binaries will be distributed during the following phase. 

# Binary distribution phase

A binary repository is created, and questions on where to host it who should have control, and who performs builds are answered. 32bit and 64bit binaries are transpiled from Linux and possibly OSX using a boilerplate CMakeLists.txt. If the expansion phase showed that source builds on OSX are a significant usability problem during the Expansion builds, and 64bit (or perhaps also 32bit or fat binaries) OSX binaries are added to Windows ones for binary distrubtion.

# Drawbacks

1. Source distribution is considered to be infeasible by a majority of responders for at least on OSX and Windows, while the RFC author considers it reasonably easy at least on Linux.
2. Using cross platform shell code is very limiting, but that's ok because the sole job of the build script is to call the build system correctly. Seperate build scripts per platform can be used as a plan B.

# Unresolved Questions

- This plan of action attempts to reconcile all points raised in discussion for the end result. Has this been successful, or should further points be addressed?


Preliminary [discussion on the sc-dev mailing list](https://www.listarc.bham.ac.uk/lists/sc-dev/thrd10.html#59665)
Seperate related [discussion on the sc-dev mailing list regarding the sc3-plugins repository](https://www.listarc.bham.ac.uk/lists/sc-dev/msg58832.html)

