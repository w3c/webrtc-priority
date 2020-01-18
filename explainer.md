## Explainer

The WebRTC model defined in the IETF includes a priority function, consisting of [local prioritization](https://tools.ietf.org/html/draft-ietf-rtcweb-transports-17#section-4.1) and [QoS prioritization](https://tools.ietf.org/html/draft-ietf-rtcweb-transports-17#section-4.2).

This functionality was part of the [WEBRTC-PC](https://w3c.github.io/webrtc-pc/) specification until late 2019, at which time it was removed for lack of
implementation as part of the preparation for getting WEBRTC-PC to Proposed Recommendation.

Adding independent controls to the core specification was [rejected at IETF 100](https://datatracker.ietf.org/meeting/100/materials/minutes-100-rtcweb/), and this specification was started to give a home for documenting an experimental means of independent control.

As the main spec removed priority, this document was updated to include the base priority definition.

### Examples

Setting local priority and network priority at the same time:
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

Setting network priority only:

<pre>
pc = new RTCPeerConnection();
sender1 = pc.addTrack(track1);
sender2 = pc.addTrack(track2);
parameters = await sender1.getParameters();
parameters.encodings[0].networkPriority = "high";
sender1.encodingParameters.setParameters(parameters);
</pre>

This will only change the DSCP code points used with sender1, and will have no effect on local prioritization.

Setting local priority only:

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
