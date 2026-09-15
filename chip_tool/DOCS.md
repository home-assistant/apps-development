# Home Assistant App: CHIP Tool

The CHIP Tool app provides the Matter chip-tool controller directly in Home Assistant.

It is intended for Matter development, testing, debugging, and advanced troubleshooting. The app gives you access to the chip-tool command line through Home Assistant, without having to build and install the Matter CHIP Tool separately.

Note: This app is currently experimental and is intended primarily for advanced users. {"fallbackMarkdown":"(GitHub
)","reference":{"matched_text":"","prefix":null,"start_idx":1030,"end_idx":1047,"safe_urls":["https://raw.githubusercontent.com/home-assistant/apps-development/master/chip_tool/config.yaml"],"refs":[],"alt":"(GitHub
)","prompt_text":null,"type":"grouped_webpages","error":null,"items":[{"title":"","url":"https://raw.githubusercontent.com/home-assistant/apps-development/master/chip_tool/config.yaml","attribution":"GitHub","pub_date":null,"snippet":null,"attribution_segments":null,"supporting_websites":[],"refs":[{"turn_index":1,"ref_type":"view","ref_index":0}],"hue":null,"attributions":null}],"style":null,"status":"done","fallback_items":null},"showLoginRequiredCard":false}

What is CHIP Tool?

chip-tool is a Matter controller. It can be used to:

- commission Matter devices;
- communicate with commissioned Matter devices;
- read and write Matter attributes;
- send Matter commands;
- subscribe to Matter attributes and events;
- test and troubleshoot Matter devices.

The CHIP Tool itself is part of the Matter SDK. The Home Assistant app provides a convenient way to run it from your Home Assistant system. For a complete reference, see the Matter project's Working with the CHIP Tool documentation.

## Installation

Use the following steps to install this app.

Click the Home Assistant My button below to open the app page in your Home Assistant instance.

If this is the first app you install from the apps-development repository, Home Assistant will ask you to add the repository. Click Add.

Click Install to install the app.

Start the app once the installation is complete.

## Using CHIP Tool

After starting the app, open CHIP Tool from the Home Assistant sidebar.

The app provides a terminal where you can run chip-tool commands.

To display the available commands, run:

chip-tool

chip-tool displays the available command groups and options. You can use these commands to discover the functionality supported by the installed version of the Matter SDK.

For example, to see the commands available for the onoff cluster:

chip-tool onoff


To see the available commands for a specific operation:

chip-tool onoff on

Commissioning a Matter device

Before you can control a Matter device with chip-tool, the device must be commissioned.

For example, if you have a Matter QR code or manual pairing code, you can use:

chip-tool pairing code <node_id> <qr_code_or_manual_code>


For example:

```
chip-tool pairing code 1 MT:Y.K9042C00KA0648G00
```

Where:

<node_id> is the ID you assign to the Matter device.
<qr_code_or_manual_code> is the QR code payload or manual pairing code from the device.

Other commissioning methods are available for different network configurations, including Wi-Fi, Thread, and commissioning through a proxy.

For detailed commissioning instructions, see the Matter Working with the CHIP Tool documentation.

Controlling a Matter device

Once a device has been commissioned, you can use chip-tool to interact with the Matter clusters implemented by the device.

For example, a device implementing the On/Off cluster can be controlled with:
```
chip-tool onoff on <node_id> <endpoint_id>
```

To turn it off:
```
chip-tool onoff off <node_id> <endpoint_id>

```


To read the current On/Off state:
```
chip-tool onoff read on-off <node_id> <endpoint_id>
```

The exact commands available depend on the device type and the Matter clusters implemented by the device.

Finding commands and options
```
chip-tool contains a large number of Matter commands.
```
You can use the command line itself to discover what is available.

List all top-level commands:

chip-tool


List the commands available for a cluster:
```
chip-tool <cluster>

```

For example:
```
chip-tool onoff

```

List the options for a command:
```
chip-tool onoff on
```

This is often the easiest way to determine the parameters required by a command.

For the complete list of supported commands, options, interactive mode, subscriptions, and advanced features, see the Matter Working with the CHIP Tool documentation.

Matter applications and tools

The Matter SDK contains many applications and development tools. Their names can be confusing at first because not all of them are controllers.

The important distinction is:

chip-tool is the Matter controller. It is used to commission and control Matter devices.
The other applications are Matter examples. They implement different device types or services and are mainly intended for development and testing.
Controller and development tools
Application	Purpose
chip-tool	Matter controller for commissioning and controlling devices
chip-cert	Matter certificate and certification tool
Device examples
Application	Device type
air-purifier	Air purifier
air-quality-sensor	Air quality sensor
all-devices	Multiple device types in a single application
closure	Gate, garage door, or other closure
contact-sensor	Contact sensor
dishwasher	Dishwasher
evse	Electric vehicle supply equipment
light	Light
lock	Door lock
microwave-oven	Microwave oven
refrigerator	Refrigerator
rvc	Robotic vacuum cleaner
thermostat	Thermostat
tv-app	TV application
tv-casting-app	TV Casting application
water-heater	Water heater
water-leak-detector	Water leak detector
Energy and network examples
Application	Purpose
energy-gateway	Electrical energy pricing and grid conditions
energy-management	Energy management
network-manager	Thread network manager
OTA examples
Application	Purpose
ota-provider	Provides over-the-air firmware updates
ota-requestor	Requests over-the-air firmware updates
Commissioning example
Application	Purpose
terms-and-conditions	Demonstrates terms and conditions during commissioning

You do not need to install these other examples to use the CHIP Tool app. The Home Assistant app provides the chip-tool controller. The other applications are separate Matter examples used for development and testing.

Configuration

This app currently has no configuration options.

Troubleshooting
A command fails

Start by checking the command syntax:

chip-tool <command>


You can also run the command without all its arguments to display the expected parameters.

Make sure that:

the Matter device is powered on and reachable;
the device has been commissioned when the command requires an existing Matter session;
the node ID is correct;
the endpoint ID is correct;
the command is supported by the device.
I need to commission a device

The commissioning process depends on the device's network and the commissioning method being used.

The Matter CHIP Tool documentation covers commissioning over:

Bluetooth LE;
Wi-Fi;
Thread;
IP;
a commissioning proxy.

See the Working with the CHIP Tool documentation for the complete procedure.

I need advanced CHIP Tool features

The Matter documentation also covers:

single-command and interactive modes;
reading and writing attributes;
subscribing to attributes and events;
testing Matter devices;
multi-admin scenarios;
wildcard commands;
advanced command options.
Support

For questions about the Home Assistant app, you can get help from the Home Assistant community:

Home Assistant Discord
Home Assistant Community Forum
Home Assistant subreddit

If you have found a bug in the app, please open an issue in the apps-development repository.

For questions about chip-tool itself or Matter protocol behavior, refer to the Matter project's CHIP Tool documentation.
