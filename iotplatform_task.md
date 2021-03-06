---

copyright:
  years: 2016, 2018
lastupdated: "2018-08-09"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Connecting devices
{: #iotplatform_task}

Before you can begin receiving data from your IoT devices, you must connect them to  {{site.data.keyword.iot_full}}. Connecting a device to {{site.data.keyword.iot_short_notm}} involves registering the device with {{site.data.keyword.iot_short_notm}} and then using the registration information to configure the device to connect to {{site.data.keyword.iot_short_notm}}.
{:shortdesc}

## Before you begin
{: #byb}

Before you start the connection process, you must ensure that your devices meet the following requirements for communicating with {{site.data.keyword.iot_short_notm}}:

- Your device must be able to communicate by using HTTP or MQTT protocols.
- The device messages must conform to the {{site.data.keyword.iot_short_notm}} message payload requirements.

For more information, see [Developing devices on Watson IoT Platform](https://console.ng.bluemix.net/docs/services/IoT/devices/device_dev_index.html).

Complete the following steps to connect your device to {{site.data.keyword.iot_short_notm}}.

## Step 1: Registering your device with {{site.data.keyword.iot_short_notm}}  
{: #iotplatform_subtask1}

Registering a device involves classifying the device as a device type, giving the device a name, and providing device information. Then you provide a connection token or accept a token that is generated by {{site.data.keyword.iot_short_notm}}.

You can add devices one at a time from the {{site.data.keyword.iot_short_notm}} dashboard, or you can use the [{{site.data.keyword.iot_short_notm}} API ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://docs.internetofthings.ibmcloud.com/apis/swagger/v0002/orgAdmin.html#!/Device_Bulk_Configuration){: new_window} to add one or more devices at a time.

To add a device from the {{site.data.keyword.iot_short_notm}} dashboard:

1. On the IBM Cloud console, select **IoT** from the menu and click the {{site.data.keyword.iot_short_notm}} link.  
2. Log in or click **Sign up to Create**.  
3. On the {{site.data.keyword.iot_short_notm}} page, choose a region, organization and space and click **Create**.  
2. On the service page, click **Launch** to start administering your {{site.data.keyword.iot_short_notm}} organization.

  The {{site.data.keyword.iot_short_notm}} web console opens in a new browser tab at the following URL:

 ```
 https://org_id.internetofthings.ibmcloud.com/dashboard/#/overview
 ```

    Where *org_id* is the ID of [your {{site.data.keyword.iot_short_notm}} organization](iotplatform_overview.html#organizations){: new_window}.

3. In the Overview dashboard, from the menu pane, select **Devices** and then click **Add Device**.
5. Select or create a device type for the device that you are adding.  
Each device that is connected to the {{site.data.keyword.iot_short_notm}} must be associated with a device type. Device types are groups of devices that share common characteristics.  
When you add your first device to your {{site.data.keyword.iot_short_notm}} organization, no device types are available in the **Device type** menu. You must first create a device type:
 1. Click **Create device type**.
 2. Enter a device type name such as `my_device_type` and a description for the device type.   
 **Important:** The device type name must be no more than 36 characters and can contain only:
 <ul>
  <li>Alpha-numeric characters (a-z, A-Z, 0-9)</li>
  <li>Hypens (-)</li>
  <li>Underscores (&lowbar;)</li>
  <li>Periods (.)</li>
  </ul>
 3. Optional: Enter device type attributes and metadata.    
 **Tip:** You can add and edit attributes and metadata later.
 4. Click **Create** to add the new device type.
10. Click **Next** to begin the process of adding your device with the selected device type.
11. Enter a device ID such as `my_first_device`.  
The device ID is used to identify the device in the {{site.data.keyword.iot_short_notm}} dashboard and is also a required parameter for connecting your device to {{site.data.keyword.iot_short_notm}}.  
**Important:** The device ID must be no more than 36 characters and can contain only:
 <ul>
 <li>Alpha-numeric characters (a-z, A-Z, 0-9)</li>
 <li>Hyphens (-)</li>
 <li>Underscores (&lowbar;)</li>
 <li>Periods (.)</li>  
 </ul>
 **Tip:** For network connected devices, the device ID could for example be the device MAC address without any separating colons.  
12. Optional: Click **Additional fields** to add device information, such as serial number, manufacturer, model, and so on.  
 **Tip:** You can add and edit this information later.
12. Optional: Enter device JSON metadata.  
 **Tip:** You can add and edit device metadata later.
13. Click **Next** to complete the addition of your device.
14. Verify that the summary information is correct and then click **Add** to add the connection.  
**Tip:** You have the option to accept an automatically generated authentication token or to provide an authentication token yourself.  
If you choose to create your own token, make sure that it is between 8 and 36 characters long and consists only of alpha-numerical characters and the following special characters:
 - Hyphen (-)
 - Underscore (&lowbar;)
 - Exclamation-point (!)
 - Ampersand (&)
 - At sign (@)
 - Question mark (?)
 - Asterisk (\*)
 - Plus sign (+)
 - Period (.)
 - Right and left parentheses.  

 **Important:** The token must not contain repeated character sequences, dictionary words, user names, or other predefined sequences.
15. In the device information page, copy and save the following device information:  
 - Organization ID, such as `tubo8x`
 - Device Type, such as `my_device_type`
 - Device ID, such as `my_first_device`
 - Authentication Method, such as `token`
 - Authentication Token, such as `PtBVriRqIg4uh)_-Kl`  
  **Tip:** You will need Organization ID, Authentication Token, Device Type, and Device ID to configure your device to connect to {{site.data.keyword.iot_short_notm}}.  

Congratulations, you registered your device. Now you can configure your device to connect to {{site.data.keyword.iot_short_notm}}

## Step 2: Connecting your devices to {{site.data.keyword.iot_short_notm}}
{: #iotplatform_subtask2}

After you register a device with {{site.data.keyword.iot_short_notm}}, you can use the registration information to connect the device and start receiving device data.

{{site.data.keyword.iot_short_notm}} supports many device types. The basic process to connect a device typically includes the following steps:
- Set up your device for MQTT messaging and use Organization ID, Authentication Token, Device Type, and Device ID to authenticate.  
- Send device messages to your {{site.data.keyword.iot_short_notm}} organization by using the MQTT protocol.

**Tip:** Many connection recipes are available for the commonly used devices. For a list of recipes, see the
[Device connection recipes ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://developer.ibm.com/recipes/tutorials/category/internet-of-things-iot/){: new_window} that are available on IBM.com.

The following information is required when connecting your device:
- URL: *org_id*.messaging.internetofthings.ibmcloud.com  
Where *org_id* is the ID of your {{site.data.keyword.iot_short_notm}} organization.
- Port:
 - 1883
 - 8883 (encrypted)
 - 443 (websockets)
- Device identifier: d:*org_id*:*device_type*:*device_id*  
This combination of parameters uniquely identifies your device.
- Username: use-token-auth  
This value indicates that you are using token authorization.
- Password: *Authentication token*  
This value is the unique token that you defined or that was assigned to your device when you registered it.
- Event topic format: iot-2/evt/*event_id*/fmt/*format_string*  
 Where the *event_id* specifies the event name that is shown in {{site.data.keyword.iot_short_notm}}, and *format_string* is the format of the event, such as JSON.
- Message format: JSON  
 {{site.data.keyword.iot_short_notm}} supports several formats, such as JSON and text.

For more information about connecting your device, see [MQTT Connectivity for Devices](devices/mqtt.html) in the technical documentation.

The [Organization Administration ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://docs.internetofthings.ibmcloud.com/apis/swagger/v0002/orgAdmin.html){: new_window} API documentation also contains the required information.

## Restoring deleted devices (Beta)
{: #restore_device}

**Important:** The {{site.data.keyword.iot_short_notm}} restore devices feature is available only as part of a limited beta program. Future updates might include changes that are incompatible with the current version of this feature. Try it out and [let us know what you think ![External link icon](../../icons/launch-glyph.svg "Externl link icon")](https://developer.ibm.com/answers/smart-spaces/17/internet-of-things.html){: new_window}.

If a device deleted by mistake, you can restore it within 14 days. 

When the device is deleted, a device “memento” is created. The memento is a copy of the device document and is available for 14 days, after which it is deleted.

The following Restore a Device API enables you to use the memento to restore a previous version of the device:

    POST /archive/device/types/{typeId}/devices/{deviceId}/restore


You can use the following API to retrieve a list of all of the device mementos:

    GET /archive/device/types/{typeId}/devices/{deviceId}

For more information about the Restore Devices APIs, see [Restore Devices APIs Beta ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://docs.internetofthings.ibmcloud.com/apis/swagger/v0002-beta/restore-device-beta.html).

## Reconnecting devices

Devices might disconnect from {{site.data.keyword.iot_short_notm}} due to a network problem or due to routine maintenance on the service or infrastructure. When the device or devices reconnect, you might want to consider minimizing the amount of network traffic that is generated during a reconnection, or to introduce a time-delay to reduce the number of simultaneous reconnects. 


## Recipes on Connecting Devices

The following recipes describe the complete flow that is used to register and connect devices to Watson IoT Platform.

- [How to Register Devices in IBM Watson IoT Platform ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://developer.ibm.com/recipes/tutorials/how-to-register-devices-in-ibm-iot-foundation/){: new_window}

- [Connecting Raspberry Pi as a Device to Watson IoT using Node-RED ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://developer.ibm.com/recipes/tutorials/deploy-watson-iot-node-on-raspberry-pi/){: new_window}

- [Connect an Arduino Uno device to the IBM Watson IoT Platform ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://developer.ibm.com/recipes/tutorials/connect-an-arduino-uno-device-to-the-ibm-internet-of-things-foundation/){: new_window}

- [Connecting a Sense HAT to Watson IoT using Node-RED ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://developer.ibm.com/recipes/tutorials/connecting-a-sense-hat-to-watson-iot-using-node-red/){: new_window}

- [Connecting Raspberry Pi with Windows IoT Core as a Device to Watson IoT Platform ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://developer.ibm.com/recipes/tutorials/connecting-raspberry-pi-with-windows-iot-core-as-a-device-to-watson-iot-using-node-red/){: new_window}
