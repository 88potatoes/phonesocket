# Phonesocket
JS package for connecting to a server via websocket from phone and desktop.
The package allows you to differentiate between phone connections and desktop connections.
Also includes reconnection handling.

## XSocketServer
XSocketServer is a child class of WebSocketServer from 'ws'.
It provides extra functionality:
- keeps track of phone connections and desktop connections
- stores events for handling phone clients and desktop clients

### Properties and methods
```
# register_event(device, event, callback)
# device = 'phone' | 'desktop'
# event: String - name of event sent from client
# callback = (ws, data) => {...}
```
Example
```
const xsocketserver = new XSocketServer({port: 8082});

xsocketserver.register_event('phone', 'toggle_reset', (ws, data) => {
    xsocketserver.broadcast_phones('reset_state', 'not_ready')
    xsocketserver.broadcast_desktops('newgame', Object.keys(coins))
    ws_send(ws, 'reset_state', players[ws.id].reset_agree ? 'ready' : 'not_ready')
    }
)
```


## Functions from index.js

### sendJSON(ws, ...messages)
```
xsocketserver.onconnect = (ws, req) => {
    if (ws.device === "desktop") {
        sendJSON(ws, {command: "init_players", data: players});
    }
}
sendJSON(ws, )
```