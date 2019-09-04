# SuperCollider RFCs

RFC (Request for Comment) is a way for SuperCollider contributors to discuss large-scale decisions in project direction in a focused environment.

**Note:** This document is presented as a proposal of the RFC process itself -- it isn't a "live" project, so please don't go filing any RFCs yet! :) However, there is a [pull request opened as an example](https://github.com/snappizz/rfcs/pull/1).

If you see anything worth changing in this document, please feel free to file a PR for it.

## What does an RFC do?

The RFC process is intended to bring focus and structure to discussions of important changes and new features in the SuperCollider project. It provides a space for sharing and collaborating on design documents, and keeping community discussion organized and goal-oriented.

Visibility and inclusivity are important values for RFCs. They are advertised to both developer and user communication channels so everyone can participate.

The following generally should go through an RFC:

- New features.
- Deprecation of features.
- Significant breaking changes.
- Organizational changes, including changes to the RFC process itself.
- Anything that we can't reach consensus on in an issue or PR thread.

The following do not need to go through an RFC:

- Bug fixes.
- Releases of new SuperCollider versions.
- Anything highly unlikely to be controversial or needing discussion.

An RFC is only a design document, and not a single line of code needs to be written for the discussion to be approved (although prototyping on a branch is often a good idea). An approved RFC has no timeline for when it needs to be implemented.

## Proposing an RFC

### Step 0: Decide if you should file an RFC

The development community is small and has limited bandwidth. If there are a lot of RFCs open, it may be better to help advance existing discussions rather than pile on new ones.

As an RFC author, it's best if you only have one RFC of yours open at any moment.

### Step 1: Fork this repository

Fork this repository if you haven't already.

### Step 2: Write RFC

Copy `rfcs/0000-template.md` to `rfcs/0000-<my-proposal-title>.md` and begin working on the RFC.

Great RFCs are strong and decisive with clear and direct writing. They do a great job at explaining and justifying their motivations. Show the reader what the problems are and how the proposal concretely and practically addresses them.

To help you write great RFCs, we have a template structure for you to fill out. This skeleton helps you write a coherent and persuasive proposal:

- **Summary.** Very short description.
- **Motivation.** Introduce and set the background for your proposal. Some example points of discussion:
  - What specific problems are you facing right now that you're trying to address?
  - Are there any previous discussions? Link to them and summarize them (don't force your readers to read them though!).
  - Is there any precedent set by other software? If so, link to resources.
- **Specification.** A concrete, thorough explanation of what is being planned.
- **Drawbacks.** Carefully consider every possible objection and issue with your proposal.
- **Unresolved questions.** Are there any portions of your proposal which need to be discussed with the community before the RFC can proceed? Be careful here -- an RFC with a lot of remaining questions is likely to be stalled. If your RFC is mostly unresolved questions and not too much substance, it may not be ready.

### Step 3: File pull request

Make a pull request back to this repository. The open pull request **is** the RFC. Discussion of the RFC is held in the comments of the pull request.

After filing the pull request, note the pull request number (say #35). This is your RFC number. The RFC branch should then be updated by changing the file name to e.g. `rfcs/0035-<my-proposal-title>.md`, and updating the "RFC PR" field inside the RFC text itself to link to the pull request.

### Step 4: Announce

After the RFC is ready to announce, please announce it to:

- sc-dev
- sc-users
- scsynth.org

Here is a template:

```
Subject: RFC: Deprecate BadFeature

A new RFC (request for comment) has been opened here:

https://github.com/supercollider/rfcs/pull/35

Please visit the above link for discussion. (Do not reply to this post to discuss the RFC.)
```

## Discussing an RFC

Discussion about the RFC happens on the RFC pull request on GitHub. Don't discuss outside the pull request thread -- that fragments the conversation.

If you are discussing an RFC, it is your responsibility to work towards **reaching a conclusion**. When you make a post to the RFC thread, talk about the contents of the RFC, and the RFC alone.

It is especially important to address these questions:

- Do you agree with the proposal in its current form?
- Are concerns and questions adequately addressed?
- Do you have suggestions for how the RFC can be revised or expanded, especially in the Unresolved Questions section?

The RFC will likely undergo numerous revisions in the discussion process.

### Don't be shy

Even if you aren't an active SC contributor, or if you don't feel "qualified" to discuss the topic, you can and should directly state your opinion on an RFC that affects you in some way.

Even a brief message like

> I agree with this. X has caused a lot of problems in my performance setup and this would be great.

is extremely helpful.

People who disagree with a proposal are more likely to speak up. To counterbalance this, a lot of people simply posting "I agree" will give a good impression of the amount of support in the community.

### Stay on topic

The SC community is an enthusiastic one that loves talking about cool ideas. Unfortunately, tangential discussion often dilutes the thread and makes it hard to actually gauge what the consensus is. **Starting or continuing tangents is disrespectful to the RFC author and is strongly discouraged.**

The purpose of the RFC thread is to reach a conclusion, and any commentary that does not contribute to that is damaging to the RFC process.

### Civility and respect

Like all other discussions that happen in the SuperCollider community, RFC discussions are governed by our [Code of Conduct](https://github.com/supercollider/supercollider/blob/develop/CODE_OF_CONDUCT.md).

Creating an RFC is a laborious project. Commenting on an RFC should be done with respect and empathy for the hard work that the author has put into it.

### Avoid scope creep

You should make suggestions on how the RFC can be revised or expanded. But be careful not to expand it too much and develop it into something far more ambitious than was originally intended.

### Meetings are great

Whether you're authoring or discussing an RFC, scheduling Skype meetings with others involved is a really great way to move it forward.

## Finalizing an RFC

When it appears that most open questions in the discussion have been resolved and addressed in the RFC's text, a motion for a Final Comment Period may be made. A Final Comment Period lasts 14 days. If no major new developments occur, the RFC is marked as **passed** or **rejected**. The motion for the Final Comment Period may be made by anyone who was involved in the discussion.

## Implementing a passed RFC

Implementation of an RFC can start before, during, or after it has been approved. In fact, if you propose an RFC, you don't need to be the person to implement it. However, usually the implementer is the same person as the RFC author.
