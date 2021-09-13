---
title: Media Capture and Streams API (Media Stream)
slug: Web/API/Media_Streams_API
tags:
  - API
  - Audio
  - Media
  - Media Capture and Streams API
  - Media Streams API
  - Overview
  - Video
---
<div>{{DefaultAPISidebar("Media Capture and Streams")}}</div>

<p>The <strong>Media Capture and Streams</strong> API, often called the <strong>Media Streams API</strong> or <strong>MediaStream API</strong>, is an API related to <a href="/en-US/docs/Web/API/WebRTC_API">WebRTC</a> which provides support for streaming audio and video data.</p>

<p>It provides the interfaces and methods for working with the streams and their constituent tracks, the constraints associated with data formats, the success and error callbacks when using the data asynchronously, and the events that are fired during the process.</p>

<h2 id="Concepts_and_usage">Concepts and usage</h2>

<p>The API is based on the manipulation of a {{domxref("MediaStream")}} object representing a flux of audio- or video-related data. See an example in <a href="/en-US/docs/Web/API/WebRTC_API/Taking_still_photos#get_the_video">Get the video</a>.</p>

<p>A <code>MediaStream</code> consists of zero or more {{domxref("MediaStreamTrack")}} objects, representing various audio or video <strong>tracks</strong>. Each <code>MediaStreamTrack</code> may have one or more <strong>channels</strong>. The channel represents the smallest unit of a media stream, such as an audio signal associated with a given speaker, like <em>left</em> or <em>right</em> in a stereo audio track.</p>

<p><code>MediaStream</code> objects have a single <strong>input</strong> and a single <strong>output</strong>. A <code>MediaStream</code> object generated by {{domxref("MediaDevices.getUserMedia", "getUserMedia()")}} is called <em>local</em>, and has as its source input one of the user's cameras or microphones. A non-local <code>MediaStream</code> may be representing a media element, like {{HTMLElement("video")}} or {{HTMLElement("audio")}}, a stream originating over the network, and obtained via the WebRTC {{domxref("RTCPeerConnection")}} API, or a stream created using the <a href="/en-US/docs/Web/API/Web_Audio_API">Web Audio API</a> {{domxref("MediaStreamAudioSourceNode")}}.</p>

<p>The output of the <code>MediaStream</code> object is linked to a <strong>consumer</strong>. It can be a media elements, like {{HTMLElement("audio")}} or {{HTMLElement("video")}}, the WebRTC {{domxref("RTCPeerConnection")}} API or a <a href="/en-US/docs/Web/API/Web_Audio_API">Web Audio API</a> {{domxref("MediaStreamAudioSourceNode")}}.</p>

<h2 id="Interfaces">Interfaces</h2>

<p>In these reference articles, you'll find the fundamental information you'll need to know about each of the interfaces that make up the Media Capture and Streams API.</p>

<ul>
 <li>{{domxref("BlobEvent")}}</li>
 <li>{{domxref("CanvasCaptureMediaStreamTrack")}}</li>
 <li>{{domxref("InputDeviceInfo")}}</li>
 <li>{{domxref("MediaDeviceKind")}}</li>
 <li>{{domxref("MediaDeviceInfo")}}</li>
 <li>{{domxref("MediaDevices")}}</li>
 <li>{{domxref("MediaStream")}}</li>
 <li>{{domxref("MediaStreamConstraints")}}</li>
 <li>{{domxref("MediaStreamEvent")}}</li>
 <li>{{domxref("MediaStreamTrack")}}</li>
 <li>{{domxref("MediaStreamTrackEvent")}}</li>
 <li>{{domxref("MediaTrackCapabilities")}}</li>
 <li>{{domxref("MediaTrackConstraints")}}</li>
 <li>{{domxref("MediaTrackSettings")}}</li>
 <li>{{domxref("MediaTrackSupportedConstraints")}}</li>
 <li>{{domxref("NavigatorUserMedia")}}</li>
 <li>{{domxref("NavigatorUserMediaError")}}</li>
 <li>{{domxref("OverconstrainedError")}}</li>
 <li>{{domxref("URL")}}</li>
</ul>

<p>Early versions of the Media Capture and Streams API specification included separate <code>AudioStreamTrack</code> and <code>VideoStreamTrack</code> interfaces—each based upon {{domxref("MediaStreamTrack")}}—which represented streams of those types. These no longer exist, and you should update any existing code to instead use <code>MediaStreamTrack</code> directly.</p>

<h2 id="Events">Events</h2>

<ul>
 <li>{{event("addtrack")}}</li>
 <li>{{event("ended")}}</li>
 <li>{{event("muted")}}</li>
 <li>{{event("overconstrained")}}</li>
 <li>{{event("removetrack")}}</li>
 <li>{{event("started")}}</li>
 <li>{{event("unmuted")}}</li>
</ul>

<h2 id="Guides_and_tutorials">Guides and tutorials</h2>

<p>The articles below provide additional guidance and how-to information that will help you learn to use the API, and how to perform specific tasks that you may wish to handle.</p>

<p>{{LandingPageListSubpages}}</p>

<h2 id="Browser_compatibility">Browser compatibility</h2>

<p>{{Compat("api.MediaStream")}}</p>

<h2 id="See_also">See also</h2>

<ul>
 <li><a href="/en-US/docs/Web/API/WebRTC_API">WebRTC</a> - the introductory page to the API</li>
 <li>{{domxref("mediaDevices.getUserMedia()")}}</li>
 <li><a href="/en-US/docs/Web/API/WebRTC_API/Taking_still_photos">Taking still photos with WebRTC</a>: a demonstration and tutorial about using <code>getUserMedia()</code>.</li>
</ul>