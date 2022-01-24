---
title: "Record and publish your meeting"
date: 2021-08-22T21:43:47+01:00
tags: ["meetings", "recording", "agile"]
featured_image: "/images/screen_record_meetings_title.png"
---

Scenario: You're in a handover session and explain everything in great detail but Dave is too hungover,
Jim is on holiday and Jane is at another meeting and Chris isn't asking any questions and in any case
Trevor (who is actually going to be looking after this system after handover) hasn't 
been recruited yet.

## Wouldn't it be useful to record the session and make it available?  

Now, this piece of writing does not explore whether we should be recording meetings,
nor does it attempt to answer the question of whether good documentation wouldn't be preferable
to having to sit and spend hours watching someone droning on about something that could be condensed
into a five minute read of a blog post. So...

## Assuming it would be useful, how do I got about it?

Yes, I know that pro versions of Zoom, Meet and Teams can probably do this at a click of a button, 
but as luck would have it, we've not paid for the enterprise edition or no administrator is around
to actually enable the feature.

## Assuming I don't want to spend any money to do it?

Let's say I don't actually know yet whether anyone will be finding such recordings useful, and
I don't want to spend money on the off-chance, how would I go about doing it without needing to
spend hours research different recording solutions.

If this sounds too contrived, this is exactly the situation that I found myself in recently.  To make it even
more complicated, I'll add in a few more requirements:

- I am using a Mac
- QuickTime screen recording didn't fit the bill (not sure why it didn't work, possibly because I wasn't 
  on the latest OSX)
- I'd like to add some title graphics at the beginning of the "end product"

# OBS

![Open Broadcaster Software](/images/screen_record_meetings_obs.png)

The [OBS project](https://obsproject.com) was recommended to me when I asked around about how to
record my screen.  I have to admit, I never heard about it before - and am still not quite sure what I
am doing with it.  My idea of "wouldn't it be great to record the meeting and the screen that I'll be
sharing" and "oh, I can't press 'record' in meet" left me with very little time to find a recording
solution.  So I can quite confidently state that I'm probably using it wrong...  Anyway, here goes:

## Installation

I installed `OBS` via Homebrew

```shell
$ brew install obs
```

It took a fair while to install - probably because it had been a while since I ran `brew update` - but
soon enough I was able to start OBS.

Well, almost - the following caught my eye on the [OBS QuickStart guide](https://obsproject.com/wiki/OBS-Studio-Quickstart)

```
macOS users: If you're on macOS, you'll need an extra app to 
capture desktop audio. This is due to limitations in macOS that 
provide no direct capture methods for desktop audio devices.
```

## iShow Audio Capture

If I'm honest, initially it was a bit off-putting having to install something else, but I
overcame my initial suspicions. I also didn't relish the fact that this wouldn't work with Big Sur
but I was still a couple of versions behind (Mojave) and needed a solution.

So I installed the app following [these instructions](https://support.shinywhitebox.com/hc/en-us/articles/204161459-Installing-iShowU-Audio-Capture)
and soon enough had a new audio input.  This would allow me to record both the audio coming out of
the meeting and my own voice.  Without it, I'd have to pick one or the other.

![Audio Capture](/images/screen_record_meetings_audio_capture.png)

To get both, I followed the instructions in [here](https://obsproject.com/forum/resources/os-x-capture-audio-with-ishowu-audio-capture.505/)
to set-up my Audio MIDI Setup (to be honest, I didn't even know Macs had a MIDI setup tool).

![Audio MIDI Setup](/images/screen_record_meetings_audio_midi_setup.png)

## Recording

![Test Capture](/images/screen_record_meetings_test_capture.mp4)

So now I'm ready to record!  Couple of things to setup:

- Set a source of "Display Capture" in the sources to capture the whole screen or
- Set a source of "Window Capture" to select a particular window  
- Don't forget to right-click on the "Display/Window Capture" and set Transform to "Fit to screen"

## Tips for recording

- Set the volume of your input to the same as the volume of mic - otherwise you might end up
  having problems with the sound of your voice being to low/loud
- Remember that recording your mic means that you're not muted when you mute Teams/Meet
- It is possible to do a screen recording of a session whilst having two young children at the same
  table.  But only when you disable the mic input in OBS and most of the talking is done by
  other people on the call.

## Making credits

Now, continuing in the spirit of not wanting to spend any money, I was thinking about how I could create
some titles.  Having re-read OBS as part of this post, I've come to realise that there probably were
easier ways of doings this, but here goes:

- created a keynote presentation with an image
- added a bit of text for "My Title" and "My Other Title"
- added transitions
- exported as a video

What follows are two videos:

- Making a title

![Making a title](/images/screen_record_meetings_making_a_title.mp4)

- The result

![The result](/images/screen_record_meetings_title_result.mp4)

## Splicing it all together

Finally, I wanted to splice it all together, but for some unknown reason, QuickTime let me down.  Although
I was able to "Add a clip" - every time I did, QuickTime refused to save.  So I installed `ffmpeg`:

```shell
$ brew install ffmpeg
```

And then I used the following to merge my title track and my recorded screen video (which worked for 
Google Meet and Microsoft Teams):

First, I found that I had to give the generated title video a silent sound track:

```shell
$ ffmpeg -i title.m4v -f lavfi -i \
  anullsrc=channel_layout=stereo:sample_rate=44100 \
  -c:v copy -shortest title.mp4
```

Then I trimmed the OBS video (to remove the "can you hear me?" or "you're on mute" hilarity at the beginning and
"see you in the pub" at the end of the session)

```shell
$ ffmpeg -i session.mkv -ss 00:03:03.0 -to 01:00:10.0 \ 
  -codec copy session_trimmed.mp4
```

Finally I put the title and the the session together

```shell
ffmpeg -i title.mp4 -i session_trimmed.mp4 -filter_complex \
  "[0:v:0][0:a:0][1:v:0][1:a:0]concat=n=2:v=1:a=1[outv][outa]" \
  -map "[outv]" -map "[outa]" output.mp4
```

## Conclusion

In this post, I have put together my experiences with creating a video recording of a handover
session.  I know that I could have done a lot of things differently and probably more efficient
but as a cheap and quick way and of recording a a lot of session, it worked for me.