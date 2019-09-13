# SuperCollider RFCs

RFC (Request for Comment) is a way for SuperCollider contributors to discuss large-scale decisions in project direction in a focused environment.

**Note:** This document is presented as a proposal of the RFC process itself -- it isn't a "live" project, so please don't go filing any RFCs yet! :) However, there is a [pull request opened as an example](https://github.com/snappizz/rfcs/pull/1).

If you see anything worth changing in this document, please feel free to file a PR for it.

The final location for this repository would be `supercollider/rfcs`.

## What does an RFC do?

The RFC process is intended to bring focus and structure to discussions of important changes and new features in the SuperCollider project. It provides a space for sharing and collaborating on design documents, and keeping community discussion organized and goal-oriented.

Visibility and inclusivity are important values for RFCs. They are advertised to both developer and user communication channels so everyone can participate.

An RFC is only a design document, and not a single line of code needs to be written for the discussion to be approved (although prototyping on a branch is often a good idea). An approved RFC has no timeline for when it needs to be implemented.

## When should I file an RFC?

RFCs should be filed for proposing "substantial" changes. The meaning of "substantial" is subjective, but a good start would be anything that benefits from design discussion. Put a different way, it's recommended to file an RFC if the design or implementation isn't immediately clear, or if there are drawbacks or potential consequences that require discussion first. The types of changes include:

- New features
- Breaking changes
- Organizational changes, including changes to the RFC process itself

The following do not need to go through an RFC:

- Bug fixes.
- Releases of new SuperCollider versions.
- Anything highly unlikely to be controversial or in need of discussion.

If you don't know whether you should file an RFC, it's fine to just ask.

## Proposing an RFC

### Step 0: Decide if you should file an RFC

The development community is small and has limited bandwidth. If there are a lot of RFCs open, it may be better to help advance existing discussions rather than pile on new ones.

As an RFC author, it's best if you only have one RFC of yours open at any moment. This restriction is porous, but you are advised to avoid multitasking and be cognizant of how much the community's attention may be divided across other issues.

### Step 1: Fork this repository

Fork this repository if you haven't already.

### Step 2: Write RFC

Copy `rfcs/0000-template.md` to `rfcs/0000-<my-proposal-title>.md` and begin working on the RFC.

Please put a lot of thought into your writing. A great RFC explains and justifies its motivations and carefully considers all drawbacks and alternatives. Show the reader what the problems are and how the proposal concretely and practically addresses them.

### Step 3: File pull request

Make a pull request back to this repository. The open pull request **is** the RFC. Discussion of the RFC is held in the comments of the pull request.

After filing the pull request, note the pull request number (say #35). This is your RFC number. The RFC branch should then be updated by changing the file name to e.g. `rfcs/0035-<my-proposal-title>.md`, and updating the "RFC PR" field inside the RFC text itself to link to the pull request. The point of this is to make the `rfcs/` folder a nice chronologically ordered archive of documents so people can look at past RFCs.

### Step 4: Announce

After the RFC is ready to go, please announce it to:

- sc-dev
- sc-users
- scsynth.org

Here is a template:

```
Subject: RFC: Deprecate BadFeature

A new RFC (request for comment) has been opened here:

https://github.com/supercollider/rfcs/pull/35

Please visit the above link for discussion.
```

## Discussing an RFC

Discussion about the RFC should happen on the RFC pull request on GitHub. If discussions happen outside the GitHub thread, it's best to link and summarize them in the RFC thread to make the conversations easier to track.

The RFC will likely undergo numerous revisions in the discussion process.

If you are discussing an RFC, it is your responsibility to work towards **reaching a conclusion**. When you make a post to the RFC thread, talk about the contents of the RFC, and the RFC alone.

Here are some guidelines:

- **Think about the important questions:**
  - Do you agree with the proposal in its current form?
  - Are concerns and questions adequately addressed?
  - Do you have suggestions for how the RFC can be revised or expanded, especially in the Unresolved Questions section?
- **Don't be shy:** Even if you aren't an active SC contributor, or if you don't feel "qualified" to discuss the topic, you can and should directly state your opinion on an RFC that affects you in some way.
- **Stay on topic:** Starting or continuing tangents is disrespectful to the RFC author and is strongly discouraged. Unfortunately, tangential discussion often dilutes the thread and makes it hard to actually gauge what the consensus is. The purpose of the RFC thread is to reach a conclusion, and any commentary that does not contribute to that is damaging to the RFC process.
- **Civility and respect:** Like all other discussions that happen in the SuperCollider community, RFC discussions are governed by our [Code of Conduct](https://github.com/supercollider/supercollider/blob/develop/CODE_OF_CONDUCT.md). Creating an RFC is a laborious project. Commenting on an RFC should be done with respect and empathy for the hard work that the author has put into it.
- **Avoid scope creep:** You should make suggestions on how the RFC can be revised or expanded, but be careful not to expand it too much and develop it into something far more ambitious than was originally intended.

## Finalizing an RFC

When the RFC seems ready to conclude, anyone may make a motion for a Final Comment Period. The Final Comment Period lasts 14 days, and has a *disposition* to either **pass** or **reject**.

When should the Final Comment Period start? There doesn't need to be a consensus with everyone involved (which is usually impossible), but the RFC should be in a state where the author feels that its writing effort is complete, the arguments have been clearly articlated, discussions are resolved and drawbacks acknowledged in the RFC's text, and there isn't a strong consensus *against* the disposition.

Sometimes, RFCs can reach points of disagreement where no clear consensus is in sight. Unfortunately, there is no way that this RFC process can solve such issues, and these have to be handled on a case-by-case basis.

If no important developments occur during this time frame, someone with write access merges (for passed RFCs) or closes (for rejected RFCs) the RFC PR.

At any point, the RFC author can also choose to **withdraw** an RFC with no 14-day grace period necessary.

Implementation of an RFC can start before, during, or after it has been approved. In fact, if you propose an RFC, you don't need to be the person to implement it, but usually the implementer is the same person as the RFC author.

## About the RFC process

This isn't a legal document, and you shouldn't spend a lot of time analyzing its wording. This process is intentionally quite informal and subjective. Situations will come up that may not have been anticipated by this article, and they should be handled on a case-by-case basis.

If something is ambiguous in this article, that means it left up to the discussion. If something is a *persistent* cause of ambiguity and confusion, then it may be wise to amend the RFC process itself for clarification.

