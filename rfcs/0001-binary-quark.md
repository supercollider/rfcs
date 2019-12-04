- Title: Distributing with binary components
- Date proposed: 2019-12-04
- RFC PR: https://github.com/supercollider/rfcs/pull/7

# Summary

Quarks currently do not support C++ components (or other compiled languages), such as UGens, buffer fills, or server side commands. This RFC proposes a standardized method for this to work.

# Motivation

Supporting C++ components would enable all 3rd party code distribution to be done with quarks, providing quark features such as automated installation and dependency management to binary extensions. It would potentially enable the retiring of the sc3-plugins repository in the future, simplifying extension installation by providing a single method of installation. Done well, a drastic improvement in easily available sounds and tools can be expected, as well as an increase in productivity.


# Specification

A cross-platform shell script called quark.build is added to quarks with binary extensions, in the canonical case calling cmake and make. ¹ 

Quark installation checks for the presences of this file. If it is present, is is executed after installation, displaying the results in the terminal, and noting success if the return code is 0, failure otherwise.

Building requires the supercollider headers, so on first build the SC code is checked out locally with the version executing the installation. This location, along with platform information, is provided to the build script as environment variables.

A working proof of concept for an installable quark with a build script: [ExampleBuildQuark](https://github.com/capocasa/ExampleBuildQuark)

# Drawbacks

1. Source distribution is considered to be infeasible by a majority of responders for at least on OSX and Windows, while the RFC author considers it reasonably easy at least on Linux.
2. Using cross platform shell code is very limiting, but that's ok because the sole job of the build script is to call the build system correctly. Seperate build scripts per platform can be used as a plan B.

# Unresolved Questions

- Shall binary quarks be distributed in binary form?

  - If yes, does the cross-platform build process happen on a CI system?
    - If yes, do we have a volunteer to maintain such as system? 
    - If yes, Shall other build options than C++ be supported, such as Rust or Nim, perhaps deferred?
    - If yes, can we create builds for each common type of executable on Linux? Different binary formats and 32/64 bit add up and require detection
    - If no, how is transpiling set up on each possible OS for a quark maintainer's system? Or should each quark be expected to be built by volunteers on all three systems?
  - If yes, should source distribution be used as a fallback mechanism?
  - If no, how is it ensured that installing build requirements is reasonably easy on OSX and Windows? To answer this question, other systems such as the Ruby Gem 'Nokogiri' should be investigated.

Preliminary [discussion on the sc-dev mailing list](https://www.listarc.bham.ac.uk/lists/sc-dev/thrd10.html#59665)
Seperate related [discussion on the sc-dev mailing list regarding the sc3-plugins repository](https://www.listarc.bham.ac.uk/lists/sc-dev/msg58832.html)

---
¹ The initial draft used a `build.scd` in sclang as a build script, but some users may wish not to depend on sclang, and cross platform shell needs to be used anyway via systemCmd or similar, so there is no gain. 

