- Title: Improve SuperCollider versioning for non-release builds
- Date proposed:
- RFC PR: https://github.com/dyfer/rfcs/pull/0000
- SuperCollider issue: 

# Summary

I'd like to propose introducing finer granularity in SuperCollider versioning.

# Motivation

Currently, SC builds other than the release builds report the version of the last released build. This pertains to the "latest develop" builds uploaded to S3, builds from other branches and PRs, as well as local builds. I would like these builds to report a more accurate version.

# Specification

My proposal pertains to SuperCollider version, as defined in the `SCVersion.txt` file and reported by sclang when starting up. 

I am seeking feedback on two "levels of granularity" for the proposed changes:
1) coarse, where individual builds do not get individual version suffixes, aimed at differentiating released builds from non-released builds
2) (optional) fine, where individual builds are differentiated, in terms of version suffix

For 1) I am seeking to fine-tune the naming of the general version suffixes, like `alpha`, `beta`, or `prerelease`. For 2), I'm seeking feedback whether such granularity is practical and desired, as well as the format for the version suffix.

### Coarse versioning

I'd like to propose to append suffixes `-alpha` for the builds on the develop branch, and `-beta` for the builds on the release branch (`3.12` etc), for the versions _following_ the last bugfix release and _preceding_ the _release candidate_ of the next minor/major release. 

I propose that the builds in the `develop` branch have the version number of the _next_ release (`current minor` + 1), with the appropriate suffix.

I propose that the builds in the `3.x` branch have the version number of the _upcoming_ release, with the appropriate suffix.

Currently used suffixes (`-rc1` etc) would be used the same way, as they are used currently.

Here's a rudimentary visualization of proposed changes, starting at the current development cycle (after the `3.11.2` release, before creating the `3.12` branch):
```
[branch name]

| 3.12.0-alpha     | 3.13.0-alpha                                      | 3.14.0-alpha
[develop] ________ _________________________________________ ... _____ ____________________ ...
                  \                         [master]  __ /            \
            [3.12] \________________________________/           [3.13] \___________________ ...
                    | 3.12.0-beta   | 3.12.0-rc1      | 3.12.0          | 3.13.0-beta 
```

### Fine versioning

Optionally, I would like to consider, whether the non-release builds could have an additional version suffix. The motivation for this is to differentiate between multiple development versions of SC that might be present on the user machine. This can happen for example, if different versions have different crucial fixes that the user needs, which have been merged into the develop branch at different points in time.

I was considering adding information like short SHA or short SHA and latest commit date, in order to have a reference to both commit build, as well as a sense of _when in the development cycle the build was created_, for example:

- `3.12.0-alpha+SHA`, e.g. `3.12.0-alpha+abcdef0`
- `3.12.0-alpha+YYYYMMDD`, e.g. `3.12.0-alpha+20210401`
- `3.12.0-alpha+YYYYMMDD.SHA`, e.g. `3.12.0-alpha+20210401.abcdef0`

In my view the objective of  fine versioning is _not_ to give the absolutely unambiguous identification of the build, but rather to provide a general idea where in the development cycle the build was done. I understand that this might be superfluous, or otherwise undesirable, and I'm looking forward to feedback on this.

# Drawbacks

I imagine the coarse versioning to be implemented by creating two version entries in `SCVersion.txt` (The RFC is not about specific implementation AFAIU, so I'm not going into details here). These version entries would need to be updated when branching out to the new release (e.g. `3.12`), which adds some housekeeping overhead.

The fine versioning might result in more drawbacks, if the version is obtained for each build, requiring rebuilding numerous elements of SuperCollider unnecessarily.

# Unresolved Questions

- Do we want to use `-alpha` and `-beta` suffixes, or maybe `-prerelease` for both versions?
- Do we want to consider fine versioning or not?
- This change does not address builds between the first `-rc` version and the last bugfix version in the development cycle. IMO this is not crucial, but I'm open to suggestions if we should differentiate these builds as well
- This change does not pertain to naming build artifacts, which could be considered later, once the version format itself is finalized.
