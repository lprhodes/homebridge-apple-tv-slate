---
title: Homebridge Appele TV Plugin

language_tabs: # must be one of https://git.io/vQNgJ
  - json

toc_footers:
  - <a href='https://github.com/lprhodes/homebridge-apple-tv'>Back to GitHub</a>

includes:

search: false
---

# Homebridge Apple TV

## Introduction
Welcome to the Apple TV plugin for [Homebridge](https://github.com/nfarina/homebridge).

This plugin allows you to control your Apple TV with HomeKit using the Home app and Siri.


## Like this plugin?

If you like this plugin and want to show your support then please star the Github repo, or better yet; buy me a drink using [Paypal](https://paypal.me/lprhodes) or [crypto currency](https://goo.gl/bEn1RW).

Working on open source projects like this is full-time for me so every bit helps.

Thank you, sincerely!

## Newsletter

You can keep informed about HomeKit, homebridge and homebridge plugins by subscribing to my [Works with](http://workswith.io) newsletter.

# Requirements

- Requires Node.js >= 7.6.0
- If you're seeing something like `ERROR LOADING PLUGIN"..."async setCharacteristicValue (props, value, callback) {` then please check your node version before creating a new issue: `node -v`.
- There's a lot of people here who have never seen config files like these before, so here's a quick tip; when you have the option of putting true or false as a value, do not put quotation marks around it. e.g. { example: false } not { example: "false" }.

# Installation

> Example Apple TV platform in the homebridge configuration when Apple TV's haven't been paired yet:


```json
  ...
  "platforms": [
    {
      "platform":"AppleTV",
      "name":"Apple TV",
      "devices": [ ],
      "accessories": [
        {
          "deviceID":"lounge",
          "name":"Up",
          "command": "up"
        }
      ]
    }
  ] 
  ...
```

> Example Apple TV platform in the homebridge configuration including paired Apple TV's:

```json
  ...
  "platforms": [
    {
      "platform":"AppleTV",
      "name":"Apple TV",
      "devices": [
        {
          "id": "lounge",
          "name": "Lounge Apple TV",
          "credentials": "..."
        }
      ],
      "accessories": [
        {
          "deviceID":"lounge",
          "name":"Up",
          "command": "up"
        }
      ]
    }
  ] 
  ...
```

1. Install homebridge using the [directions on the project page](https://github.com/nfarina/homebridge)
2. Install this plugin using: `npm install -g homebridge-apple-tb`
3. Update your homebridge `config.json` file. See [config-sample.json](config-sample.json) for examples.

<aside class="success">When you run homebridge, your Apple TV will be automatically discovered.</aside>


# Advanced Setup
## Homebridge Config Location
Generally the plugin can find where your `config.json` lives as it commonly sits in the `.homebridge` directory of your user account. If you're doing something out of the ordinary however you may need to override this. You can do so by adding a `"homebridgeDirectory": "/path/to/config.json` key/value pair to the platform config.


## Apple TV Discovery

> Include paired Apple TV's in the top of the platform config (not in any of the accessories).

```json
...
"devices": [
  {
    "id": "lounge",
    "name": "Lounge Apple TV",
    "credentials": "..."
  }
],
...
```

Apple TV's are automatically discovered when the plugin runs. Each discovered Apple TV will add a "Pair" switch to the Home app. You can then use this switch to pair with the Apple TV.

When you toggle a "Pair" switch on, the Apple TV will show a pairing code. At the same time, a message will appear in the homebridge terminal asking you to type in the code. Once the correct code has been entered the credentials will be provided within an example JSON snippet that you can copy and paste into the `"devices"` option as shown on the right.

You can hide "Pair" switches by adding `"showPairSwitches": false` to the config.

# Commands

## Supported Apple TV Commands

The following commands are supported by the plugin:

command | description
------- | -----------
up | Performs the up key
down | Performs the down key
left | Performs the left key
right | Performs the right key
menu | Performs the menu key
play | Plays the current audio/video key
pause | Pauses the current audio/video
select | Performs the select key
top_menu | Goes to the app screen on the Apple TV or the TV app depending on how the button is configured in the setting.
tv | The same as `top_menu`
mic | Simulates the press of the mic button
siri | The same as `mic`
next | Skips to the next song or skips forward in a video
previous | Returns to the previous song or goes backwards in a video
sleep | Sends the Apple TV to sleep
suspend | The same as `sleep`
stop | Stops a song or video

## Simple Command Structure

> Command sent when switch is turned on.


```json
{
  ...
  "command": "select"
  ...
}
```

The `"command"` option may be either a single command or an advanced command structure as shown below. 

# Advanced Command Structure

Command objects allow multiple commands to be sent with `"repeat"`, `"interval"` and `"pause"` options.

## Repeat

> Example

```json
{
  ...
  "command": [
    {
      "command": "menu",
      "repeat": 3,
      "interval": 0.3
    }
  ]
  ...
}

```

> Result

```json
Send "menu"
Wait 0.3 seconds
Send "menu"
Wait 0.3 seconds
Send "menu"
```

Commands can be repeated multiple times. A delay can be added between each send of the command by adding an `"interval"` option.


## Multiple Commands

> Example

```json
{
  ...
  "command": [
    {
      "command": "down",
      "pause": 0.3
    },
    {
      "command": "right",
      "pause": 0.3
    },
    {
      "command": "select",
    }
  ]
  ...
}
```

> Result

```json
Send "down"
Wait 0.3 seconds
Send "right"
Wait 0.3 seconds
Send "select"
```

You may wish for different commands to be sent at once. A delay can be added between each command by adding a `"pause"` option.

## Multiple Commands & Repeats

> Example

```json
{
  ...
  "command": [
    {
      "command": "down",
      "repeat": 3,
      "interval": 0.1,
      "pause": 0.3
    },
    {
      "command": "right",
      "pause": 0.3
    },
    {
      "command": "select",
    }
  ]
  ...
}
```

> Result

```json
Send "down"
Wait 0.1 seconds
Send "down"
Wait 0.1 seconds
Send "down"
Wait 0.3 seconds
Send "right"
Wait 0.3 seconds
Send "select"
```

You may wish for different commands to be sent at once, with some repeating. A delay can be added between each command by adding a `"pause"` option. A delay can be added between each repeat of the same command by adding an `"interval"` option.

# Accessories

Each accessory will show up in the Home app as a switch. Turn the switch on and the command is sent. The switch will toggle off automatically once the command has been sent. 

key | description | example | default | required | unit tested
--- | ----------- | ------- | ------- | -------- | -----------
`name` | A descriptor for the accessory that will show in HomeKit apps. | "TV" | - | Yes | No
`command` | A single command or an advanced command structur. | See above. | - | Yes | No
`deviceID` | The `id` of the Apple TV as defined in the `devices` config option. | "lounge" | - | Yes | No
`enableAutoOff` | Turn the switch off automatically after the command has been sent. | false | true | No | No
`disableLogs` | Disables the log output for this accessory. | true | false | No | No
`debug` | Outputs some additional logs, useful for figuring out issues. | true | false | No | No

## Special Thanks
Thanks to @edc1591 (https://github.com/edc1591/node-appletv) who saved me a lot of time by creating a node module for controlling the Apple TV.