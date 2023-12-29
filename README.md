# Phonesocket
JS package for connecting to a server via websocket from phone and desktop.
The package allows you to differentiate between phone connections and desktop connections.
Also includes reconnection handling.

## XSocketServer
XSocketServer is a child class of WebSocketServer from 'ws'.
It provides extra functionality:

- All phones which connect to an XSocketServer are automatically registered as phone devices
- All desktops which connect to and XSocketServer are automatically registered as desktop devices

``` js
# To use import it like
import { XSocketServer } from 'phonesocket/xserver';
```

### Properties and methods
**XSocketServer.register_event(device, event, callback)**
Registers an event to be sent by a particular kind of device and the corresponding callback
```
device = 'phone' | 'desktop'
event: String - name of event sent from client
callback = (ws, data) => {...}
```
**XSocketServer.broadcast_phones(event, data)**
Sends a JSON websocket message of the form {"command": [event], "data": [data]} to all phone connections
```
event: String
data: any
```
**XSocketServer.broadcast_desktops(event, data)**
Sends a JSON websocket message of the form {"command": [event], "data": [data]} to all phone connections
```
event: String
data: any
```
Example
``` js
const xsocketserver = new XSocketServer({port: 8082});

// when a phone sends an event 'toggle_reset'
xsocketserver.register_event('phone', 'toggle_reset', (ws, data) => {
    xsocketserver.broadcast_phones('reset_state', 'not_ready')
    xsocketserver.broadcast_desktops('newgame', Object.keys(coins)) // coins is an object
    ws_send(ws, 'reset_state', true ? 'ready' : 'not_ready')
})
```

**XSocketServer.onclose_extra**
A function that is run upon websocket disconnection

**XSocketServer.onconnect**
A function that is run upon websocket connection

Example
```js
// some dummy code taken from a project
xsocketserver.onclose_extra = (ws) => {
    latent_players[ws.ip] = players[ws.id];
    delete players[ws.id];
}

xsocketserver.onconnect = (ws, req) => {
    if (ws.device === "desktop") {
        sendJSON(ws, 
        {command: "init_players", data: players}, 
        {command: "init_walls", data: walls}, 
        {command: "init_coins", data: Object.values(coins)});
    } else {
        //...
    }
}
```

## XSocketClient
Client side websocket object
Use a tool such as watchify to use node package in client side

### Properties and methods
**XSocketClient.register_event(event, callback)**
Registers an event and a correspondign callback
Example
``` js

const phonesocket = new XSocketClient('phone', `ws://localhost:8082`)
const resetbutton = document.getElementById('resetbutton');
phonesocket.register_event('reset_state', (data) => {
    resetbutton.innerText = data === 'ready' ? 'READY' : '';
})
```

## Functions from index.js

### sendJSON(ws, ...messages)
``` js
xsocketserver.onconnect = (ws, req) => {
    if (ws.device === "desktop") {
        sendJSON(ws, {command: "init_players", data: players});
    }
}
sendJSON(ws, )
```