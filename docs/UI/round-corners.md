# Round corners
There is no native way to set a corner-radius for components / rectangle / anything at all.
The best results can be achieved by using a MaskGroup. Keep in mind that MaskGroups can't use 9-patch-images.
For some unknown reason (to me) using a Rectangle-Node inside a MaskGroup will only work if you set the opacity of the Rectangle to < 1.

To add a corner-radius simply add a black png with rounded corners to your [MaskGroup](https://developer.roku.com/en-gb/docs/references/scenegraph/control-nodes/maskgroup.md). 


```
<!-- any component -->
<MaskGroup
    id="mask"
    maskSize="[400, 225]"
    maskOffset="[0,0]" 
    maskUri="pkg:/images/$$RES$$/yourMaskImage.png"
>
    <Poster
        id="cover"
        width="400"
        height="225"
    />
</MaskGroup>
```
