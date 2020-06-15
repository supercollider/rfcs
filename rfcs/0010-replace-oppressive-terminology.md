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
and accurate alternatives.

A full motiviation and argumentation for this change, along with examples of precedent, can be found in the [Internet
Engineering Task Force](https://tools.ietf.org/id/draft-knodel-terminology-00.html) RFC on the same topic. This RFC
itself should be regarded as an effective "resubmission" of the IETF RFC for the SuperCollider community; I believe it
makes the case very well, I agree with it completely, and I have nothing to add to it.

# Specification

(Much of this is directly adapted from the IETF RFC above)

We should:
- **Change the 'master' branches of repositories 'master' to 'main'**, as shown in https://www.hanselman.com/blog/EasilyRenameYourGitDefaultBranchFromMasterToMain.aspx.
- Prevent new commits from being pushed to 'master' branches of repositories.
- Replace the oppressive terms “master-slave” with more accurate alternatives.
- Replace the oppressive terms “blacklist-whitelist” with more accurate alternatives.
- Use the neuter “they” as the singular pronoun and
- Reflect on our use of metaphors generally.

These changes should be made across all SuperCollider repositories and documentation.

Luckily, these terms see very limited use within our repositories and code already; the change would not be difficult.
And could be done by one or a few people within a few days.

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

Changes to terminology in documentation have no drawbacks that I can see.

# Unresolved questions

Should the "master" branches of our repositories remain present even if they are no longer updated? I think this may be
unfortunately desirable for the following reasons:
- links to SuperCollider source code should be preserved when possible
- removing the branch may be very disruptive to people who have cloned the project
- removing a branch on which releases are tagged 

These are the same reasons why I would also not propose to rewrite the history of this project by filtering each past
commit to change this terminology.

The guide I found for this, https://www.hanselman.com/blog/EasilyRenameYourGitDefaultBranchFromMasterToMain.aspx, also
does not recommend entirely deleting the master branch.

Personally, I feel that creating a new 'main' branch, and continuing work along it, is fully within the spirit of this
change, but I also recognize that as someone who is not significantly affected by this terminology, I cannot and should
not be the last word on this.
