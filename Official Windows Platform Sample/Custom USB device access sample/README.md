# Custom USB device access sample
## Requires
- Visual Studio 2013
## License
- MS-LPL
## Technologies
- Windows Runtime
## Topics
- usb
- Devices and sensors
## Updated
- 11/25/2013
## Description

<div id="mainSection">
<p>This sample shows how to communicate with a USB device by using the <a href="http://msdn.microsoft.com/library/windows/apps/dn278466">
<b>Windows.Devices.Usb</b></a> namespace. The sample can communicate with these devices:
</p>
<ul>
<li>The OSR USB FX2 learning kit. You can get the learning kit from <a href="http://www.osronline.com/">
OSR Online</a>. </li><li>The SuperMUTT device. You can purchase the device from <a href="http://jjgtechnologies.com/mutt.htm">
JJG Technologies</a>. You must update the device firmware before using it (discussed later).
</li></ul>
<p></p>
<p>The sample demonstrates these key scenarios:</p>
<ul>
<li><a href="http://msdn.microsoft.com/library/windows/apps/dn303343">How to connect to a USB device</a>
</li><li><a href="http://msdn.microsoft.com/library/windows/apps/dn303347">How to send a USB control transfer</a>
</li><li><a href="http://msdn.microsoft.com/library/windows/apps/dn303348">How to send a USB interrupt transfer</a>
</li><li><a href="http://msdn.microsoft.com/library/windows/apps/dn303346">How to send a USB bulk transfer</a>
</li><li><a href="http://msdn.microsoft.com/library/windows/apps/dn303344">How to get USB descriptors</a>
</li><li><a href="http://msdn.microsoft.com/library/windows/apps/dn303345">How to select a USB interface setting</a>
</li><li>How to handle app suspension and resume events </li><li>How to implement AutoPlay activation </li></ul>
For step-by-step instructions about implementing USB features in a Windows Store app, see
<a href="http://msdn.microsoft.com/library/windows/apps/dn312121">Talking to USB devices, start to finish</a>.
<p></p>
<p>
<table>
<tbody>
<tr>
<th>Windows runtime class</th>
<th>Description</th>
</tr>
<tr>
<td>App</td>
<td>
<p>Invoked when the sample app is activated. The OnActivated implementation causes the app to get launched when the device is connected to the system. When the app is activated, the user is shown information about which device launched the app.</p>
</td>
</tr>
<tr>
<td>DeviceListEntry</td>
<td>
<p>This class stores <a href="http://msdn.microsoft.com/library/windows/apps/br225393">
<b>DeviceInformation</b></a> objects associated with each device, dynamically detected by the
<a href="http://msdn.microsoft.com/library/windows/apps/br225446"><b>DeviceWatcher</b></a> object. This class is used by the UI to display device-specific information. For example, the UI uses this class to get the device interface path so that the user can
 identify the device and use it for data transfers.</p>
