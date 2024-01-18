- Title: Move Wiki from GitHub and convert to a static site
- Date proposed:2024-01-17
- RFC PR: https://github.com/supercollider/rfcs/pull/0000 **update this number after RFC PR has been filed**

# Summary

Move the wiki from Github to wiki.supercollider.online.  Use [Sphinx](https://www.sphinx-doc.org/en/master/) to generate a static site.

# Motivation

A wiki is hopefully a permanent reference.

But in the history of SC, there has already been (at least?) one abandoned wiki that was migrated to the GitHub wiki, and while GitHub is a convenient place, [it may not a place we should support.][https://sfconservancy.org/GiveUpGitHub/]

Moving to out of GitHub would allow the community to take ownership of the wiki URL and be independent of the code hosting platform, providing a permanent location for precious information.

This would also prepare the community/wiki in case GitHub needs to be evacuated.

A static site would also have many advantages:
- independence of any hosting platform - hosting a static website can be easily done these days
- searching the wiki
- editing can be performed offline
- internal references can be checked for dead links at build time
- custom styling
- Clear PR workflow 
- website structure is independent of folder structure, which allows to improve the organization of files

please see discussion here: [6181](https://github.com/supercollider/supercollider/issues/6181)
and on the forum [here](https://scsynth.org/t/the-sc-wiki/8123/11?u=semiquaver)

# Specification

As a proof of concept I cloned the existing wiki and converted it to a Sphinx website - this means that the history of most pages is untouched, preserving valuable git history (see https://github.com/capital-G/supercollider-wiki/blame/main/docs/contributing/workflow/Unit-Testing-Guide.md)

Repo: https://github.com/capital-G/supercollider-wiki
Website: https://capital-g.github.io/supercollider-wiki/

Edit buttons were added to pages that drop users into the repo where they can edit and make pull requests in the browser.

Further work on style and structure could be done using a PR workflow

# Drawbacks

The only drawback that surfaced in the discussions so far was relative ease of contribution. There are online wiki systems that allow users to edit directly in the browser. This proposal would limit contributors to those people comfortable with a git PR workflow. 




# Unresolved Questions

