- Title: License RFCs and process under the terms of the CC-BY-SA-4.0
- Date proposed: 2021-02-05
- RFC PR: https://github.com/supercollider/rfcs/pull/14

# Summary

Licensing the submitted RFCs and the process itself ensures re-usability. Not
licensing the RFCs leads to a complex situation with many files being "all
rights reserved" or patented.

# Motivation

For reusability in other projects or when e.g. working with or extending the
content it is important for the content to be licensed under the terms of a
[free and open-source
license](https://en.wikipedia.org/wiki/Comparison_of_free_and_open-source_software_licences).

Depending on country, an unlicensed document may be automatically considered
["all rights reserved"](https://en.wikipedia.org/wiki/All_rights_reserved) or
fall under some form of [patent](https://en.wikipedia.org/wiki/Patent) by its
original author, which complicates further derivative works.

The [Creative Commons licenses](https://creativecommons.org/licenses/) offer a
proven set of licenses for the context of e.g. documentation and artistic
content.

One project licensing their RFCs (and RFC process) is
[NixOS](https://github.com/NixOS/rfcs).

# Specification

From the [Creative Commons license
chooser](https://creativecommons.org/choose/) a license is selected based on
the following:

- The licensor permits others to create and distribute derivitave works, but
  only under the same or a compatible license (aka.
  [copyleft](https://en.wikipedia.org/wiki/Copyleft))
- The licensor permits others to copy, distribute, display and perform the
  work, including for commercial purposes (e.g. a press article).

The resulting license is the [Attribution-ShareAlike
4.0](https://creativecommons.org/licenses/by-sa/4.0/) (CC-BY-SA-4.0), a free
cultural license.

A [LICENSE
file](https://raw.githubusercontent.com/spdx/license-list-data/master/text/CC-BY-SA-4.0.txt)
(containing the license text) is added to the repository and referenced to in
the README.
All previous contributors are queried for their permission to relicense their
work in this repository under the terms of the [CC-BY-SA-4.0
license](https://creativecommons.org/licenses/by-sa/4.0/legalcode).

All future RFCs are licensed under the terms of the CC-BY-SA-4.0.

# Drawbacks

Dispute over "the best" license for this purpose might occur.

# Unresolved Questions

None.