</td>
</tr>
<tr>
<td>EventHandlerForDevice</td>
<td>
<p>This class implements all event handlers required by the sample app. There two types of events that the app handles:
</p>
<ul>
<li>App events:
<p>Windows can suspend, resume, or terminate an app as a result of a change in system state or user action. Windows notifies the app about the state through
<b>Suspending</b> and <b>Resuming</b> events. By implementing handlers, the app can save app or session data before the state changes. In the sample, the app stops all
<a href="http://msdn.microsoft.com/library/windows/apps/br225446"><b>DeviceWatcher</b></a> objects on suspension. Otherwise,
<b>DeviceWatcher</b> continues to raise events even when the app is suspended. On resume, the app starts them.
</p>
<p>In order to serialize events, on suspension, this class calls a registered callback (provided by a scenario). That allows the app to respond to app suspension before the app closes the device.</p>
<p>Additionally, when an app suspends, the API releases the <a href="http://msdn.microsoft.com/library/windows/apps/dn263883">
<b>UsbDevice</b></a> object. To avoid using a stale reference on resume, the sample explicitly closes the device in its
<b>Suspending</b> event handler. In the <b>Resuming</b> handler, the sample opens a handle to the device and obtains a new reference to the
<b>UsbDevice</b> object.</p>
</li><li><a href="http://msdn.microsoft.com/library/windows/apps/br225446"><b>DeviceWatcher</b></a> events:
<p>The sample registers for these <a href="http://msdn.microsoft.com/library/windows/apps/br225446">
<b>DeviceWatcher</b></a> events: <a href="http://msdn.microsoft.com/library/windows/apps/br225450">
<b>Added</b></a>, <a href="http://msdn.microsoft.com/library/windows/apps/br225453">
<b>Removed</b></a>, and <a href="http://msdn.microsoft.com/library/windows/apps/br225451">
<b>EnumerationCompleted</b></a>. Detects when the current connected device is removed or reconnected. It then opens the device and obtains a
<a href="http://msdn.microsoft.com/library/windows/apps/dn263883"><b>UsbDevice</b></a> reference and releases that reference on disconnect.
</p>
<p>The app calls the when EventHandlerForDevice:OpenAsync to open the device. The class also handles the surprise remove event. When the physical device is device unplugged, the
<a href="http://msdn.microsoft.com/library/windows/apps/dn263883"><b>UsbDevice</b></a> is released. On reconnect, EventHandlerForDevice opens the device again.</p>
</li></ul>
The class also holds a reference to the currently connected device for which the class handles events. The app can get a reference to the EventHandlerForDevice singleton and can access the
<a href="http://msdn.microsoft.com/library/windows/apps/dn263883"><b>UsbDevice</b></a> by using EventHandlerForDevice::Current-&gt;Device.
<p></p>
</td>
</tr>
<tr>
<td>Scenario1_DeviceConnect</td>
<td>
<p>This class implements methods that use the <a href="http://msdn.microsoft.com/library/windows/apps/br225446">
<b>DeviceWatcher</b></a> object to dynamically detect devices (see EventHandlerForDevice). That reference is used through the sample to interact with the device. When finished, the sample closes the device by releasing that reference.</p>
<p>When the class is instantiated, the app creates <a href="http://msdn.microsoft.com/library/windows/apps/br225446">
<b>DeviceWatcher</b></a> objects for SuperMUTT and OSRFX2 devices. Those types of devices are detected by their respective
<b>DeviceWatcher</b> objects. </p>
</td>
</tr>
<tr>
<td>Scenario2_ControlTransfer</td>
<td>
<p>This class implements methods that demonstrate how to initialize USB setup packets and send control transfers to the device. The sample changes the blink pattern (SuperMUTT) and seven segment display (OSRFX2) by using control transfers.</p>
</td>
</tr>
<tr>
<td>Scenario3_InterruptPipes</td>
<td>
<p>This class implements methods that write to a USB interrupt pipe (only applies to SuperMUTT)and read from another interrupt pipe as data arrives in the pipe. To read data, the sample registers an event handler that gets invoked every time there is data to
 read. In the sample's event handler, it reads the received data and displays number of times it read data.</p>
</td>
</tr>
<tr>
<td>Scenario4_BulkPipes</td>
<td>
<p>The class implements methods to read and write data to and from USB bulk pipes. The sample demonstrates how to start and cancel asynchronous operations for bulk transfers. After the transfer is complete, the sample displays the number of bytes read or written.
 Data is read and written by using <a href="http://msdn.microsoft.com/library/windows/apps/br208119">
<b>DataReader</b></a> and <a href="http://msdn.microsoft.com/library/windows/apps/br208154">
<b>DataWriter</b></a> objects.</p>
</td>
</tr>
<tr>
<td>Scenario5_UsbDescriptors</td>
<td>
<p>The class implements methods that display all USB descriptors associated with the current device. The sample displays the device descriptor, configuration descriptor, interface descriptors for all settings and their endpoint descriptors.</p>
<p>It also shows how to obtain the entire USB configuration descriptor as a raw descriptor and use the
<a href="http://msdn.microsoft.com/library/windows/apps/br208119"><b>DataReader</b></a> to read bytes from the descriptor.
</p>
</td>
</tr>
<tr>
<td>Scenario6_InterfaceSetting</td>
<td>
<p>This class implements methods that demonstrate how to enumerate USB alternate settings for an interface, get an active setting and, select an alternate setting. Selecting an alternate setting applies to the SuperMUTT device. Retrieving number of interface
 settings (drop down list) and getting active interface setting applies to OSRFX2 and SuperMUTT devices.
