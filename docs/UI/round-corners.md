# Round corners
There is no native way to set a corner-radius for components / rectangle / anything at all.
The best results can be achieved by using a MaskGroup. Keep in mind that MaskGroups can't use 9-patch-images.
For some unknown reason (to me) using a Rectangle-Node inside a MaskGroup will only work if you set the opacity of the Rectangle to < 1.