# streamABC Player SDKs / APIs

Ver. 1.0.0 12-07-2017

streamABC offers two open Javascript player SDKs to interact with our player related APIs.

* `sABC.Playerservices` - methods for metadata and other meta information for listeners, channels and stations
* `sABC.Radio` - methods to implement a web player that can control skips for Radioplayer or stand-alone players

streamABC player SDKs are implemented as JavaScript libraries and use the `sABC` namespace. You need to include the specific JS file in your web application to use it.

**Compatibility:** All streamABC libraries need a ES5 compatible webbrowser. All recent versions of major browsers are supported. Because we make strong use of Websockets some services will only work in a compatibility mode if no Websockets connection can be established.

## `sABC.Playerservices`

This library provides methods to receive realtime metadata for:
* the **listener** that is connected to our infrastructure
* a specific radio station **channel**
* a specific radio **station** with all belonging channels

The SDK tries to use websockets to provide the best experience with realtime events. If no websocket connection can be established it falls back to polling. Due to limitations of this mode events are not real-time and not all features are available.

> Note: Listener specific meta data is only available if the channel streamed on streamABC infrastructure. All other types can be used with all backed distributors.

### Installation

Add a reference to our library to your web application like so:
```html
<script src="//playerservices.streamabc.net/sabc.js"></script>
```

> Please load this library with a direct link and don't save local copy to your site. This prevents cross-domain issues makes sure you use the latest version.

### Usage

Create an instance of `sABC.Playerservices` with calling `sABC.newPlayerservices(options?: PlayerservicesConfig)` somewhere in your code:
```javascript
var sabcService = sABC.newPlayerservices({
    debug: false,
    apikey: "abc"
});
```

The function argument is a object with config values:


config key | type       | default value | description
:----------|:-----------|:--------------|:-----------
`debug`    | `boolean`  | `false`       | enables debug output in browser console
`apikey`   | `string`   |               | api key provided by streamABC

After you created your instance you can attach event listeners to receive metadata events and start/stop receiving data. 

### Attach Event Listeners

`sABC.Playerservices` emits the following event types:
name          | arguments       | description
:-------------|:----------------|:-----------
`metadata`    | JSON object with meta data         | fires if new meta data for current song is available
`metanext`    | JSON object with meta data         | fires if new meta data for next song is available 
`error`  | error | fires if an error ocurred, the response object property type defines if the error is on the websocket side (ws) or audio side (audio)

You can listen to one or both events:
```javascript
sabcService.on("metadata", function(data) {
    console.log(data)
});

sabcService.on("metanext", function(data) {
    console.log(data)
});
```

The payload `data` of both events contains all available metadata, for example:
```json
{ 
    "mount": "/sABCTest-sabcfm-mp3-256-5131639",
    "channel": "sABC-live", 
    "timestamp": "2017-07-12T16:28:13.452Z", 
    "session": "1cf9dc2a-530b-48a1-9979-f1ed1bcc719a",
    "rawdata": "LCD Soundsystem - New York, I Love You but You're Bringing Me Down",
    "artist": "LCD Soundsystem", 
    "song": "New York, I Love You but You're Bringing Me Down", 
    "type": "now",
    "meta": [
        {
            "ASIN": "B001QC8V4I",
            "artist": "LCD Soundsystem",
            "image": "https://images-eu.ssl-images-amazon.com/images/I/517o3A%2BQ%2BsL._SL160_.jpg",
            "largeimage": "https://images-eu.ssl-images-amazon.com/images/I/517o3A%2BQ%2BsL.jpg",
            "trackurl": "https://www.amazon.de/York-Love-Youre-Bringing-Down/dp/B001QC8V4I?SubscriptionId=AKIAIWBEZNVKMHRC5YOA&tag=momusicle-21&linkCode=xm2&camp=2025&creative=165953&creativeASIN=B001QC8V4I",
            "type": "amazon"
        },
        {
            "artist": "LCD Soundsystem",
            "artisturl": "https://itunes.apple.com/de/artist/lcd-soundsystem/id29525428?uo=4",
            "image": "http://is5.mzstatic.com/image/thumb/Music/v4/4f/e9/df/4fe9df4a-0ae2-0d17-58ce-edfd5f3385c2/source/100x100bb.jpg",
            "largeimage": "https://images-eu.ssl-images-amazon.com/images/I/517o3A%2BQ%2BsL.jpg",
            "trackurl": "http://is5.mzstatic.com/image/thumb/Music/v4/4f/e9/df/4fe9df4a-0ae2-0d17-58ce-edfd5f3385c2/source/600x600bb.jpg",
            "previewurl": "http://audio.itunes.apple.com/apple-assets-us-std-000001/AudioPreview71/v4/72/63/ad/7263adf0-8314-fd7a-20ff-004e121c1fb8/mzaf_6600252372906776921.plus.aac.p.m4a",
            "trackurl": "https://itunes.apple.com/de/album/new-york-i-love-you-but-youre-bringing-me-down/id742432549?i=742434985&uo=4",
            "type": "itunes"
        }
    ]
}
```

### Start receiving user specific meta data

To receive listener specific meta data the first step is to decorate the streaming url with our listener parameter. There is a convinience utility method `decorateStreamURL(url: string): string` that does exactly this:
```javascript
var streamURL = sabcService.decorateStreamURL("http://sabc-test.stream.vip/1/mp3-256/");
``` 

You can then start receiving meta data events:
```javascript
var streamURL = sabcService.startMeta();
``` 

> Note: The stream has to be played using the decorated stream URL to receive meta data events.

### Start receiving channel or station events

Because meta data is not user specific in this mode, you don't need to decorate your stream URL.

You can start receiving events with one of this methods:
* `startMetaStation(station: string): void` - receive meta data for all channels of a station
* `startMetaChannel(channel: string): void` - receive meta data for one channel

> Please use the channel key or station key provided by streamABC. You can find this information in your streamABC management console.

```javascript
sabcService.startMetaChannel("sABC-live");
```

### Stop receiving events and error handling

To stop receive meta data events you can use `stopMeta()` method.
It doesn't matter what mode you use.

The SDK tries to handle connection errors as good as possible and reconnects if a websocket connection is lost.

Error events are emitted if the SDK detects a connection error. A detailed error message is provided as an argument to this event.

> Be aware, that error events are emitted even though the SDK tries to get around it by itself, e.g. falling back to polling if websockets don't work.


## `sABC.Radio`

This library provides methods to either control a Radioplayer console or a stand-alone player with a skipable radio stream in our streamABC infrastructure.

A reference implementation for Radioplayer console can be found in this [repository](https://github.com/streamABC/skiponradio-radioplayer).

### Installation

Add a reference to our library to your web application like so:
```html
<script src="//playerservices.streamabc.net/radio.js"></script>
```

> Please load this library with a direct link and don't save local copy to your site. This prevents cross-domain issues makes sure you use the latest version.

**Note:** You don't need to include `sabc.Playerservices` to receive meta data events because `sabc.Radio` uses it behind the scenes and emits the same meta data events as described above.

### Usage

Disable the default Radioplayer init call:
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