</p>
</td>
</tr>
<tr>
<td>Scenario7_Sync</td>
<td>
<p>This class implements methods that syncs data with the device in a background task. Because only one process can access
<a href="http://msdn.microsoft.com/library/windows/apps/dn263883"><b>UsbDevice</b></a> object, the app closes the previous reference obtained in Scenario1_DeviceConnect and opens the device again after the task is complete. Sync completion is is notified though
 a registered callback registered by the app. </p>
<p>For more information, see <a href="http://go.microsoft.com/fwlink/?LinkID=306314">
this topic</a> about sync-ing with a device.</p>
</td>
</tr>
</tbody>
</table>
</p>
<p><b>App manifest package </b></p>
<p>The sample adds the <a href="http://msdn.microsoft.com/library/windows/apps/br211430">
<b>DeviceCapability</b></a> element in the Package.appxmanifest file. Device information includes the device's vendor/product Ids and device class information. For OSRFX2 and SuperMUTT devices, the device class is specified by indicating the device class code
 and a string for the code.</p>
<pre class="syntax"><code>  &lt;Capabilities&gt;
      &lt;!--When the device's classId is FF * *, there is a predefined name for the class. You can use the name instead of the class id. 
          There are also other predefined names that correspond to a classId.--&gt;
      &lt;m2:DeviceCapability Name=&quot;usb&quot;&gt;
          &lt;!--OSRFX2 Device--&gt;
          &lt;m2:Device Id=&quot;vidpid:0547 1002&quot;&gt;
              &lt;m2:Function Type=&quot;classId:ff * *&quot;/&gt;
              &lt;!--&lt;m2:Function Type=&quot;name:vendorSpecific&quot;/&gt;--&gt;
          &lt;/m2:Device&gt;
          &lt;!--SuperMutt Device--&gt;
          &lt;m2:Device Id=&quot;vidpid:045E 0611&quot;&gt;
              &lt;!--&lt;m2:Function Type=&quot;classId:ff * *&quot;/&gt;--&gt;
              &lt;m2:Function Type=&quot;name:vendorSpecific&quot;/&gt;
          &lt;/m2b:Device&gt;
      &lt;/m2:DeviceCapability&gt;
  &lt;/Capabilities&gt;</code></pre>
