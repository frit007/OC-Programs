# Websocket Client API

API to connect to a Websocket Server.
Requires a Internet Card and Lua 5.3.


### API-Methods/Functions
* ``` websocket.create(function: callback, [boolean: autoTick]):client ``` -- Creates and returns a new client instance. ```callback``` will get called whenever a event accures. If ```autoTick``` is set to false you will have to call the ```update()``` method manually in order to recieve messages (default: true)
* ``` client:connectURL(string: url) ``` -- Connects to a specific server. Error when it can't connect. Example URL: "ws://example.com/path/to/endoint"
* ``` client:connect(host,port,path,tls) ``` -- Connects to a specific server. If tls is set to true (default: false) the api will use "Fingercomp-libtls" to open a tls connection. Error when it can't connect. The base path is "/" and cannot be empty!
* ``` client:disconnect():boolean``` -- Disconnects the current connection. false if the client was not connected.
* ``` client:update()``` -- Check the input buffer. Will get called automatically by a timer when ```autoTick``` was set to true.
* ``` client:send(string: message) ``` -- Send a messege to the connected server
* ``` client:hasConnection():boolean ``` -- Returns true if there is a open TCP Socket connection to the server.
* ``` client:isConnected():boolean ``` -- Returns true if the Client has a connection to the server and has successfully completed the handshake.


### Callback Events
#### Example callback function 
```
local callback = function(event, var1)
    print("Event "..event.." fired. Var1 = "..var1);
end
local client = websocket.create(callback, true);
```

#### Events
* ```handshake_error(string: error_message)``` -- Handshake with Server failed. The connection gets closed before this event gets fired
* ```close_request(string: reason)``` -- Server has requested to close the connection. The connection gets closed after this event gets fired. ```reason``` can be a empty string if no reason is given.
* ```text(string: message) ``` -- String message recieved
* ```error(string: error_message) ``` -- Error occurred while reading message.
* ```msg_unknown(frame: frame) ``` -- Unknown Frame recieved

### API Limitations
* The API can't handle continuous frames and will fire an ```error```-event after recieving a non final frame (Support maybe in later versions)
* The API can't recieve and send binary messages and will fire an ```msg_unknown```-event after recieving a frame with binary data (Support maybe later version)
* The API will NOT check if the Handshake key is correct and might cause issues (very very very small chance).

### Example
```
local ws = require("websocket_client");
local event = require("event");

local cl = ws.create(function(event ,var1) print(event .. "->" .. var1) end);

cl:connect("localhost", 12345, "/", false);

while true do
  local ev = {event.pull()};

  if ev[1] == "interrupted" then
    cl:disconnect();
    return;
  elseif ev[1] == "touch" then
    cl:send("HI");
  end
end
```

### Install
To install the library just run this command:
```pastebin run xnXssAtH```
