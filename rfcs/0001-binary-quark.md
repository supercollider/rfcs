- Title: Distributing quarks with binary components
- Date proposed: 2019-12-04
- RFC PR: https://github.com/supercollider/rfcs/pull/7

# Summary

Quarks currently do not support UGens, buffer fills, server side commands, which requires binary artifacts to be loaded by the scsynth or supernova servers. This RFC proposes to permit.

# Motivation

Supporting quarks with binary components enable all 3rd party code distribution to be done with quarks, providing quark features such as automated installation and dependency management to binary extensions. It would potentially enable the retiring of the sc3-plugins repository in the future, simplifying extension installation by providing a single method of installation. Done well, a drastic improvement in easily available sounds and tools can be expected, as well as an increase in productivity.


# Specification

A boilerplate example quark with cmake and travis scripts is provided and documented on the "Writing Quarks" guide.

The boilerplate allows building quarks that have no special build requirements with minimal editing. All platforms that are supported by SuperCollider are enabled by default. Plugin authors are encouraged to build for all platforms unless the plugin is inherently platform specific. Platforms are specified in a configuration section at the top of the build script. Supernova builds are also enabled by default and can be manually disabled.

A travis CI environment is activated through github to automatically build on push. If the push was a tag with a semantic version number, a formal github release containing the builds is automatically created using the github API.

The Quark system checks for the presence of releases on install, downloads the appropriate release for the system, and places it within a folder in the app support directory alongside downloaded quarks, with the platform and intended scsynth/supernova version number as subdirectories. Then the directory symlinked (if supported) or copied into a "active-quarks" directory inside the extensions directory so they are picked up by servers on boot. When quark directories are modified, symlinks must be deleted and created as appropriate.

On executing an quark update, missing binary components are added. This can happen when quarks are transferred to a different platform, or sc is up/downgraded

## Requirements

- Quarks with binary artifacts must be no more difficult to install than those without- ideally, users should not be required to know the difference
- Especially, they must be installable on machines with no root access
- They must be supported on all platforms where binary SuperCollider releases are provided, except where the nature of the quark precludes it
- The build system must have as few dependencies as possible in addition to CMake
- Impact on non-sclang quark systems must be as small as possible

## Out of scope 

The scope of this RFC is limited to distributing binary artifacts, and the minimal changes to the current quarks system required for that purpose. Changes that go further are out of scope, including: 

- Improving dependency management
- Improving quarks.txt loading behvaior
- Directory-specific quark loading
- Security improvements such as parsing the .quark file instead of executing it
- Reducing dendencies, e.g. dropping dependency on git
- Non-github repositories

## Deferred

These remain within scope for discussion, but will likely not be part of an initial release.

- Assisted test tracking- developers are encouraged to test as many built platforms as possible and mark them tested, while others also may mark platforms tested. The Quark installer then displays whether a quark has been found working on the current system.
- Assisted error reporting- errors should be reported via github issues
- Automated source build to assist developers and users of embedded systems or exotic hardware
- Support for languages other than C++ such as Faust, Nim, Rust - at release they will be only supported indirectly via intermediate C++ if applicable

## Rejected

These ideas have been considered and rejected

- Automated source build as primary method of distribution, because it is impossible for users without root access
- Manual binary build by quark developers. It is possible with good tagging a la Pure Data's Deken, but too much burden on developers and bad security practice
- Depending on platform specific package managers for library dependencies. Installing these package managers usually requires root access

# Drawbacks

- The automatic build system requires maintenance
- Quark update is required after upgrading or switching platform or supercollider version
- Manual build required for unsupported systems.

# Unresolved Questions

- Should the binary quark system be implemented as part of supercollider proper, or be installable as a quark of its own that is depended on by quarks that require it?
  - If part of supercollider, should servers be modified to load binary artifacts from the quark folders instead of 
- Do administrators of systems without root access, such as at universities, prohibit installing binary artifacts as a non-root user?

---
Initial discussion on the sc-dev mailing list:

Preliminary [discussion on the sc-dev mailing list](https://www.listarc.bham.ac.uk/lists/sc-dev/thrd10.html#59665)
Seperate related [discussion on the sc-dev mailing list regarding the sc3-plugins repository](https://www.listarc.bham.ac.uk/lists/sc-dev/msg58832.html)

