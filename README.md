# electron-fcm-push-receiver

A module to bring Web Push support to [Electron](https://github.com/electron/electron) allowing it to receive notifications from Firebase Cloud Messaging (FCM).

## Why and how ?

See [this blog post](https://medium.com/@MatthieuLemoine/my-journey-to-bring-web-push-support-to-node-and-electron-ce70eea1c0b0).

## Install

```
npm i -S electron-fcm-push-receiver
```

## Usage

- In `main.js` / in main process :

```javascript
const pushReceiver = require('electron-push-receiver');

// Call it before 'did-finish-load' with mainWindow a reference to your window
pushReceiver.setupPushReceiver(mainWindow.webContents);
```

- In renderer process :

```javascript
import { ipcRenderer } from 'electron';
import {
  START_NOTIFICATION_SERVICE,
  NOTIFICATION_SERVICE_STARTED,
  NOTIFICATION_SERVICE_ERROR,
  NOTIFICATION_RECEIVED as ON_NOTIFICATION_RECEIVED,
  TOKEN_UPDATED,
} from 'electron-fcm-push-receiver/src/constants';

// Listen for service successfully started
ipcRenderer.on(NOTIFICATION_SERVICE_STARTED, (_, token) => // do something);
// Handle notification errors
ipcRenderer.on(NOTIFICATION_SERVICE_ERROR, (_, error) => // do something);
// Send FCM token to backend
ipcRenderer.on(TOKEN_UPDATED, (_, token) => // Send token);
// Display notification
ipcRenderer.on(ON_NOTIFICATION_RECEIVED, (_, notification) => // display notification);
// Start service
ipcRenderer.send(START_NOTIFICATION_SERVICE, senderId);
```
### Reseting the Push Receiver
This plugin has been forked and modified from the original in order to support resetting the plugin for a new user, and turning off notifications for a token.

To reset the receiver, and turn off notifications for any previous token, call this in the main process:
```javascript
pushReceiver.reset();
```

To re-initialize the plugin, call this from the main process:
```javascript
pushReceiver.setup(mMainWindow.webContents);
```
NOTE: You also need to call the above line at setup, when you're creating the window.  It must be initialized before the ``did-load`` event as per original plugin spec.

## Example

Thanks to [CydeSwype](https://github.com/CydeSwype), you can find an example project [here](https://github.com/CydeSwype/electron-fcm-demo).
