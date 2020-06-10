## Explainer

When sending multiple streams of media or data over a limited capacity channel, it is common that some of the streams are more important than others.

If the network or the sender has to decide to limit some streams, the user will often have an opinion of which streams should be sent in full quality, and which streams can safely be relegated to a lower quality, slower delivery or otherwise treated as less important.

When the need for prioritization is visible inside the sending computer, this can be a local decision; if it occurs in the network, where streams of media or data from multiple different sources all contribute to possible congestion, there needs to be markings on the packets to indicate to the network the relative priority of the different packets. Since WebRTC flows are fully encrypted, it is not possible to tell which flows are more important by looking at the payload of the packets; the markings need to travel in the packet header.

This specification gives a way to influence both local scheduling decisions and decisions in the network, by offering controls to indicate to the user agent what flows are more or less important to the user.

The resolution chosen (4 levels only) is deliberately coarse; experience in other fora has shown that a plethora of levels or a more complex priority model are rarely used effectively.

### History

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
