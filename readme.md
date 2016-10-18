# Alternative Websocket Effects Manager for Elm

> Websocket (Client) Effects Manager for Elm programs that allows for more sophisticated higher-level protocols. The API requires connections to be explicitly handled by the application. This version provides messages when the connection is lost allowing stateful connection protocols to reinitialize their state, e.g. authentication or subscription.

## Install

### Elm

Since the Elm Package Manager doesn't allow for Native code and this uses Native code, you have to install it directly from GitHub, e.g. via [elm-github-install](https://github.com/gdotdesign/elm-github-install) or some equivalent mechanism.

### Node modules

Since this Effects Manager is for the browser and the native code relies on node-based code, some package manager is required. Webpack is used in the Test application.

You'll also need to install the dependent node modules at the root of your Application Directory. See the example `package.json` for a list of the dependencies.

The installation can be done via `npm install` command.

### Build Test App

The `buildTest.sh` file contains the Webpack command to build the test program.

The output will be in a build directory. This file will be included by the `Test/index.html` file.

## API

### Commands

> Connect to a Websocket Server

This must be done before any other commands are run.

Connections are maintained by the Effect Manager State and are referenced via `url`s.

```elm
connect : ConnectErrorTagger msg -> ConnectTagger msg -> Url -> Cmd msg
connect errorTagger tagger url
```
__Usage__

```elm
connect ConnectError Connect "wss://echo.websocket.org"
```
* `ConnectError` and `Connect` are your application's messages to handle the different scenarios.
* `wss://echo.websocket.org` is the URL to the websocket server

> Send a message to the Websocket Server

Send a message to a specified URL. (A connection must already exist)

```elm
send : SendErrorTagger msg -> SendTagger msg -> Url -> String -> Cmd msg
send errorTagger tagger url message
```
__Usage__

```elm
send SendError Sent "wss://echo.websocket.org" "a string message"
```
* `SendError` and `Sent` are your application's messages to handle the different scenarios
* `wss://echo.websocket.org` is the URL to the websocket server
* `a string message` is the message to send

> Disconnect from a Websocket Server

When a connection is no longer needed, it can be disconnected.

```elm
disconnect : DisconnectErrorTagger msg -> DisconnectTagger msg -> Url -> Cmd msg
disconnect errorTagger tagger url
```
__Usage__

```elm
disconnect ErrorDisconnect SuccessDisconnect "wss://echo.websocket.org"
```

* `ErrorDisconnect` and `SuccessDisconnect` are your application's messages to handle the different scenarios
* `wss://echo.websocket.org` is the URL to the websocket server


### Subscriptions

> Listen for messages and events from a Websocket Server

Listen for messages and events from a specified URL (A connection must already exist)

```elm
listen : ListenErrorTagger msg -> MessageTagger msg -> ConnectionClosedTagger msg -> Url -> Sub msg
listen errorTagger messageTagger connectionClosedTagger url =
```
__Usage__

```elm
listen ListenError Message ConnectionClosed "wss://echo.websocket.org"
```
* `ListenError` is your application's message to handle an error in listening
* `Message` is your application's message to handle received messages
* `ConnectionClosed` is your application's message to handle when the server closes it's connection
* `wss://echo.websocket.org` is the URL to the websocket server

### Messages

#### ConnectErrorTagger

Error when connecting.

```elm
type alias ConnectErrorTagger msg =
    ( Url, ErrorMessage ) -> msg
```

__Usage__

```elm
ConnectError ( url, error ) ->
	let
		l =
			Debug.log "ConnectError" ( url, error )
	in
		model ! []
```

#### ConnectTagger

Successful connection.

```elm
type alias ConnectTagger msg =
    Url -> msg
```

__Usage__

```elm
Connect url ->
	let
		l =
			Debug.log "Connect" url
	in
		{ model | connected = True } ! []
```

#### SendErrorTagger

Error attempting to send.

```elm
type alias SendErrorTagger msg =
    ( Url, Message, ErrorMessage ) -> msg
```

__Usage__

```elm
SendError ( url, message, error ) ->
	let
		l =
			Debug.log "SendError" ( url, message, error )
	in
		model ! []
```

#### SendTagger

Successful send.

```elm
type alias SendTagger msg =
    ( Url, Message ) -> msg
```

__Usage__

```elm
Sent ( url, message ) ->
	let
		l =
			Debug.log "Sent" ( url, message )
	in
		model ! []
```

#### DisconnectErrorTagger

Error when disconnecting.

```elm
type alias DisconnectErrorTagger msg =
    ( Url, ErrorMessage ) -> msg
```

__Usage__

```elm
DisconnectError ( url, error ) ->
	let
		l =
			Debug.log "DisconnectError" ( url, error )
	in
		model ! []
```

#### DisconnectTagger

Successful disconnect.

```elm
type alias DisconnectTagger msg =
    Url -> msg
```

__Usage__

```elm
Disconnect url ->
	let
		l =
			Debug.log "Disconnect" url
	in
		model ! []
```

#### ListenErrorTagger

Error when attempting to listen.

```elm
type alias ListenErrorTagger msg =
    ( Url, ErrorMessage ) -> msg
```

__Usage__

```elm
ListenError ( url, error ) ->
	let
		l =
			Debug.log "ListenError" ( url, error )
	in
		{ model | listenError = True } ! []
```

#### MessageTagger

Message received from server.

```elm
type alias MessageTagger msg =
    ( Url, Message ) -> msg
```

__Usage__

```elm
Message ( url, message ) ->
	let
		l =
			Debug.log "Message" ( url, message )
	in
		model ! []
```

#### ConnectionClosedTagger

Server closed the connection.

```elm
type alias ConnectionClosedTagger msg =
    Url -> msg
```

__Usage__

```elm
ConnectionClosed url ->
	let
		l =
			Debug.log "ConnectionClosed" url
	in
		{ model | connected = False } ! []
```


## Warning

This library is still in alpha.
