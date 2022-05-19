# Tasks
Tasks are required for some items such as HTTP operations, and are good for other slow operations such as registry flushes.  They are also good for repeated timed operations such as regular configuration updates.  

But tasks threads are dramatically slower than the render thread.  I have heard numbers as high as a 10:1 difference in speed, although my benchmark tests showed about a 6:1 difference.  Using tasks is good for offloading operations of the render thread, and in some cases, like the HTTP mentioned above, required.  However, always consider the performance cost of relying on tasks too much for heavy processing or parsing.

When using tasks, rendezvous are the automatic synchronization system of BrightScript when moving variables between the task and other threads, and should be minimized. The cost has been greatly decreased from earlier version of BrightScript, but still is not free. The [BrightScript Language] extension for VSCode has a dedicated rendezvous tracking section to visualize your rendezvous counts and times. Rendezvous occurrences can be reduced by using parameters, hoisting variables, and refactoring if needed.  A goal of zero rendezvous is unrealistic, but getting as close to minimal as possible is not.

RAF must run as a task, and has a high rendezvous count and appears to be unavoidable. 

Finally, BrightScript has limits on the number of active tasks. 50 active tasks will generate a warning, and 100 will shut down a channel.  Using permanent tasks with field observers allows a single task to process repeated requests, and can help in minimizing the number of tasks.  These long-lived tasks can even include multiple observed fields that trigger various functions.  The task will process the observed triggers one at a time, in the order they are received.
