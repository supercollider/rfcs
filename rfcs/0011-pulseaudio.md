- Title: PulseAudio backend for Linux
- Date proposed: 2020-07-03
- RFC PR: https://github.com/supercollider/rfcs/pull/0011

# Summary

On some Linux systems Jack is either not available or difficult to configure. Users and distribution maintainers should have the option to use a PulseAudio backend instead. This RFC is about adding a new PulseAudio backend to SuperCollider. Jack will remain the default choice.

**Let me stress this point once more... this is NOT a replacement for jack, and we'll keep encouraging people to use jack under Linux if possible. This is just to open the possbility of using SuperCollider on systems where it is not easy to make jack work, for non expert users. We will make this clear in the documentaion and through the build mechanism.**

# Motivation

The status of Jack support in Linux distributions varies wildly: in some distributions it is supported out of the box, in others it needs potentially complex changes to the system or configuration that are out of reach to most casual users. A lot of Linux distributions are using (or are moving to) PulseAudio for their default audio backend.

For example in some Debian based systems (like Raspbian), it is not straightforward to configure jack and requires some user involvement. There are lots of reports from users trying to configure this on their systems.

Now, this RFC does not want to argue about the technical merits of Jack vs PulseAudio, it is just about making a PulseAudio backend available for systems where Jack is difficult to set up properly or not available.

The issue of not having a native PulseAudio backend has been raised a number of times in different forums, but so far no solution has been offered due to lack of development resources. This will allow a lot of users that cannot use SuperCollider (or other SW that relies on SuperCollider to generate sound) today, because it is hard to set up jack on their systems given their tecnical knowledge, to use it and have fun with it, which I believe is one of SuperCollider's objectives.

According to this proposal the default backend for Linux will continue to be Jack, and it will be up to the user or distribution maintainer to change this explicitly if necessary.

# Specification

The idea is to implement a PulseAudio backend for Linux. The PR for the technical work is now available here: https://github.com/supercollider/supercollider/pull/5081. Please note that the idea is to use that PR for the technical discussion of the implementaion, and keep using this RFC for the reasons why this makes sense.

The backend choice will be done at build time, not runtime, as it is done currently for the other backends. *(This is currently under discussion. Some people think it might be better to do this at launch time)*

This PulseAudio backend will be implemented using RtAudio, which provides a nice C++ API, which is simple to use and maintain. For more info see here: https://www.music.mcgill.ca/~gary/rtaudio/. *(There is some discussion regarding the maturity level of maintenance of this library - I believe it is ok, and testing is going well, but make the note here)*

In the initial implementation the audio device selection will be done in the System's control panel / PulseAudio server software, following PulseAudio conventions. We have tested this in a system with multiple audio devices, and this allows the audio device to be changed at runtime from the PulseAudio control application. No need to reboot the SC server or anything. This brings the use model closer to what people using PulseAudio are used to, which I believe is important.

In the future, we might implement device selection inside SuperCollider itself, a number of people have indicated interest in this.

At the moment I have working code in the PR described above, which has been tested in a couple of different systems, so it is mature enough for people to start giving it a proper try and give feedback about their specific experience. 

The new code will NOT require any changes to the existing code of the SuperCollider server itself. Just compiling in the new audio driver file when requested by the build process.

There will be some simple changes to some CMakeLists.txt (`./CmakeLists.txt, lang/CMakeLists.txt, server/scsynth/CMakeLists.txt`) to add the new PulseAudio backend as an option. The default will continue to be Jack, so unless the user selects pulseaudio explicitly, there will be no changes to the current build.

I have added clear notes in the README_LINUX file, to make sure that people understand that jack is the preferred option if possible. If people prefer to be even more explicit or change the style, I am more than happy to change that.

So, in total, I see just one new file added and 3 CmakeLists files changed, together with some documentation updates, so the change will be very self-contained and easy to maintain.

# Drawbacks

I do not see any obvious drawbacks, since the default backend in Linux will continue to be jack, as it is now. If not selected as part of the Cmake build process, the new file will not even be compiled in.

# Unresolved Questions

- Perhaps there are language side changes required, but this has not been identified yet. From what I can see, the data is getting to the driver code, and we can access things like the sample rate, samples per block, device name, channel configuration, etc.
- Build time vs Launch time selection of the backend? Both have pros and cons. 
