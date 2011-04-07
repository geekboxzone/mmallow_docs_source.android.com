# Lights #

<p>Android defines a user space C abstraction interface for LED hardware. The interface header is defined in 
<code>hardware/libhardware/include/hardware/lights.h</code>.
In order to integrate LEDs with Android you need to build a shared library that implements this interface. 

The types of logical lights currently supported by Android include:
<ul>
<li>Backlight</li>
<li>Keyboard</li>
<li>Buttons</li>
<li>Battery</li>
<li>Notifications</li>
<li>Attention</li>
</ul>
</p> 
 
<a name="androidLightsBuildingDriver"></a><h3>Building a Lights Library</h3>
<p> To implement a Lights driver, create a shared library that implements the interface defined in <code>lights.h</code>. You must name your shared library 
<code>liblights.so</code> so that it will get loaded from <code>/system/lib</code> at runtime. 
</p>

 
Doxygen content is unavailable at the moment as source.android.com is deconstructed and then reconstructed. Sorry for the inconvenience!
