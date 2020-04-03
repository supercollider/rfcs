- Title: Make Platform Support Guarantees Explicit
- Date proposed: 2019-10-06
- RFC PR: https://github.com/supercollider/rfcs/pull/1

# Summary

We should decide on the kind of support we will guarantee for various platforms and compilers. These guarantees should
be documented and enforced through continuous integration.

# Motivation

This was previously touched on in https://github.com/supercollider/supercollider/issues/2143, "specify minimum build
requirements for each platform, scsynth/supernova". Much of what I'm writing/proposing here is the same.

A project like SuperCollider has two opposing goals: provide support for as many systems as possible, and allow for
efficient development and support of new versions of operating systems and compilers. At a certain point, it is not
worthwhile for developers to expend effort supporting out-of-date systems or compilers, especially when they prevent
new features or bug fixes from being implemented.

In addition, documenting the guaranteed support of a project results in fewer suprises for users and new contributors,
and reduces the need to continually discuss whether or not to break support when the desire arises. Having a
documented policy arround such support allows breaking changes to happen with less controversy, by providing a sense
of legitimacy and forethought.

# Specification

## Support guarantees

The development community for SuperCollider has agreed on some guarantees about support for various platforms and
toolchains. Anything that breaks these guarantees should be fixed as soon as possible.

These guarantees are given as both a number of years and number of releases. Whichever period is longer applies. For
instance, macOS is supported for 2 years and 2 minor releases. That means that any new minor version of macOS
released in the last 2 years is supported, _and_ that the last two minor versions of macOS are always supported. At
present (2020-04-02) that would be macOS 10.14 Mojave and 10.15 Catalina.

These guarantees are for the official releases published on GitHub and the supercollider.github.io website
(pre-compiled binaries on macOS and Windows, and a built-from-source binary on Linuxes).

### Platforms

The following platforms are supported:
- macOS: 2 years, 2 minor releases
- Windows: 4 years, 4 releases
- Ubuntu: 4 years, 2 LTS releases
- Debian: 4 years, 2 major releases
- Fedora: latest release
- Arch Linux: latest release
- Raspberry Pi: latest release
- BeagleBone Black: latest release

"Compatibility" on Linuxes means there exists some toolchain easily obtainable -- preferably through the standard
package manager of that plaform, except when cross-compiling -- that can build SuperCollider.

### Toolchains

SuperCollider also makes some guarantees about the toolchains (libraries, compilers, and other third-party software)
that can be used to compile it.

The official minor release of SuperCollider is guaranteed to be compatible with:
- Xcode: 2 years, 2 major releases
- gcc: 4 years, 4 major releases
- clang: 4 years, 4 major releases
- MSVC: 4 years, 2 major releases
- Qt: 2 years, 1 LTS release

As much as possible, SuperCollider should support building with the latest release of Boost, and the version packaged
in the source tree should be updated reasonably soon after a release.

Other libraries and tools which SuperCollider uses:
- CMake
- libsndfile
- libjack
- fftw
- git
- ALSA
- libudev
- libreadline
- Asio SDK
- NSIS
- libyaml-cpp

These are not given guarantees either because:
- a compatible version is included in the SuperCollider repository itself, or
- the library is relatively stable and so no additional compatibility requirements are necessary, or
- there have been few compatibility issues with the library.

Support guarantees can be added if needed.

### Patch releases

To the extent that it is possible and practical, the supported platforms and toolchains for a minor SuperCollider
release should also be supported by patch releases within that same minor release. For example, if SuperCollider
3.11.0 supports Debian Buster, so should 3.11.1, 3.11.2, and so on.

## Documentation

When documenting these guarantees, it is important to note four distinct levels of support:
1. guaranteed supported, CI-checked platforms and toolchains
2. guaranteed supported, non-CI-checked platforms and toolchains
3. not guaranteed supported, known-to-work platforms and toolchains
4. not guaranteed supported, not known-to-work platforms and toolchains

The following information should be clearly documented:
- these platform and toolchain guarantees themselves
- (1-3) from the above list
- any support that is broken between releases
- the platforms supported by release artifacts

Support guarantees will be documented in the following places:
- SuperCollider website download page
- README
- GitHub release notes
- markdown release notes (CHANGELOG.md)
- schelp release notes ("New in Version 3.x")

## Enforcement

These support guarantees should be enforced through a robust set of CI jobs, and through encouraging and being
responsive to user testing.

# Implementation

Implementation of this RFC consists of the following:
- adding CI jobs as outlined in "Enforcement" above
- adopting documentation as outlined in "Documentation" above
- notfiying the development and user communities of these guarantees

# Drawbacks

None I can think of at present, everyone benefits from greater transparency and accountability.

# TODO

- What to document where?

# Reference release dates and CI info

For reference, the supported versions of the toolchains and platforms listed above for today's date (2020-03-31)
would be:

- macOS: 10.14 (2018-09)
- Windows: Windows 10 Version 1607 (2016-08)
- Debian/Raspbian: 9.0 Stretch (2017-06)
- Ubuntu: Xenial 16.04 LTS (2016-04)
- Fedora: Fedora 31 (2019-10)
- Arch: [rolling release]

- Xcode: 10 (2018-06) - now on 11
- MSVC: 2017 (2017-03) - now on 2019
- gcc: 6 (2017-04) - now on 9
- clang: 4 (2017-03) - now on 10
- Qt: 5.11 (2018-05) - now on 5.14

For reference, the platforms currently (2020-03-31) available in our CI. I use the MSVC version numbers since Windows
is pretty great about backward compatibility:
- Travis Ubuntu: 12.04 to 18.04
- Travis macOS: 10.10 to 10.14 (Xcode 6 to 11)
- Appveyor MSVC: 2013 to 2019

And gcc/clang toolchains are pretty easily available. So it should be possible to support these guarantees with CI,
with the exception of Catalina in Travis (hopefully resolved soon) and other Linux distributions.
