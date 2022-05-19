# Component Libraries
Component libraries are normally downloaded as needed, and not distributed with the channel itself in the channel zip file.  But they can be included with the channel itself, avoiding the download process for each user on every channel load, shortening the process from download then compile, to just a compile, which is done on channel load anyway.

A simple zip file (exposed to any channel devs using the library) or a pkg file (with the source code protected) can be used for this embedded CL ability.

A library is created, must be referenced in the library manifest, and zipped or optionally packaged.
```
sg_component_libs_provided=MyLibrary
```

A Node should also be created in the library exposing the public library functions:
```xml
<?xml version="1.0" encoding="utf-8" ?>
<component name="MyLibrary" extends="Node">
   <interface>
        <function name="func1" />
        <function name="func2" />
    </interface>
    <script type="text/brightscript" uri="LibCode1.brs" />
    <script type="text/brightscript" uri="MoreCode2.brs" />
      …
    <script type="text/brightscript" uri="EvenMoreLibCode.brs" />
</component>
```

The library zip file should be included in the channel's source directory, then referenced via a stub file, also in the source directory, and any exposed subs/functions wrapped:
```vb
sub MyLib_Func1(Parameters): MyLib().callfunc("Func1", Parameters): end sub
function MyLib_Func2(Parameters): return MyLib().callfunc("Func2", Parameters): end sub

function MyLib()
    Library= m.Global.MyLib
    if Library<>invalid return Library' If we have it already initialized in global memory return it
    Library=CreateObject("roSGNode"," MyLibrary: MyLibrary ")
          ' This and the next line can output log messages that the object can’t be created, this is normal if very early in channel initialization while the sub-compile takes place
    while (Library=invalid): Sleep (128): Library=CreateObject("roSGNode"," MyLibrary: MyLibrary"): end while
          ' If called too soon in channel initialization, this retries until it succeeds (in my experience a fraction of a second)
    m.Global.AddFields({MyLib:Library}) ' Save the newly created component to global memory, so subsequent calls to this will return the stored value
    return Library
end function
```

This is load and compile the library the first time it is called, and store if to the global variable so it is available for subsequent calls.  The while…sleep(128) cycle is to give the CL a moment to compile.

By putting the above initial load and store to global in the channel initialization code, the entire MyLib() function could be eliminated and replaced by calling m.Global.MyLib directly.  With minor code adjustments the zip or pkg file could be stored anywhere in the channel desired, but this assumes it’s in the source directory.