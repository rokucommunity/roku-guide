# Socket Server
I had a project with short form movie review videos, but had short intros and extros to be played before and after every video.  They wanted them to be seamless, to look like they were part of the original video, but also wanted them to be globally changeable for holidays, announcements, additions to channels we tracked (Netflix, Hulu, Amazon, and so on), etc.

If I treated them as separate videos, which they were, there would be loading/buffering times before each of the segments, instead of just at startup.  This was against the spec I was given.

My solution was to send the desired video url (from Vimeo) to the following function.  It would download the m3u8 file from the Vimeo server.  The intro/extro m3u8 files were already loaded and cached at channel startup.  A new m3u8 file was created, and stored to TMP, using the m3u8 discontinuity feature, combining the intro TS files, the video TS files, and the extro TS files.

I then streamed the resulting m3u8 file using an roStreamSocket, from the users box (we used port 2188, but any available port would work), to play the video as if it was a single video.  Before RAF existed I also used this to embed ads supplied by Roku into the m3u8 for client side stitching, creating a single stream for the entire playback, with no interim loads.

This is done before SceneGraph, tasks and the like were not yet part of BrightScript, so the code I have would not apply to current models and is not remotely runnable as is, but the general gist of it is in the following code segment.  A basic understanding of the m3u8 file format would be needed if trying to implement this ability, but should give a starting point.

```vb
Function VimeoM3U8(Size as string, Speeds, VideoLines, OriginalURL, UseIntrosExtros)
    CRLF=chr(13)+chr(10)
    Result = ""
    for each Speed in Speeds
       Value= "#EXTM3U" + CRLF + CRLF + "#EXT-X-VERSION:3" + CRLF + "#EXT-X-MEDIA-SEQUENCE:0" + CRLF + "#EXT-X-ALLOW-CACHE:YES" + CRLF + "#EXT-X-TARGETDURATION:10" + CRLF + CRLF
       if (UseIntrosExtros and m.Files.Exists("tmp:/intro.ts")) Value=Value + #EXTINF:4.250,"+CRLF+"http://"+m.IP+":2188/intro.ts"+ CRLF + "#EXT-X-DISCONTINUITY"+CRLF
         for each Line in VideoLines
            if Line.Left(4)="INF:"
	Lines=StringSplitChar(Line,chr(10))
	Value = Value + "#EXT" + Lines[0].trim() + CRLF + AbsoluteURL(Lines[1], OriginalURL) + CRLF
            end if
         end for
      if (UseIntrosExtros and m.Files.Exists("tmp:/extro.ts")) Value=Value + "#EXT-X-DISCONTINUITY"+CRLF + "#EXTINF:6.000,"+CRLF+"http://"+m.IP+":2188/extro.ts"+ CRLF
          Value=Value + "#EXT-X-ENDLIST"
          WriteAsciiFile("tmp:/x"+ Speed.toStr()+ "x" +Size + ".m3u8",Value)
          Result = Result + "#EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=" + (Speed*1000).ToStr()+",RESOLUTION="+Size + CRLF + "http://"+m.IP+":2188/x"+Speed.toStr()+"x"+Size + ".m3u8"+CRLF
          end for
      return Result
End Function
```