
```javascript
```

### Event handling in WearScript:

I the following WearScript code, I am handling gesture and speech events for glass using callbacks. I have shown the use of anonymous and named callbacks, and an example of closre. Please see the commented code below for more details.

```javascript
// Possible gestures: LONG_PRESS, SWIPE_DOWN, SWIPE_LEFT, SWIPE_RIGHT, TAP, TWO_TAP
// THREE_LONG_PRESS, THREE_TAP, TWO_LONG_PRESS, TWO_SWIPE_RIGHT, TWO_SWIPE_UP, 
WS.gestureCallback('onGestureTAP', function () {
    // Stream camera frames (can view in the Images tab)
    WS.cameraOn(0.25);
});

// Named callback
var speech_callback=function (data) {
    WS.say('you said ' + data);
    function write(){
        // closure example: data variable is available to inner function
        WS.log('speech: ' + data);
    }
    write();
}

WS.gestureCallback('onGestureTWO_TAP', function () {
    var in_gesture='TWO TAP';
    //  Named callback speech_callback
    WS.speechRecognize('Say Something', speech_callback);
});

WS.gestureCallback('onGestureLONG_PRESS', function () {
    WS.cameraVideo();
});
```

### Publish/Subscribe in WearScript:

#### Methods

* ```publish(String channel, args...) : void```: publish messages on channel with optional arguments
* ```subscribe(String channel, Function callback) : void```: subscribe to messages on channel with a defined callback

#### Subscribe to Image/Sensor stream:

WearScript initiates image and sensor streams, and python client subscribes to these channels. Please see the code and comments below.

WearScript Javascript:

```javascript
function main() {
    if (WS.scriptVersion(1)) return;
    WS.serverConnect('{{WSUrl}}', function () {

    	// initialize sensor streams
        WS.sensorOn('accelerometer', .25);

        // initialize image stream
        WS.cameraOn(1);

        WS.dataLog(false, true, .15);
    });
}
```

Python client: run using command line ```python <filename>.py client <WS end point>``` 

```python
# Python: Client or Server
import wearscript
import argparse


def callback(ws, **kw):

    def get_image(chan, timestamp, image):
        print('Image[%s] Time[%f] Bytes[%d]' % (chan, timestamp, len(image)))

    def get_sensors(chan, names, samples):
        print('Sensors[%s] Names[%r] Samples[%r]' % (chan, names, samples))

    # callback: get_image, subscribe to image stream channel  
    ws.subscribe('image', get_image)

    # callback: get_sensors, subscribe to image sensors channel 
    ws.subscribe('sensors', get_sensors)

    # start handling websocket messages
    ws.handler_loop()

wearscript.parse(callback, argparse.ArgumentParser())
```

#### Ping/Pong (Publish/Subscribe): 
Send *ping* message from Wearscript to Python client and *pong* message from python client to Wearcript. These messages are sent over a Go server. Please see the code and comments below.

WearScript Javascript:

```javascript
function main() {
    if (WS.scriptVersion(1)) return;
    WS.say('Welcome to WearScript');
    WS.serverConnect('{{WSUrl}}', function () {

    	// Step 2 & 3 happen in the python code

    	//Step 4: subscribe to pong channel
        WS.subscribe('pong', function (chan, timestamp0, timestamp1, groupDevice) {
            WS.log('Pong: ' + groupDevice + ': Remote - Glass0: ' + (timestamp1 -
             timestamp0) + ' Glass1 - Glass0: ' + (((new Date).getTime() / 1000) - timestamp0));
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

Python client: run using command line ```python <filename>.py client <WS end point>```

```python
# Python: Client
import wearscript
import argparse
# argparse, the recommended command-line parsing module in the Python standard library. 
import time

def callback(ws, **kw):
	# Step 1 and 4 are in JS wearscript
    def get_ping(chan, resultChan, timestamp):
    	# Step 3: publish messages using ws, i.e. websocket, on resultChan
        ws.publish(resultChan, timestamp, time.time(), ws.group_device)

    # Step 2: receive messages on ping channel and callback get_ping   
    ws.subscribe('ping', get_ping)

    # start handling websocket messages
    ws.handler_loop()

wearscript.parse(callback, argparse.ArgumentParser())
```