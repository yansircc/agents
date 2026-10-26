---
"agents": patch
---

fix(chat): stop re-parking a connection that already ACKed the stream

A connection that joined while a turn was streaming could be parked in `pendingResumeConnections` twice: once by the proactive `STREAM_RESUMING` from `onConnect`, and again when its own `STREAM_RESUME_REQUEST` arrived after it had already ACKed. `useAgentChat` dedupes the second notice and never ACKs again, so the connection stayed excluded from live broadcast and received no chunks until the stream ended. The resume handshake now remembers which request each connection ACKed and only parks it again for a new request id; the notice itself is still sent, because an explicit request must always get an answer.
