# Seamless Multi-Segment Videos (HLS)

I had a project with short form movie review videos, but had short intros and extros to be played before and after every video.  They wanted them to be seamless, to look like they were part of the original video, but also wanted them to be globally changeable for holidays, announcements, additions to channels we tracked (Netflix, Hulu, Amazon, and soon), etc.  

If I treated them as separate videos, which they were, there would be loading/buffering times before each of the segments, instead of just at startup.  This was against the spec I was given by the channel owners.

With understanding of the m3u8 format, and using the m3u8 discontinuity feature, multiple TS segments from multiple m3u8 files can be combined to make a single seamless stream.  I don’t have any source code to provide as the source I have is from BrightScript version 3.1 and doesn’t apply to the current BrightScript/SceneGraph system. 

The gist of my solution was to first download the m3u8 file from the Vimeo server and extract the included TS file names.  The intro/extro m3u8 files were already loaded, parsed, and cached at channel startup.  A new m3u8 file was created, and stored to TMP,  again using the m3u8 discontinuity feature, combining the intro TS files, the video TS files, and the extro TS files. 

I then streamed the resulting m3u8 file using a roStreamSocket, from the users box (any available port would work), to play the video as if it was a single video.  Before RAF existed I also used this to embed ads supplied by Roku into the m3u8 for client side stitching, creating a single stream for the entire playback, with no interim loads.

Again I have no current source code to provide here, but the concept worked perfectly on our channel and is still viable as the ability to directly download files from URL and the roStreamSocket node both still exist.