<h2><a id="related_topics"></a>Related topics</h2>
<dl><dt><a href="http://msdn.microsoft.com/library/windows/apps/dn278466"><b>Windows.Devices.Usb</b></a>
</dt><dt><a href="http://msdn.microsoft.com/library/windows/apps/dn303355">Writing a Windows store app for a USB device</a>
</dt><dt><a href="http://go.microsoft.com/fwlink/p/?LinkID=227694">Windows 8 app samples</a>
</dt></dl>
<h2>Related technologies</h2>
<a href="http://msdn.microsoft.com/library/windows/apps/dn278466"><b>Windows.Devices.Usb</b></a>
<p>Provides Windows Runtime classes and enumerations that a Windows store app can use to communicate with an external USB device that uses WinUSB (Winusb.sys) as the device driver.
</p>
, <a href="http://msdn.microsoft.com/library/windows/apps/br225459"><b>Windows.Devices.Enumeration</b></a>
<p>Provides Windows Runtime classes for device discovery and notifications about devices for apps that use them.
<a href="http://msdn.microsoft.com/library/windows/apps/br224847"><b>Windows.ApplicationModel.Background</b></a>
</p>
, <a href="http://msdn.microsoft.com/library/windows/apps/br224847"><b>Windows.ApplicationModel.Background</b></a>
<p>Enables an app to schedule background tasks to run app code even when the app is suspended.
</p>
, <a href="http://msdn.microsoft.com/library/windows/apps/br225446"><b>DeviceWatcher</b></a>
<p>Enumerates devices dynamically, so that the app receives notifications if devices are added, removed, or changed after the initial enumeration is complete.
</p>
, <a href="http://msdn.microsoft.com/library/windows/apps/br208119"><b>DataReader</b></a>
<p>Reads data from an input stream. Used for reading data from a USB pipe.</p>
, <a href="http://msdn.microsoft.com/library/windows/apps/br208154"><b>DataWriter</b></a>
<p>Writes data to an output stream. Used for writing data to a USB pipe.</p>
, <a href="http://msdn.microsoft.com/library/windows/apps/hh452731">Auto-launching with AutoPlay</a>
<p>Use AutoPlay to provide your app as an option when a user connects a device to their computer.
</p>
<h2>Operating system requirements</h2>
<table>
<tbody>
<tr>
<th>Client</th>
<td><dt>Windows&nbsp;8.1 </dt></td>
</tr>
<tr>
<th>Server</th>
<td><dt>Windows Server&nbsp;2012&nbsp;R2 </dt></td>
</tr>
</tbody>
</table>
<h2>Build the sample</h2>
<h2><a id="Driver_requirements"></a><a id="driver_requirements"></a><a id="DRIVER_REQUIREMENTS"></a>Driver requirements</h2>
<p>The sample app communicates with the device through the Microsoft-provided kernel-mode driver, Winusb.sys. You must install it as the device driver.
</p>
<p>Hardware manufacturers can specify Winusb.sys as the device driver in either of these two ways:</p>
<ul>
<li>By providing a custom INF that references the Microsoft-provided Winusb.inf file. For more information, see
<a href="http://msdn.microsoft.com/library/windows/apps/ff540283">WinUSB (Winusb.sys) Installation</a>.
</li><li>By setting Microsoft operating system (OS) feature descriptors that report the compatible ID as &quot;WINUSB&quot;. In this case, Windows matches the compatible ID with the driver and automatically loads Winusb.sys as the device driver. For more information, see
<a href="http://msdn.microsoft.com/library/windows/apps/hh450799">WinUSB Device</a>.
</li></ul>
When you connect your device, you might notice that Windows loads Winusb.sys automatically. Otherwise follow these instructions to load the driver:
<ol>
<li>Open Device Manager and locate the device. </li><li>Right-click the device and select <b>Update driver software...</b> from the context menu.
</li><li>In the wizard, select <b>Browse my computer for driver software</b>. </li><li>Select <b>Let me pick from a list of device drivers on my computer</b>. </li><li>From the list of device classes, select <b>Universal Serial Bus devices</b>. </li><li>The wizard displays <b>WinUsb Device</b>. Select it to load the driver. </li></ol>
<p></p>
<h2><a id="If_you_are_using_the_OSRFX2__device..."></a><a id="if_you_are_using_the_osrfx2__device..."></a><a id="IF_YOU_ARE_USING_THE_OSRFX2__DEVICE..."></a>If you are using the OSRFX2 device...</h2>
<p>The preceding procedure does not add a device interface GUID for the app to access the device. You must add the GUID manually by following this procedure.</p>
<ol>
<li>Load the driver as described in the preceding procedure. </li><li>Generate a device interface GUID for the OSRFX2 device, by using a tool such as guidgen.exe.
</li><li>Find the registry key for your OSRFX2 device under this key. In this example, VID and PID of the device is VID_0547&amp;PID_1002:
<p><b>HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Enum\USB\VID_0547&amp;PID_1002</b></p>
</li><li>Under the <b>Device Parameters</b> key, add a String registry entry named <b>
DeviceInterfaceGUID</b> or a Multi-String entry named <b>DeviceInterfaceGUIDs</b>. Set the value to the GUID you generated in step 2.
</li><li>Disconnect the device from the system and reconnect it to the same physical port.
<p class="note"><b>Note</b>&nbsp;&nbsp;If you change the physical port then you must repeat steps 1 through 4.</p>
</li></ol>
<h2><a id="If_you_are_using_the_SuperMUTT_device..."></a><a id="if_you_are_using_the_supermutt_device..."></a><a id="IF_YOU_ARE_USING_THE_SUPERMUTT_DEVICE..."></a>If you are using the SuperMUTT device...</h2>
<p>Windows automatically loads Winusb.sys as the device driver. Otherwise, manually install the driver by using the preceding instructions.</p>
<ol>
<li>Download and install the <a href="http://msdn.microsoft.com/en-us/library/windows/hardware/jj590752.aspx">
MUTT Software Package</a>. </li><li>Open a command prompt and run the MuttUtil tool included in the package. Use the tool to update the firmware:
<p><code>MuttUtil.exe –forceupdatefirmware</code></p>
</li><li>By using the MuttUtil tool, change the device mode to WinRTUsbPersonality:
<p><code>MuttUtil.exe –SetWinRTUsb</code></p>
<p>The SuperMUTT device when configured in WinRTUsbPersonality mode, exposes configuration, interfaces, and endpoints, that work with the sample.
</p>
</li></ol>
<h2><a id="Update_and_deploy_device_metadata"></a><a id="update_and_deploy_device_metadata"></a><a id="UPDATE_AND_DEPLOY_DEVICE_METADATA"></a>Update and deploy device metadata</h2>
<p>These build instructions are only for building this sample so it can be tested locally with OSRFX2 and SuperMUTT devices. The device metadata for two devices is included in this project.</p>
<ul>
<li>
<p>SuperMUTT device metadata file: 8E5ADE77-7398-458A-B12D-C2706D7C03CE.devicemetadata-ms
</p>
</li><li>
<p>OSRFX2 device metadata file: 1BA8367E-1D19-4217-93BB-B10408850DE4.devicemetadata-ms;
</p>
</li></ul>
<p class="note"><b>Note</b>&nbsp;&nbsp; If you are authoring device metadata for your own device, you must also ensure that the Package name, Publisher name, and App ID are in sync with those that you registered with the Windows Store.</p>
<p>The images in this section show how to create a device metadata package. If you are opening an existing package, information in the package should be populated in the wizard.
</p>
<p class="proch"><b>To create and install the device metadata package by using the
<b>Device Metadata Authoring Wizard</b>, follow these instructions:</b></p>
<ol>
<li>Open the <b>Device Metadata Authoring Wizard</b>. You can open the tool in either of these ways:
<ul>
<li>If you have the Windows Driver Kit (WDK) installed, open <b>Driver</b> &gt; <b>
Device Metadata</b> &gt; <b>Authoring</b>. </li><li>If you have the Standalone SDK installed, the tool is located at <i>&lt;install_path&gt;</i>\bin\x86\DeviceMetadataWizardexe.
</li></ul>
</li><li>Check <b>Other</b> in <b>Device Categories</b>.
<p><img src="102711-image.png" alt="" align="middle">
</p>
</li><li>On the <b>Locales</b> page, Check the locale for which you want the package.
<p><img src="102712-image.png" alt="" align="middle">
</p>
</li><li>On the <b>Device Info</b> page, enter <b>Model Name</b>, <b>Manufacturer</b>, and
<b>Description</b>.
<p><img src="102713-image.png" alt="" align="middle">
</p>
</li><li>On the <b>Hardware Info</b> page, enter the hardware ID of your device.
<p><img src="102714-image.png" alt="" align="middle">
</p>
</li><li>On the <b>App Info</b> page, clear the <b>Package name</b>, <b>Publisher name</b>, and
<b>Windows Store App ID</b>.
<p><img src="102715-image.png" alt="" align="middle">
</p>
</li><li>On the <b>Windows Info</b> page, add information about the Package name, Publisher name, and App ID. That information is registered with the Windows Store. This is required for AutoPlay.
<p><img src="102716-image.png" alt="" align="middle">
</p>
<p>Notice that in the preceding image, <b>Enable AutoPlay for registered apps</b> is checked. That option allows other registered-apps to participate in AutoPlay. The AutoPlay feature for this sample is enabled through the Package.appxmanifest file (discussed
 later). </p>
