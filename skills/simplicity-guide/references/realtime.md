# Realtime updates

The ladder for keeping the client current. Start at manual refresh; climb only
when a stated requirement forces it.

## manual-refresh

The user reloads the page (or hits a button) to get fresh data.

- **Use when:** data changes rarely or staleness is acceptable until the user
  acts; no expectation of automatic updates.
- **Climb when:** users need updates without acting — the view must refresh
  itself on some interval.
- **Cost:** none. No extra connections, no background traffic, no client timers.
- **Rationale template:** `"chose manual refresh — {rarely-changing data},
  {staleness acceptable}, {user-driven updates}"`

## polling

The client periodically re-fetches data on a timer.

- **Use when:** updates can lag by seconds-to-minutes; an interval refresh is
  good enough (dashboards, status pages, slow-moving feeds).
- **Climb when:** you need server-initiated pushes with low latency, or polling
  overhead becomes wasteful at the freshness you require.
- **Cost:** repeated requests on a timer; trivial to implement but wastes some
  requests when nothing changed.
- **Rationale template:** `"chose polling — {seconds-to-minutes staleness ok},
  {interval refresh enough}, {no server push}"`

## sse

Server-Sent Events: a one-way server-to-client stream over HTTP.

- **Use when:** the server needs to push updates promptly but the flow is
  one-directional (notifications, live feeds, progress streams).
- **Climb when:** you need genuine low-latency *bidirectional* messaging — the
  client must push to the server over the same live channel.
- **Cost:** a long-lived connection per client to hold open; works over plain
  HTTP, no separate protocol, but ties up a connection slot.
- **Rationale template:** `"chose SSE — {prompt server push}, {one-way flow},
  {no client-to-server channel}"`

## websockets

A full-duplex, low-latency bidirectional connection.

- **Use when:** both sides exchange messages continuously with low latency —
  chat, collaborative editing, multiplayer, live trading.
- **Climb when:** (top of this ladder) — scaling fan-out across nodes (pub/sub,
  presence) is a separate, later decision driven by measured load.
- **Cost:** a stateful connection protocol to run, scale, and keep alive across
  restarts and load balancers. Real ops burden — check it against the ceiling.
- **Rationale template:** `"chose websockets — {low-latency bidirectional},
  {continuous exchange}, {SSE one-way insufficient}"`
