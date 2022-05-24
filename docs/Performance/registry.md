# Registry
rokudevelopers.slack.com: @Scott Brower

Reading and writing to and from the registry is quick enough, but the required flush after writes is not.  Creating a permanent singleton task to handle flushes, and even writes if wanted, moves that out of the render thread, and can make a significant different in performance if you are using the registry heavily, as I do as for persistent event count accumulators.

Also, the registry uses a flash memory system, like an SSD drive but much smaller, and has a definite life span (number of times written).  This is normally a number in the hundreds of thousands of time per block, and the chips are extremely good about balancing the writes among the blocks and then disabling depleted blocks.  Excessively hammering the registry is a potential issue on device lifespan, although it would be difficult for a single channel to do any damage, unless it was a heavily used channel doing an insane and constant writing over a long term, such as using the registry for highly volatile global variables.

Finally, the registry has a 16k limit for a given channel, and as the registry is compressed, 32k is probably more realistic, but storing a full video library catalog or images (not normally compressible so the statement above becomes weaker) and the like would not work well in the long run.
