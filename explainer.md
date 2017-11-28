## Explainer

When people are trying to deploy priority for MediaStreamTracks per the [priority model](http://w3c.github.io/webrtc-pc/#priority-and-qos-model) in the spec, they want to experiment with the two parts of prioritization - [local prioritization](https://tools.ietf.org/html/draft-ietf-rtcweb-transports-17#section-4.1) and [QoS prioritization](https://tools.ietf.org/html/draft-ietf-rtcweb-transports-17#section-4.2) - independently.

Adding independent controls to the core specification was [rejected at IETF 100](https://datatracker.ietf.org/meeting/100/materials/minutes-100-rtcweb/), but there seems nothing wrong with specifying an experimental extension to allow people to run trials with this functionality.

### Examples

With the standard API:
<pre>
pc = new RTCPeerConnection();
sender1 = pc.addTrack(track1);
sender2 = pc.addTrack(track2);
parameters = await sender1.getParameters();
parameters.encodings[0].priority = "high";
sender1.encodingParameters.setParameters(parameters);
</pre>
This causes two effects:
- sender1's packets will be marked with a different DSCP code point from sender2's packets
- if congestion occurs across both sender1 and sender2, sender1 will get to send more packets than sender2
The assumption of the core spec is that this is the desired effect, but when experimenting, it can be hard to tell which of these two changes caused a change in performance as seen by the user.

With the API proposed here:

<pre>
pc = new RTCPeerConnection();
sender1 = pc.addTrack(track1);
sender2 = pc.addTrack(track2);
parameters = await sender1.getParameters();
parameters.encodings[0].networkPriority = "high";
sender1.encodingParameters.setParameters(parameters);
</pre>

This will only change the DSCP code points used with sender1, and will have no effect on local prioritization.

Similarly:

<pre>
pc = new RTCPeerConnection();
sender1 = pc.addTrack(track1);
sender2 = pc.addTrack(track2);
parameters = await sender1.getParameters();
parameters.encodings[0].priority = "high";
parameters.encodings[0].networkPriority = "low";
sender1.encodingParameters.setParameters(parameters);
</pre>

This will only change the local scheduling when congestion occurs, and have no effect on DSCP code points.
