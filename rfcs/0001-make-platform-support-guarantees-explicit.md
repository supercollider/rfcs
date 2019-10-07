- Title: Make Platform Support Guarantees Explicit
- Date proposed: 2019-10-06
- RFC PR: https://github.com/snappizz/rfcs/pull/1

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

Here is a proposed wording:

> SuperCollider makes a best-effort attempt to provide support for software released in the last N years, including
> operating systems, compilers, and other software required to build the project.
>
> For software released more than N years ago, no support is guaranteed, nor will any additional development effort be
> expended to test or support such software. However, developers will not break compatibility needlessly, and will
> attempt to maintain support if there is no additional cost for doing so.
>
> Support will not be broken in patch releases; only major and minor releases.
>
> A "software release" is considered to be a major, non-patch version of an operating system or other software. For
> instance, macOS 10.12 Sierra was first released September 20, 2016. SuperCollider's support guarantee for macOS
> 10.12 Sierra extends until September 20, 20xx, and not until N years after July 19, 2017 (the first release of macOS
> 10.12.6), nor N years after September 26, 2019 (the most recent release of macOS 10.12.6). Similarly, only major
> releases of MSVC, LLVM CLang, and GCC, and minor releases of CMake are considered under this policy.

Since it is typically much easier to upgrade operating systems than software, I suggest N=3 for operating systems and
N=2 for other software. In any case, I strongly suggest N<=4.

## Documentation

We must also document exactly which versions of software are supported in each release.

## Enforcement

We must also add continuous integration jobs to enforce these guarantees. Note that Travis now supports Windows, so we
may be able to switch to them and add more Windows builds without it heavily affecting our overall CI run time.

# Discussion

Release dates of various software used for SuperCollider, by version:

MacOS 10.10: October 2014
MacOS 10.11: September 2015
MacOS 10.12: September 2016
MacOS 10.13: September 2017
MacOS 10.14: September 2018

Xcode 8.0: September 2016
Xcode 9.0: September 2017
Xcode 10.0: September 2018
Xcode 11.0: September 2019

gcc 6.1: April 2016
gcc 7.1: May 2017
gcc 8.1: May 2018
gcc 9.1: May 2019

clang 4: March 2017
clang 5: September 2017
clang 6: March 2018
clang 7: September 2018
clang 8: March 2019
clang 9: September 2019

MSVC 2015: July 2015
MSVC 2017: March 2017
MSVC 2019: April 2019

Qt 5.7: June 2016
Qt 5.8: January 2017
Qt 5.9: May 2017
Qt 5.10: November 2017
Qt 5.11: May 2018
Qt 5.12: December 2018
Qt 5.13: June 2019

Windows 7: October 2009
Windows 8: October 2012
Windows 8.1: August 2013
Windows 10: July 2015

# Drawbacks

None I can think of at present, everyone benefits from greater transparency and accountability.

# Unresolved Questions

Perhaps N should be chosen per platform. As we've seen, macOS moves much more quickly in breaking compatibility than
Windows or Linuxes, and in fact appears to actively disincentivizes supporting older platforms.

Similarly, perhaps it could also be chosen on the basis of the software's own release schedule and
backwards-compatibility habits.
