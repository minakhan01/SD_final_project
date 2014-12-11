
```javascript
```

Ping/Pong: Send messages from wearscript to python. These messages are sent over a Go server.

```javascript
function main() {
    if (WS.scriptVersion(1)) return;
    WS.say('Welcome to WearScript');
    WS.serverConnect('{{WSUrl}}', function () {

    	// Step 2 & 3 happen in the python code

    	//Step 4: subscribe to pong channel
        WS.subscribe('pong', function (chan, timestamp0, timestamp1, groupDevice) {
            WS.log('Pong: ' + groupDevice + ': Remote - Glass0: ' + (timestamp1 - timestamp0) + ' Glass1 - Glass0: ' + (((new Date).getTime() / 1000) - timestamp0));
        });

        // Step 1: message initiator, publish to ping channel
        setInterval(function () {
        	// note that 'pong' is the resulting channel later used in the client
            WS.publish('ping', 'pong', (new Date).getTime() / 1000);
            WS.log('in ping publish: '+(new Date).getTime() / 1000);
        }, 250);
    });
}
```

```python
# Python: Client
import wearscript
import argparse
# argparse, the recommended command-line parsing module in the Python standard library. 
import time

def callback(ws, **kw):

    def get_ping(chan, resultChan, timestamp):
    	# Step 3: publish messages using ws, i.e. websocket, on resultChan
        ws.publish(resultChan, timestamp, time.time(), ws.group_device)

    # Step 2: receive messages on ping channel and callback get_ping   
    ws.subscribe('ping', get_ping)

    # start handling websocket messages
    ws.handler_loop()

wearscript.parse(callback, argparse.ArgumentParser())
```