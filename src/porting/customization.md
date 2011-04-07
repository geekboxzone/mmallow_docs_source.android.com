# Customization #

<a name="androidBootScreenCustomization"></a><h3>Boot Screen Customization</h3> 
 
<p>At startup, Android displays a splashscreen image while booting the device. Do the following if you wish to modify the default splash screen:</p> 
<p> 
<ol><li>Create a 320x480 image, <code>splashscreen.jpg</code> in this example.</li> 
<li>Using ImageMagick, convert your .jpg file to .r format:
<pre class="prettify"> 
convert screen.jpg screen.r
</pre> 
</li> 
<li>Use the rgb2565 application to convert the image to 565 format:
<pre class="prettify"> 
rgb2565 < screen.rgb > screen.565
</pre> 
</li> 
<li>Use fastboot to flash the image to the device:
<pre class="prettify"> 
fastboot flash splash1 screen.565
</pre> 
</li> 
</ol> 
 
 
<a name="androidNetCustPlat"></a><h3>Network Customization Platform</h3> 
 
 
 
<a name="androidNetCustPlatNetworkConfig"></a><h4>Network Configuration</h4> 
 
<p>Android stores network configurations as a resource that gets compiled into binary at form at build time. The XML representation of this resource is located at <code>//android/frameworks/base/core/res/res/xml/apns.xml</code>. This file does not include any configured APNs. You should not modify this file, but instead configure APNs by product at build time (see Build-time APN Configuration below).</p> 
<p>Each network configuration is stored in an XML element following this syntax:</p> 

    <apn carrier="T-Mobile US"
             mcc="310"
             mnc="260"
             apn=" wap.voicestream.com"
             user="none"
             server="*"
             password="none"
             proxy=" 216.155.165.50"
             port="8080"
             mmsc="http://216.155.174.84/servlets/mms"
    />
 
<a name="androidNetCustPlatAPNConfig"></a><h4>Build-time APN configuration</h4> 
 
<p>To set the APN configuration for a particular product target, add an <code>apns-conf.xml</code> file to the product configuration (do not modify the default platform APNs). This allows multiple products, all with different APNs, to be built off the same code base.  </p> 
 
<p>To configure APNs at the product level, add a line to the product configuration file like the example below (<code>vendor/<vendor_name>/products/myphone-us.mk</code>): </p> 
 
<pre class="prettify"> 
PRODUCT_COPY_FILES := vendor/acme/etc/apns-conf-us.xml:system/etc/apns-conf.xml
</pre> 
 
 
 
<a name="androidNetCustPlatAPNRunTime"></a><h4>APN configuration at run time</h4> 
 
<p>At runtime, the Android reads APNs from the following file:</p> 
<pre class="prettify"> 
system/etc/apns-conf.xml
</pre> 
 
<p>Android supports the following run-time network configuration methods to choose the appropriate APN from the list of configured APNs:</p> 
<p><ul> 
<li><b>Automatic Configuration</b>: At boot time, Android determines the correct network configuration based on the MCC and MNC from the SIM card and automatically configure all network settings.</li> 
<li><b>Manual Configuration</b>: The platform will also support runtime (user) manual selection of network settings by name, for example, "Company Name US," and will support manual network configuration entry.</li> 
<li><b>WAP / SMS Push Configuration</b>: The network configurations are standard Android resources. You can upgrade a resource at runtime by installing a new system resource APK package. It will be possible to develop a network configuration service which listens to a specific binary SMS port for binary SMS messages containing the network configurations.  NOTE: The implementation will likely be network operator dependent due to inconsistent SMS ports, binary SMS formats, etc.</li> 
</ul> 
 
 
 
 
<a name="androidCustomizingPre-LoadedApps"></a><h3>Customizing pre-loaded applications</h3> 
 
<p>To customize the list of Android packages for a particular product (applications, input methods, providers, services, etc.), set <code>PRODUCT_PACKAGES</code> property in the product configuration, as illustrated below:</p> 
 