</li><li>Open the <b>Finish</b> tab. Select the <b>Copy packages to your system's local metadata store</b> check box.
<p><img src="102717-image.png" alt="" align="middle">
</p>
</li></ol>
<p class="note"><b>Note</b>&nbsp;&nbsp;</p>
<p class="note">The device metadata package generates an Experience GUID that must match the
<b>Device event</b> value under the <b>Declarations</b> tab of the Package.appxmanifest. When the device is connected to the system, the app is associated and launched automatically.</p>
<p></p>
<p>Alternatively, you can manually install the device metadata.</p>
<p class="proch"><b>To manually install the device metadata package, follow these instructions:</b></p>
<ol>
<li>Enable test signing on your computer. Open a command window as Administrator. Enter the following command.
<p><b>bcdedit /set TESTSIGNING ON</b></p>
</li><li>Copy device metadata files to this location:
<ul>
<li>
<p>SuperMUTT device metadata file: 8E5ADE77-7398-458A-B12D-C2706D7C03CE.devicemetadata-ms
</p>
</li><li>
<p>OSRFX2 device metadata file: 1BA8367E-1D19-4217-93BB-B10408850DE4.devicemetadata-ms
</p>
</li></ul>
<p>%PROGRAMDATA%\Microsoft\Windows\DeviceMetadataStore\<i>&lt;locale&gt;</i></p>
<p>Where, <i>&lt;locale&gt;</i> specifies the locale such as en-US.</p>
</li></ol>
<p>Now that device metadata package is installed, you can test it. </p>
<p class="proch"><b>To verify that the device metadata is loaded, follow these instructions:</b></p>
<ol>
<li>Connect the device to the computer. </li><li>In Control Panel, open <b>View devices and printers</b> and verify that the icon of the device is this image:
<p><img src="102718-image.png" alt="" align="middle">
</p>
</li><li>Verify that the device description is:
<p><b>Device Metadata Package Sample for </b><i>&lt;device&gt;</i></p>
Where, <i>&lt;device&gt;</i> is SuperMUTT or OSRFX2. </li></ol>
<p><b>Troubleshooting</b></p>
<p>If the device metadata in <b>View devices and printers</b> does not show the expected icon and description,
</p>
<ol>
<li>Open <b>View devices and printers</b> and remove the device. </li><li>Clear the device store cache. </li><li>Delete all files/folders under:
<p>%PROGRAMDATA%\Microsoft\Windows\DeviceMetadataCache\</p>
</li><li>Connect the device and verify that the correct icon and description is shown in the UI.
</li></ol>
<p></p>
<h2><a id="Customizing_the_sample_for_your_device_"></a><a id="customizing_the_sample_for_your_device_"></a><a id="CUSTOMIZING_THE_SAMPLE_FOR_YOUR_DEVICE_"></a>Customizing the sample for your device
</h2>
<p>You can extend this sample for your device by performing these tasks.</p>
<ol>
<li>Create a <a href="http://msdn.microsoft.com/library/windows/apps/br225446"><b>DeviceWatcher</b></a> object for your device in Scenario1_DeviceConnect.
</li><li>Add information about your device in the <a href="http://msdn.microsoft.com/library/windows/apps/br211430">
<b>DeviceCapability</b></a> in the Package.appxmanifest file. The information must be added under the
<b>DeviceCapability</b> element with <b>Name</b> attribute set to &quot;usb&quot;.
<p class="note"><b>Note</b>&nbsp;&nbsp;You cannot modify the USB device capability in Microsoft Visual Studio&nbsp;2013. You must right-click the Package.appxmanifest file in
<b>Solution Explorer</b> and select <b>Open With...</b>, and then <b>XML (Text) Editor</b>. The file opens in plain XML.
</p>
<p>Add information about the device under DeviceCapability.</p>
<pre class="syntax"><code>          
      &lt;wb:DeviceCapability Name=&quot;usb&quot;&gt;
          &lt;!--OSRFX2 Device--&gt;
          ....
          &lt;!--SuperMutt Device--&gt;
          ....
          &lt;!--Your Device--&gt;
          &lt;wb:Device Id=&quot;vidpid:XXXX XXXX&quot;&gt;
              &lt;wb:Function Type=&quot;classId:XX * *&quot;/&gt;
          &lt;/wb:Device&gt;
      &lt;/wb:DeviceCapability&gt;
