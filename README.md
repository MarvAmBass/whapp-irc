# whapp-irc
_a simple whatsapp web <-> irc gateway_

## information
- private chats;
- group chats, with op for admins;
- kicking, inviting, and stuff;
- LIST, WHO (with online/offline state);
- joining chats;
- converts names to irc safe names as much as possible;
- receiving files, hosts it as using a HTTP file server;
- receiving locations, will send a Google Maps link to the location;
- receiving reply messages;
- generating QR code;
- saves login state to disk;
- replay using `whapp-irc/replay` capability;
- IRCv3 `server-time` support;
- no configuration needed;
- probably some stuff I forgot.

## configuration
whapp-irc provides two services. HTTP (port: `3000`) for Pictures/QR-Code etc. and IRC (port: `6060`) which is the gateway to WhatsApp.

### irc client
To use whapp-irc optimally you should set the following client capabilities:
- `server-time` (this will show the time when the message was sent in whatsapp
	in your IRC client, instead of when the bridge received it);
- `whapp-irc/replay` (this will replay all the messages the bridge missed, for
	example: when the bridge is turned off. The bridges stores the timestamp of
	the last message for every chat on disk and will send all newer messages to
	the client).

### environment variables
All configuration is done using environment variables.

Quick and simple.

- `HOST`
    - the IP/domain which is used when HTTP Fileserver URLs are generated
    - _optional_
    - _default:_ `localhost`
    
- `FILE_SERVER_PORT`:
    - port which is used when HTTP Fileserver URLs are generated
    - _optional_
    - _default:_ `3000`

- `IRC_SERVER_PORT`:
    - the port to listen on for IRC connections
    - _optional_
    - _default:_ `6060`

- `LOG_LEVEL`
    - `normal` - normal logging output
    - `verbose` - will log all communication between whapp-irc and the chromium instance
    - _optional_
    - _default:_ `normal`

- `MAP_PROVIDER`
    - The map provider to use for location messages
    - can be one either `googlemaps` or `openstreetmap`
    - _optional_
    - _default:_ `googlemaps`

### volumes

- `/root`
    - all data which should be persistent is stored beneath `/root`.
    - _If you want persistance you should map this folder into your docker container._

## docker
It's recommend to use the docker image.
It's also the only supported version, since this way we have a consistent,
predictable and reproducible version.

To run:
```
docker run -d \
	--name whapp-irc \
	-v "$HOME/.whapp-irc:/root" \
	-p 6667:6060 \
	-p 3000:3000 \
	-e "HOST=localhost" \
	lieuwex/whapp-irc
```

## local build
make sure you have go and dep, then clone the repo in your `$GOPATH` and:
```shell
dep ensure
go build
./whapp-irc
```
