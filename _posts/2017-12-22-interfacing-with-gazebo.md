---
Title: Interfacing with Gazebo
---

Gazebo exposes a Google Protobuf API interface to interface with the simulator,
allowing one to read the state of the simulator and also control the simulator.
The API documentation can be found
[here](http://osrf-distributions.s3.amazonaws.com/gazebo/msg-api/7.1.0/classes.html).

The actual Google ProtoBuf message definitions (proto files) for Gazebo can be found in
[gazebo/msgs/](https://bitbucket.org/osrf/gazebo/src/dce3030d87304270475ddb742e9be402bc383b5c/gazebo/msgs/?at=default)
in the Gazebo source code repository. If you haven't done so already clone this
repo. 
Once you have all of the proto files you need to create a language bindings for  Java, C++ or Python using the `protoc'
tool (I believe other languages are supported too). Install by,

{% highlight text %}
sudo apt-get install protobuf-compiler
{% endhighlight %}


Well now look at how to generate bindings for Python. 
Official documentation can be read
[here](https://developers.google.com/protocol-buffers/docs/reference/python-generated).

The protoc tool will compile the .proto files into the corresponding Python
files that can be interfaced with your code. The following command will place
all the generated files in build/gen.

{% highlight text %}
protoc --python_out build/gen --proto_path gazebo/gazebo/msgs
gazebo/gazebo/msgs/*.proto
{% endhighlight %}


There is a [bug](https://github.com/google/protobuf/issues/1491) currently that will not allow the generated Python code to use
relative imports to be compatible with Python 3. For example if you place all of
your generated code in a separate subdirectory, if the generated code needs to
import one another this currently will not work.

The work around is to place an __init__.py file in the generated directory to
signify the directory is a package and then run the
[2to3](http://www.diveintopython3.net/porting-code-to-python-3-with-2to3.html) tool to automatically
fix all the imports. The following command will fix imports (-f import) and
overwrite the fixed files in the msg/ directory. 

{% highlight text %}
2to3 -w -f import msg/
{% endhighlight %}


# PyGazebo

I've already done for you in the pygazebo library.
Unfortunately the original project seems abandoned and has outdated messages. My
[fork](https://github.com/wil3/pygazebo/tree/f_msgs) has the current updated
protobuf messages for Gazebo 8. For future releases you just updated the Python
bindings as explained previously.  
Just clone this branch and install,

{% highlight text %}
sudo pip3 install .
{% endhighlight %}

The library uses asyncio and is a bit confusing. I'll include some examples in a
future post.

