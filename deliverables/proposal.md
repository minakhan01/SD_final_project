## CS215 Software Design: Project Proposal
### By Mina Khan

### Topic of Study: 
#### Event-driven programming using event handlers, callbacks and publish-subscribe pattern

In computer programming, event-driven programming is a programming paradigm in which the flow of the program is determined by events—i.e., sensor outputs or user actions (mouse clicks, key presses) or messages from other programs or threads [[1]](http://www.princeton.edu/~achaney/tmve/wiki100k/docs/Event-driven_programming.html). Event handlers are a common manifestation of event-driven programming, and these are widely used in web applications.

Publish/Subscribe pattern fall under the category of event-driven programming and is closed realted to observer pattern. Publish–subscribe is a messaging pattern where senders of messages, called publishers, do not program the messages to be sent directly to specific receivers, called subscribers. Instead, published messages are characterized into classes, without knowledge of what, if any, subscribers there may be. Similarly, subscribers express interest in one or more classes, and only receive messages that are of interest, without knowledge of what, if any, publishers there are. [[2]](http://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern)

### Application of project: 
#### Sending messages to Glass using WearScript.js
Event-driven progamming is effective for Google glass because like glass applications repond to user actions, for example when the user taps the glass, the glass can make a sound and/or call another function to change glass display. I shall use [WearScript.js](http://www.wearscript.com/en/latest/index.html#) for getting basic functionality on Glass. WearScripts run on Glass in a WebView and supports standard browser functionality. I shall be using [WearScript web API](https://api.wearscript.com) for development. 
I am shooting for basic functionality since I am new to WearScript, but I am hopeful that I shall be able to accomplish something demostrable since I am familiar with Javascript and HTML used in basic Wearscript gists.

### Programming language: 
#### Javscript and HTML
Javascript allows for asynchronous events, which is important since the event-driven architecture is also inherently asynchronous as event producers and event consumers are not strongly coupled and may not have a synchronous event exchange.[[3]](http://berb.github.io/diploma-thesis/original/055_events.html)

### Additional resources:
* [WearScript documentation](https://media.readthedocs.org/pdf/wearscript/latest/wearscript.pdf)
