
This quick tutorial  is to show how arbitrary data can be logged to the Betaflight blackbox logger which is helpful during development. Note this is for betaflight 3.3.3, new versions may be different.


In `build/debug.c` there is a global array variable called [debug](https://github.com/betaflight/betaflight/blob/master/src/main/build/debug.c#L26). This has a size defined by `DEBUG16_VALUE_COUNT` which defaults to 4.
This allows the placement of 4 debug values that can be written to the blackbox log.

1. Register the debug type in Betaflight
First add a string representing the data to be logged to
[build/debug.c](https://github.com/betaflight/betaflight/blob/master/src/main/build/debug.c#L33). This will be the value you use to enable logging from the configurator. Next add an enum representing your data in [build/debug.h](https://github.com/betaflight/betaflight/blob/master/src/main/build/debug.h#L51). You will use this in your code to enable logging. 

2. Log your data
In your code you can now log your data. First include the debug header, and then log the data, for example


```c
#include "build/debug.h"
....
if (debugMode == DEBUG_NN_OUT) {
    debug[i] = output;
}
```

3. Configured the firmware
Flash the firmware, open up the configurator and go to the command line. Set
the `debug_mode` to whatever string value you set in `debug.c` (this is case
insensitive ). For example,


```
set debug_mode = nn_output
```

4. View your logged data
From the blackbox explorer create a new graph and select the debug values from
the drop down menu.

![alt text](https://raw.githubusercontent.com/wil3/wil3.github.io/master/images/blackbox-custom-data.png "Blackbox Explorer")

# Future work

If you are using the blackbox explorer there are the options to only select the
4 debug values. But what if we have more?

It looks like we can increase the number of debug values by increasing [DEBUG16_VALUE_COUNT](https://github.com/betaflight/betaflight/blob/master/src/main/build/debug.h#L23) and increment the number of debug fields in the variable [blackboxMainFields](https://github.com/betaflight/betaflight/blob/master/src/main/blackbox/blackbox.c#L218-L221) found in `blackbox/blackbox.c`.
However it is unknown at this time how this will be read by the blackbox
explorer. You may be able to use
[blackbox-tools](https://github.com/cleanflight/blackbox-tools) to parse the
log to csv and then write your own utility, say in Python, to display the data. 


