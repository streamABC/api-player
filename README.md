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
* a specific radio **station** with all dedicated channels

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
* `metadata` - event fires if new data for the now playing song arrives 
* `metanext` - event fires if new data for the upcoming song arrives 

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

## `sABC.Radio`

This library provides methods to either control a Radioplayer console or a stand-alone player with a skipable radio stream in our streamABC infrastructure.

### Installation

Add a reference to our library to your web application like so:
```html
<script src="//playerservices.streamabc.net/radio.js"></script>
```

> Please load this library with a direct link and don't save local copy to your site. This prevents cross-domain issues makes sure you use the latest version.

**Note:** You don't need to include `sabc.Playerservices` to receive meta data events because `sabc.Radio` uses it behind the scenes and emits the same meta data events as described above.

### Usage

