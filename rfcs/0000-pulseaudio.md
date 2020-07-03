- Title: PulseAudio backend for Linux
- Date proposed: 2020-07-03
- RFC PR: https://github.com/supercollider/rfcs/pull/0000 **update this number after RFC PR has been filed**

# Summary

On some Linux systems Jack is either not available or difficult to configure. Users and distribution maintainers should have the option to use a PulseAudio backend instead. This RFC is about adding a new PulseAudio backend to SuperCollider. Jack will remain the default choice since it has technical advantages.

# Motivation

The status of Jack support in Linux distributions varies wildly: in some distributions it is supported out of the box, in others it needs potentially complex changes to the system or configuration that are out of reach to most casual users, and in other systems it is completely unsupported. A lot of Linux distributions are using (or are moving to) PulseAudio for their default audio backend.

Now, this RFC does not want to argue about the technical merits of Jack vs PulseAudio, it is just about making a PulseAudio backend available for systems where Jack is difficult to set up properly or not available.

The issue of not having a native PulseAudio backend has been raised a number of times in different forums, but so far no solution has been offered due to lack of development resources.

According to this proposal the default backend for Linux will continue to be Jack, and it will be up to the user or distribution maintainer to change this explicitly if necessary.

# Specification

The idea is to implement a PulseAudio backend for Linux. It would be made available in a number of phases. The first phase would be to offer simple output support for basic configurations (i.e. stereo, 44100, ...). Later, add input support and more complex topologies. I think this approach makes sense, because the most likely configuration for people not using Jack will probably be simple (i.e. laptop headphones). For more serious work / configurations, people normally want to use Jack. I know this is a generalization, though.

The backend choice will be done at build time, not runtime. Supporting runtime choice would need some changes done on the server core, whereas supporting it at build time would require just adding some new driver code and compiling it, so much simpler to implement and maintain.

The plan is to start from the PortAudio driver (SC_PortAudio.cpp), to understand the basic cstructure of a SuperCollider audio driver, and then implement the PulseAudio driver based on that. Basically it is a matter of implementing some methods from the `SC_AudioDriver` class: `DriverStart, Start, Stop`.

At the moment I have some proof of concept code outputting audio using PulseAudio instead of Jack.

From the initial exploration I have made, it would only be necessary to add a new file to the current code base.

The new code will NOT require any changes to the existing code of the SuperCollider server itself. Just compiling in the new audio driver file when requested by the build process.

There will be some simple changes to some CMakeLists.txt (`./CmakeLists.txt, lang/CMakeLists.txt, server/scsynth/CMakeLists.txt`) to add the new PulseAudio backend as an option. The default will continue to be Jack, so unless the user selects pulseaudio explicitly, there will be no changes to the current build.

So, in total, I see just one new file added and 3 CmakeLists files changed, so the change will be very self-contained and easy to maintain.

# Drawbacks

I do not see any obvious drawbacks, since the default backend in Linux will continue to be Jack, as it is now. If not selected as part of the Cmake build process, the new file will not even be compiled in.

# Unresolved Questions

I don't have any unresolved questions at the moment, but the RFC will likely uncover some...
