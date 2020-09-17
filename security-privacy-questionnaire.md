This questionnaire's questions were copied from https://raw.githubusercontent.com/w3ctag/security-questionnaire/master/questionnaire.markdown on September 17. 2020.

> 01. What information might this feature expose to Web sites or other parties,
>     and for what purposes is that exposure necessary?

This feature does not expose any information to Web sites.

It allows Web sites to set the priority of network flows, which in turn can be observed by entities with the ability to monitor network traffic.

> 02. Is this specification exposing the minimum amount of information necessary
>     to power its features?

Yes.

> 03. How does this specification deal with personal information,
>     personally-identifiable information (PII), or information derived from
>     them?

This specification does not deal with personally-identifiable information.

> 04. How does this specification deal with sensitive information?

This specification does not deal with sensitive information.

> 05. Does this specification introduce new state for an origin that persists
>     across browsing sessions?

No.

> 06. What information from the underlying platform, e.g. configuration data, is
>     exposed by this specification to an origin?

In combination with an ability to monitor network traffic, this specification exposes information about what features of
the specification are actually honored by the platform.

> 07. Does this specification allow an origin access to sensors on a user’s
>     device

No.

> 08. What data does this specification expose to an origin?  Please also
>     document what data is identical to data exposed by other features, in the
>     same or different contexts.

This specification does not expose any data through its API.

> 09. Does this specification enable new script execution/loading mechanisms?

No.

> 10. Does this specification allow an origin to access other devices?

No.

> 11. Does this specification allow an origin some measure of control over a user
>     agent's native UI?

No.

> 12. What temporary identifiers might this this specification create or expose
>     to the web?

None.

> 13. How does this specification distinguish between behavior in first-party and
>     third-party contexts?

It makes no such distinction.

> 14. How does this specification work in the context of a user agent’s Private
>     Browsing or "incognito" mode?

It makes no such distinction.

> 15. Does this specification have a "Security Considerations" and "Privacy
>     Considerations" section?

Not yet.

> 16. Does this specification allow downgrading default security characteristics?

No.

> 17. What should this questionnaire have asked?

Does this specification allow influencing the UA's environment in security-relevant ways?

This specification allows some control over parts of network headers that are commonly used for
prioritization of network traffic (DSCP markings). If the network to which the UA is attached
is misconfigured or underprovisioned, this may influence the local network environment by emitting
packets with DSCP markings that are not expected by the configuration.

Countermeasures against this risk include proper configuration; the simplest configuration
is DSCP bleaching - always zeroing or ignoring the DSCP markings. Congestion control (always mandatory
for WebRTC) will prevent network overload in most cases.