<pre class="prettify"> 
PRODUCT_PACKAGES := \
 <company_name>Mail \
    <company_name>IM \
 <company_name>HomeScreen \
 <company_name>Maps \
 <company_name>SystemUpdater
</pre> 
 
<p>Package names should correspond to the <code>LOCAL_PACKAGE_NAME</code> specified for each package's build target. For example, the <code>Android.mk</code> build target for <company_name>Mail, referenced above, could look like this:
 
    # Build the <company_name>Mail application
    LOCAL_PATH:= $(call my-dir)
    include $(CLEAR_VARS)
 
    LOCAL_MODULE_TAGS := user development
 
    LOCAL_SRC_FILES := $(call all-java-files-under,src,tests)
 
    LOCAL_STATIC_JAVA_LIBRARIES := <company_name>login-client
 
    # Specify the package name
    LOCAL_PACKAGE_NAME := <company_name>Mail
 
    # Specify the certificate used to sign the application
    LOCAL_CERTIFICATE := vendor/<company_name>/certs/app
 
    include $(BUILD_PACKAGE)
 
    # Build the login client static library
    include $(LOCAL_PATH)/client/Android.mk
 
<p>Note that the home screen is just an Android application that can be replaced entirely or customized by changing source code and application resources (Java source, layouts, etc.).</p> 
 
 
<a name="androidBrowserBookmarks"></a><h3>Customizing browser bookmarks</h3> 
 
<p>Browser bookmarks are stored as string resources in the Browser application: <code>//android/packages/apps/Browser/res/values/strings.xml</code>.  Bookmarks are defined as simple value string arrays called "bookmarks".  Each bookmark entry is stored as a pair of array values; the first represents the bookmark name and the second the bookmark URL.  For example:</p> 
<pre class="prettify"> 
<!-- Bookmarks -->
<string-array name="bookmarks">
    <item>Google</item>
    <item>http://www.google.com/</item>
    <item>Yahoo!</item>
    <item>http://www.yahoo.com/</item>
    <item>MSN</item>
    <item>http://www.msn.com/</item>
    <item>MySpace</item>
    <item>http://www.myspace.com/</item>
    <item>Facebook</item>
    <item>http://www.facebook.com/</item>
    <item>Wikipedia</item>
    <item>http://www.wikipedia.org/</item>
    <item>eBay</item>
    <item>http://www.ebay.com/</item>
    <item>CNN</item>
    <item>http://www.cnn.com/</item>
    <item>New York Times</item>
    <item>http://www.nytimes.com/</item>
    <item>ESPN</item>
    <item>http://espn.go.com/</item>
    <item>Amazon</item>
    <item>http://www.amazon.com/</item>
    <item>Weather Channel</item>
    <item>http://www.weather.com/</item>
    <item>BBC</item>
    <item>http://www.bbc.co.uk/</item>
</string-array>
</pre> 
<p>Like and Android application resource, the platform will load alternate resources based on the platform configuration values.  See <a href="http://developer.android.com/guide/topics/resources/resources-i18n.html">Resources and Internationalization</a> in the Android SDK for details.  To configure bookmarks for a specific mobile network operator, place your customized bookmarks in a separate <code>strings.xml</code> file and place it under a Mobile Network Code (MNO) specific resource folder.  For example, <code>Browser/res/values-mccXXX-mncYYY/strings.xml</code> where XXX and YYY represent the three-digit MCC and two to three digit MNC values.</p> 
<p>Android loads any configuration-specific resources as override values for the default values, so it is only necessary to include the bookmarks string-array values in this file.</p> 
 
 
 
<a name="androidEmailProviderCustomization"></a>
<h3>Email Provider Customization</h3> 
 
