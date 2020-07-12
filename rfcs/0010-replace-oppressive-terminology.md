- Title: Replace oppressive terminology with more accurate alternatives
- Date proposed: 2020-06-15
- RFC PR: https://github.com/supercollider/rfcs/pull/0010

# Summary

Replace oppressive terminology with more accurate alternatives across the SuperCollider organization. In particular,
switch all 'master' branches to 'main', avoid use of "master/slave", "blacklist/whitelist", and use gender-neutral language.

# Motivation

According to the [Code of Conduct](https://github.com/supercollider/supercollider/blob/develop/CODE_OF_CONDUCT.md)
governing the SuperCollider project:

> A primary goal of our community, our user groups and mailing lists, symposia and other events is to be inclusive to the largest number of contributors, with the most varied and diverse backgrounds possible. As such, we are committed to providing a friendly, safe and welcoming environment for all.

To that end, we should, when possible and practicable, replace usage of oppressive and exclusive language with inclusive
and accurate alternatives. This ensures that for a diverse set of new users, SuperCollider will make them feel welcome
when reading the documentation and source code.

A full motiviation and argumentation for this change, along with examples of precedent, can be found in the [Internet
Engineering Task Force](https://tools.ietf.org/id/draft-knodel-terminology-00.html) RFC on the same topic. This RFC
itself should be regarded as an effective "resubmission" of the IETF RFC for the SuperCollider community.

# Specification

We should:
- **Change the 'master' branches of repositories 'master' to 'main'**, as shown in https://www.hanselman.com/blog/EasilyRenameYourGitDefaultBranchFromMasterToMain.aspx.
- Prevent new commits from being pushed to 'master' branches of repositories.
- Keep inactive 'master' branches on repos where they currently exist, for historical purposes.
- For new repos in the SuperCollider organization which are not being forked, name the default branch 'main' and do not
  create a 'master' branch.
- Replace terms using 'master' with 'main', for example 'master volume' to 'main volume'.
- Replace the oppressive terms “master-slave” with more accurate alternatives:
    - primary-secondary
    - conductor-follower
    - active-standby
    - primary-replica
    - writer-reader
    - parent-helper
    - coordinator-worker
- Replace the oppressive terms “blacklist-whitelist” with more accurate alternatives.
    - blocklist-allowlist
    - block-permit
- When referencing other documents or specifications that have not replaced oppressive technology, provide another term
  with a note that their term is original and not being suggested by us
- Use the neuter “they” as the singular pronoun and
- Reflect on our use of metaphors generally.

These changes should be made across all SuperCollider organization (github.com/supercollider) repositories and documentation.

These changes do not impact repositorities in the supercollider-quarks organization. If or when such changes are to be
made, they will be addressed by a new RFC.

## Impact

Luckily, these terms see very limited use within our repositories and code already; the change would not be difficult,
and I would estimate it can be done by a few people working over a few days.

Here is a complete list of documents in supercollider/supercollider which contain 'slave':
- HelpSource/Classes/SyncSaw.schelp
- examples/demonstrations/stealthissound.scd

'blacklist':
- none, but there is one occurrence in supercollider/hidapi

'whitelist':
- none

For inappropriately gendered language:

- HelpSource/Overviews/Operators.schelp and others refer to a function as a "poor man's gaussian".

Probably other language worth changing could be found with more thorough searching.

# Drawbacks

Aside from the humanpower required to make such changes, very few:
- People will have to know to switch from 'master' to 'main' , and this may lead to a small amount of confusion. Since
  most (in fact, I think all) repositories within SuperCollider do not commit directly to the default branch, and
instead work by opening PRs against whatever the default branch happens to be, much of this change will be entirely
transparent to contributors and require no effort on their part.

One case where this may lead to confusion is that if people continue to attempt to pull from 'master' but not 'main',
they may not receive updates. I expect the impact of this to be small, since:
- the change will be advertised, and so most people who have already cloned SuperCollider will know about it and update
  accordingly.
- the most active repository in this project, supercollider/supercollider, does not even use 'master' as its default
  branch (it is 'develop').
