## `sABC.Radio`

This library provides methods to either control a Radioplayer console or a stand-alone player with a skipable radio stream in our streamABC/QuantumCast infrastructure.

A reference implementation for Radioplayer console can be found in this [repository](https://github.com/streamABC/skiponradio-radioplayer).

### Installation

Add a reference to our library to your web application like so:
```html
<script src="//playerservices.streamabc.net/radio.js"></script>
```

> Please load this library with a direct link and don't save local copy to your site. This prevents cross-domain issues makes sure you use the latest version.

**Note:** You don't need to include `sabc.Playerservices` to receive meta data events because `sabc.Radio` uses it behind the scenes and emits the same meta data events as described above.

### Usage

If used in a Radioplayer console, disable the default Radioplayer init call (omit this if used in stand-alone mode):
```javascript
//radioplayer.init();
```

Create an instance of `sABC.Radio` with calling `sABC.newRadio(options?: sABC.RadioConfig)` somewhere in your code:
```javascript
var sabcRadio = sABC.newRadio({
    debug: false,
    apikey: "abc",
    radioplayer: true
});
```            

Set `radioplayer: false` if used in stand-alone mode. All other functions keep the same in both modes.

Set a channel with `sabcRadio.setChannel(channel: sABC.Channel, defaultQuality: string)`:
```javascript
sabcRadio.setChannel({
    "skip": true,
    "streamurl": {
        "lq": "http://sabc-test.stream.vip/1/mp3-256/",
        "hq": "http://sabc-test.stream.vip/1/mp3-256/"
    },
    "name": "streamABC LIVE",
}, "lq");
``` 

> Note: Stream URLs are set with a dedicated quality. This is basically a string that you can use to differentiate between qualities. You have to provide the
used quality as the second argument to `setChannel`.

Attach all event listeners you want to use. The most used events are `metadata`, `metanext`, `skippable`, `skip`, `play`. A full list of all events can be found below.

```javascript
sabcRadio.on("metadata", function(data) {
    // do something with data
})

sabcRadio.on("skippable", function(skippable) {
    // skippable is a boolean indicating that you can skip
})
```

If you attached all event listeners you can finally init the channel and start playing:
```javascript
sabcRadio.init().then(function () {
    // here you can add code that should be executed if the init process is finished
});
```

The Radioplayer now plays your channel with the stream url from your channel config.

To initiate a skip you can call the `.skip()` method. To return to the live channel use `.live()`. You can add these calls to click events:
```javascript

$("#SkipButton").on("click",function(e){
    e.preventDefault();
    sabcRadio.skip();
});

```

### Events

name          | arguments       | description
:-------------|:----------------|:-----------
`metadata`    | JSON object with meta data         | fires if new meta data for current song is available
`metanext`    | JSON object with meta data         | fires if new meta data for next song is available 
`skippable`   | boolean, true if skippable         | fires skippable state changes
`streamtype`   | boolean, true = user stream, false = live stream         | stream state changes
`loading`   | -         | stream is loading
`ready`     | -         | stream is loading
`play`   | { skipped: boolean, channel: channeldata}         | stream starts playing
`stop`   | channeldata         | stream stops playing
`playing`   | duration         | duration played in seconds
`skip`   | channeldata         | fires after a skip
`error`  | { type: ws\|audio, err: error} | fires if an error ocurred, the response object property type defines if the error is on the websocket side (ws) or audio side (audio)
