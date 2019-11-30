# Transcode MythTV Recordings And Store Into MythTV Video Library

Note: This script is highly personalized.  There would need to be substantial revisions made before someone other than I uses it.

## recording2video

This Python takes a MythTV recording, converts it into a standalone video file and stores it in the MythTV video library.  I use it to archive TV series recordings.

The script must be run on a system that has a properly configured MythTV frontend installed.

Before the script is run the user may want to edit the recording(s) they plan to transcode, to remove commercials, etc.  This is done using the MythTV editing function to create a "cutlist".

The workflow implemented in this script goes like this:
1. The script first displays a list of all available recordings and prompts the user to select one.
2. The script collects episode metadata from the MythTV database and TheTVDB and displays it.
4. The script offers the user some options:
  a. To change the season and episode numbers,
  b. To normalize the audio volume,
  c. To only transcode the video, leaving out the steps that would store it back into MythTV's video library,
  d. To crop the black frame from a WGVU-Life (local alternate PBS channel) recording,
  d. To crop the black frame and remove the on-screen logo from a WGVU Create-TV (local alternate PBS channel) recording,
  e. To remove the on-screen logo from a WGVU (local PBS channel) recording
5. A target path and file name are set to match the directory structure I've established on my MythTV server, based on the recording's series name, season and episode number.
6. The script starts the `mythtranscode` script with the `--cleancut` option, sending the output to FIFO pipes.  Simultaneously, the script starts the `ffmpeg` command, reading audio and video from those pipes and transcoding it into a MP4 video file.<br />This is the core of the script, the reason that I wrote it.  This is the only way to do frame-level edits of MythTV recordings.  All ofher methods only support making cuts at keyframes.
6. If the user chose to normalize the audio then `ffmpeg` processes the MP4 file a second time, copying the video stream and re-encoding the audio, this time with normalization.  I found that including the normalization step in the previous step slowed things down dramatically.
7. Finally the video file is moved to the target path in the MythTV video library and a database entry is created for it.