<p>The default email provider settings are stored as string resources in the Email application (<code>//android/packages/apps/Email/res/xml/providers.xml</code>) as illustrated below.</p> 

    <providers>
    
        <!-- Gmail variants -->
        <provider id="gmail" label="Gmail" domain="gmail.com">
            <incoming uri="imap+ssl+://imap.gmail.com" username="$email"/>
            <outgoing uri="smtp+ssl+://smtp.gmail.com" username="$email"/>
        </provider>
        <provider id="googlemail" label="Google Mail" domain="googlemail.com">
            <incoming uri="imap+ssl+://imap.googlemail.com" username="$email"/>
            <outgoing uri="smtp+ssl+://smtp.googlemail.com" username="$email"/>
        </provider>
        <provider id="google" label="Google" domain="google.com">
            <incoming uri="imap+ssl+://imap.gmail.com" username="$email"/>
            <outgoing uri="smtp+ssl+://smtp.gmail.com" username="$email"/>
        </provider>
        <provider id="android" label="Android" domain="android.com">
            <incoming uri="imap+ssl+://imap.gmail.com" username="$email"/>
            <outgoing uri="smtp+ssl+://smtp.gmail.com" username="$email"/>
        </provider></p> 
     
        <!-- Common US providers -->
        
        <provider id="aim" label="AIM" domain="aim.com">
            <incoming uri="imap://imap.aim.com" label="IMAP" username="$email"/>
            <outgoing uri="smtp://smtp.aim.com:587" username="$email"/>
        </provider>
        <provider id="aol" label="AOL" domain="aol.com">
            <incoming uri="imap://imap.aol.com" label="IMAP" username="$email"/>
            <outgoing uri="smtp://smtp.aol.com:587" username="$email"/>
        </provider>
        <provider id="comcast" label="Comcast" domain="comcast.net">
            <incoming uri="pop3+ssl+://mail.comcast.net" username="$user"/>
            <outgoing uri="smtp+ssl+://smtp.comcast.net" username="$user"/>
        </provider>
        <provider id="compuserve" label="CompuServe" domain="cs.com">
            <incoming uri="imap://imap.cs.com" username="$user"/>
            <outgoing uri="smtp://smtp.cs.com" username="$user"/>
        </provider>
        <provider id="dotmac" label=".Mac" domain="mac.com">
            <incoming uri="imap+tls://mail.mac.com" username="$email"/>
            <outgoing uri="smtp+tls://smtp.mac.com" username="$email"/>
        </provider>
        <provider id="earthlink" label="Earthlink" domain="earthlink.net">
            <incoming uri="pop3://pop.earthlink.net" username="$email"/>
            <outgoing uri="smtp://smtpauth.earthlink.net:587" username="$email"/>
        </provider>
        <provider id="juno" label="Juno" domain="juno.com">
            <incoming uri="pop3://pop.juno.com" username="$user"/>
            <outgoing uri="smtp://smtp.juno.com" username="$user"/>
        </provider>
        <provider id="live" label="Windows Live Hotmail Plus" domain="live.com" note="@string/provider_note_live">
            <incoming uri="pop3+ssl+://pop3.live.com" username="$email"/>
            <outgoing uri="smtp+tls+://smtp.live.com" username="$email"/>
        </provider>
        <provider id="hotmail" label="Windows Live Hotmail Plus" domain="hotmail.com" note="@string/provider_note_live">
            <incoming uri="pop3+ssl+://pop3.live.com" username="$email"/>
            <outgoing uri="smtp+tls+://smtp.live.com" username="$email"/>
        </provider>
        <provider id="msn" label="Windows Live Hotmail Plus" domain="msn.com" note="@string/provider_note_live">
            <incoming uri="pop3+ssl+://pop3.live.com" username="$email"/>
            <outgoing uri="smtp+tls+://smtp.live.com" username="$email"/>
        </provider>
        <provider id="mobileme" label="MobileMe" domain="me.com">
            <incoming uri="imap+tls://mail.me.com" username="$email"/>
            <outgoing uri="smtp+tls://smtp.me.com" username="$email"/>
        </provider>
        <provider id="netzero" label="NetZero" domain="netzero.com">
            <incoming uri="pop3://pop.netzero.com" username="$user"/>
            <outgoing uri="smtp://smtp.netzero.com" username="$user"/>
        </provider>
        <provider id="sbcglobal" label="SBC Global" domain="sbcglobal.net">
            <incoming uri="pop3://pop.sbcglobal.yahoo.com" username="$email"/>
            <outgoing uri="smtp://smtp.sbcglobal.yahoo.com" username="$email"/>
        </provider>
        <provider id="verizon" label="Verizon" domain="verizon.net">
            <incoming uri="pop3://incoming.verizon.net" username="$user"/>
            <outgoing uri="smtp://outgoing.verizon.net" username="$user"/>
        </provider>
        <provider id="yahoo" label="Yahoo Plus" domain="yahoo.com" note="@string/provider_note_yahoo">
            <incoming uri="pop3+ssl+://plus.pop.mail.yahoo.com" username="$user"/>
            <outgoing uri="smtp+ssl+://plus.smtp.mail.yahoo.com" username="$user"/>
        </provider>
      
        <!-- Common UK providers -->
        
        <provider id="aol-uk" label="AOL" domain="aol.co.uk">
            <incoming uri="imap+ssl+://imap.uk.aol.com" label="IMAP" username="$user"/>
            <outgoing uri="smtp+ssl+://smtp.uk.aol.com" username="$user"/>
        </provider>
        <provider id="bt" label="BT Internet" domain="btinternet.com">
            <incoming uri="pop3://mail.btinternet.com" username="$email"/>
            <outgoing uri="smtp://mail.btinternet.com" username=""/>
        </provider>
        <provider id="tiscali" label="Tiscali" domain="tiscali.co.uk">
            <incoming uri="pop3://pop.tiscali.co.uk" username="$email"/>
            <outgoing uri="smtp://smtp.tiscali.co.uk" username="$email:wq"/>
        </provider>
        <provider id="yahoo-uk" label="Yahoo" domain="yahoo.co.uk" note="@string/provider_note_yahoo_uk">
            <incoming uri="pop3+ssl+://pop.mail.yahoo.co.uk" username="$user"/>
            <outgoing uri="smtp+ssl+://smtp.mail.yahoo.co.uk" username="$user"/>
        </provider>
        
        <!-- Common Germany providers -->
        
        <provider id="freenet" label="Freenet" domain="freenet.de">
            <incoming uri="pop3://mx.freenet.de" username="$user"/>
            <outgoing uri="smtp+ssl://mx.freenet.de" username="$email"/>
        </provider>
        <provider id="gmx" label="GMX" domain="gmx.de">
            <incoming uri="pop3+tls://pop.gmx.net" username="$email"/>
            <outgoing uri="smtp+tls://mail.gmx.net" username="$email"/>
        </provider>
        <provider id="T-Online" label="T-Online" domain="t-online.de" note="@string/provider_note_t_online">
            <incoming uri="pop3://popmail.t-online.de" username="$email"/>
            <outgoing uri="smtp://smtpmail.t-online.de" username="$email"/>
        </provider>
        <provider id="web.de" label="Web.de" domain="web.de">
            <incoming uri="pop3+tls://pop3.web.de" username="$user"/>
            <outgoing uri="smtp+tls://smtp.web.de" username="$user"/>
        </provider>
    </providers>

<p>As with all Android application resources, the platform will load alternate resources based on the platform configuration values.  See <a href="http://developer.android.com/guide/topics/resources/resources-i18n.html">Resources and Internationalization</a> in the Android SDK for details.  To configure email providers for a specific mobile network operator, place the customized providers in a separate <code>providers.xml</code> file and place it under  a Mobile Network Code (MNO) specific resource folder.  For example, <code>Email/res/xml-mccXXX-mncYYY/providers.xml</code> where XXX and YYY represent the three-digit MCC and two to three digit MNC values.</p> 
 
 
 
<a name="androidThemes"></a><h3>Platform Themes</h3> 
 
 
 
<a name="androidThemesStyles"></a><h4>Themes and Styles</h4> 
 
<p>System level styles are defined in <code>//android/framework/base/core/res/res/values/styles.xml</code>.</p> 
 
 
<a name="androidThemesAnimations"></a><h4>Animations</h4> 
 
<p>Android supports configurable animations for window and view transitions.  System-level animations are defined in XML in global resource files located in <code>//android/framework/base/core/res/res/anim/</code>.</p> 
 
