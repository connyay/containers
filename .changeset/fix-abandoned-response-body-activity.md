---
'@cloudflare/containers': patch
---

Fix containers never sleeping after a client abandons a response body. `containerFetch` counted a
proxied response as in flight until its body had been fully piped. A body nobody read stalled on
backpressure, so the in-flight counter never dropped back to zero and every alarm renewed
`sleepAfter`. The request now leaves the in-flight count as soon as the container answers, and
bytes flowing through the response body renew the activity timeout instead.

Proxying large response bodies is also cheaper. The proxy now reads the body in chunks of up to
256KB instead of handing it to JavaScript 4KB at a time.
