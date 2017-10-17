# streamABC Player SDKs / APIs

Ver. 1.0.0 12-07-2017

streamABC offers two open Javascript player SDKs to interact with our player related APIs.

* [`sABC.Playerservices`](./Docs-Playerservices.md) - methods for metadata and other meta information for listeners, channels and stations
* [`sABC.Radio`](./Docs-Radio.md) - methods to implement a web player that can control skips for Radioplayer or stand-alone players

streamABC player SDKs are implemented as JavaScript libraries and use the `sABC` namespace. You need to include the specific JS file in your web application to use it.

**Compatibility:** All streamABC libraries need a ES5 compatible webbrowser. All recent versions of major browsers are supported. Because we make strong use of Websockets some services will only work in a compatibility mode if no Websockets connection can be established.

[Documentation for sABC.Playerservices](./Docs-Playerservices.md) 
[Documentation for sABC.Radio](./Docs-Radio.md)