</code></pre>
If you specify the device class code, make sure that it is supported. For a list of supported device classes see
<a href="http://msdn.microsoft.com/library/windows/apps/">Updating the app manifest package for a USB device</a>.
</li></ol>
<p></p>
<h2><a id="Enabling_AutoPlay"></a><a id="enabling_autoplay"></a><a id="ENABLING_AUTOPLAY"></a>Enabling AutoPlay</h2>
<p>The AutoPlay feature is enabled in the App manifest. </p>
<p>The sample is one the recommended app in the, review those settings and related code:</p>
<ol>
<li>In Visual Studio&nbsp;2013, open Package.appxmanifest in the visual editor. </li><li>On the <b>Declarations</b> tab, select the <b>AutoPlay Device</b> declaration.
</li><li>The <b>Verb</b> setting identifies a value that is passed to your app for the selected option. The value is passed to the app when the
<b>OnActivated</b> handler is invoked (See AutoPlay.cpp). By accessing the <i>args-&gt;Verb</i> value, the app notifies the user.
</li><li>The <b>Action display name</b> setting identifies the string that AutoPlay displays for your app.
</li><li>In the <b>Device event</b> value, the ExperienceID GUID must match the ExperienceID in your device's StoreManifest file generated by the
<b>Device Metadata Authoring Wizard</b>. </li><li>The app must implement how it should respond when launched. For code example, see AutoPlay.cpp/cs. For the JavaScript version of the sample, see default.js (ready: method). If the app does not handle the event, the app will not launch and show the content.
</li></ol>
<img src="102719-image.png" alt="" align="middle">
<p></p>
<h2><a id="Building_the_Sample"></a><a id="building_the_sample"></a><a id="BUILDING_THE_SAMPLE"></a>Building the Sample</h2>
<p>To build this sample, open the solution (.sln) file titled CustomUsbDeviceAccess.sln from Visual Studio&nbsp;2013. Press F7 or go to
<b>Build</b>-&gt;<b>Build Solution</b> from the top menu after the sample has loaded.</p>
<h2>Run the sample</h2>
<ol>
<li>To run this sample after building it, press F5 (run with debugging enabled) or Ctrl-F5 (run without debugging enabled), or select the corresponding options from the
<b>Debug</b> menu. To deploy the app, select <b>Build</b> &gt; <b>Deploy CustomUsbDeviceAccess</b>.
</li><li>Select a USB device. To determine the device to open, view the device interface path shown in the
<b>Output</b> string. </li><li>Click the <b>Connect to device</b> button. </li><li>Choose one of the scenarios in the <b>Select scenario</b> input box: either <b>
1) Connecting To Device</b> or <b>2) Control Transfer</b>, and so on. </li><li>To find devices, select <b>1) Connecting To Device</b> and click the <b>Start Device Watcher</b> to find devices. Select the device from the list to open the device.
<p><img src="102720-image.png" alt="" align="middle">
</p>
</li><li>To send a USB vendor control transfer, select <b>2) Control Transfer</b>. Set the blink pattern to initiate a transfer.
<p><img src="102721-image.png" alt="" align="middle">
</p>
</li><li>To perform interrupt data transfers, select <b>3) Interrupt Pipes</b>. For the SuperMUTT device, you can read and write data. To write data, click the
<b>Write To Interrupt Out</b> button. The transfer writes 1024 bytes to the interrupt OUT endpoint. Number of bytes is the maximum packet size supported by the endpoint. To read from the device, click
<b>Register For Interrupt Event</b>. This starts transfer from the interrupt IN endpoint. The UI shows the number of bytes read and the count of events received.
<p><img src="102722-image.png" alt="" align="middle">
</p>
<p>For the OSR FX2 board, you can only read data. To read interrupt data click <b>
Register For Interrupt Event</b> and then press switches on the board to generate data.</p>
<p><img src="102723-image.png" alt="" align="middle">
</p>
</li><li>To perform bulk transfers, select <b>3) Bulk Pipes</b>. Click the appropriate button to start the transfer. You can cancel all transfers by clicking the
<b>Cancel all Read/Write tasks</b> button.
<p><img src="102724-image.png" alt="" align="middle">
</p>
<p><img src="102725-image.png" alt="" align="middle">
</p>
<img src="102726-image.png" alt="" align="middle"> </li><li>To view USB descriptors, select <b>4) Getting Descriptors</b>. Select the type of descriptor you want to get. The descriptor data is shown on the UI.
<p><img src="102727-image.png" alt="" align="middle">
</p>
</li><li>To select an alternate setting, select <b>6) Selecting Interface Settings</b>. This scenario is only enabled for the SuperMUTT device because the device supports two identical settings for demo purposes.
<p><img src="102728-image.png" alt="" align="middle">
</p>
</li><li>To start sync with a device in a background task, select <b>7) Sync with Device</b>.
<p>The app closes the previous reference and reopens it because the sync scenario runs in a separate process. As a result, the user is prompted to allow access to the device. The output string shows the device instance of the device that is closed.</p>
<p><img src="102729-image.png" alt="" align="middle">
</p>
<p>To cancel an in-progress sync operation, select <b>Cancel Sync with the device</b>.
</p>
<p>The OSR FX2 board can hold up to 2048 bytes. If the buffer is full, subsequent writes wait until the buffer can fit more data.</p>
</li></ol>
</div>
