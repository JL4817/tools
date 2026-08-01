# Wireshark: Packet Operations

This chapter continues the introductory Wireshark notes with the second TryHackMe room, **Wireshark: Packet Operations**. It explains not only which menu or filter to use, but also **why** that operation answers the question.

> [!IMPORTANT]
> The IP addresses and domains in the training capture are evidence for analysis only. Do not browse to them, scan them, or otherwise interact with them.

Sources:

- [Wireshark: Packet Operations — TryHackMe walkthrough](https://medium.com/@jcm3/wireshark-packet-operations-tryhackme-walkthrough-93283297bd27)
- [Wireshark User's Guide](https://www.wireshark.org/docs/wsug_html/)
- [Wireshark Display Filter Reference](https://www.wireshark.org/docs/dfref/)

---

## 1. A useful packet-analysis strategy

Opening a large capture and reading packets from the first row downward is rarely efficient. A better investigation has five stages:

1. **Establish scope** — check the capture duration, packet count, endpoints, conversations, and protocols.
2. **Form a hypothesis** — identify unusual hosts, ports, protocols, traffic volumes, or response times.
3. **Filter** — turn the hypothesis into a precise display filter.
4. **Inspect context** — examine packet fields, nearby packets, and reconstructed streams.
5. **Record evidence** — preserve packet numbers, timestamps, comments, filters, hashes, and exported objects.

The **Statistics** menu is mainly used during stages 1 and 2. Display filters are used during stages 3 and 4.

---

## 2. Statistics overview

Wireshark's **Statistics** menu summarizes the capture before the analyst begins packet-by-packet inspection. Statistics do not determine whether traffic is malicious; they reveal patterns that deserve investigation.

Useful questions include:

- Which protocols dominate the capture?
- Which hosts send or receive the most packets?
- Which pairs of hosts communicate?
- Which DNS names were resolved?
- Which HTTP hosts received requests?
- Are request-response times unusually high?

### 2.1 Resolved Addresses

Open **Statistics → Resolved Addresses**.

![Wireshark Resolved Addresses window](https://www.wireshark.org/docs/wsug_html_chunked/images/ws-resolved-addr.png)

This window maps machine-readable values to human-readable names. The **Hosts** view lists IPv4 and IPv6 addresses for which Wireshark knows a hostname. Names may come from:

- DNS answers stored inside the capture;
- the capture file's name-resolution records;
- local hosts files;
- manually entered names; or
- an external resolver, if enabled.

#### Why this is useful

An address such as `199.232.24.81` is difficult to recognize by sight. A captured DNS answer containing a hostname beginning with `bbc` immediately provides context about the resource the client attempted to access.

#### Important limitations

- A resolved name is **context**, not proof that the host is trustworthy.
- Several hostnames can use the same IP address because of CDNs, proxies, and shared hosting.
- One hostname can resolve to several IP addresses.
- External name resolution can generate new DNS traffic and may produce different results later.
- After changing name-resolution settings, reopen the Resolved Addresses window to refresh it.

> The walkthrough briefly points to **Analyze → Expert Information** for the `bbc` question. The direct and conceptually correct route is **Statistics → Resolved Addresses**, because the task asks for a hostname-to-address mapping.

### 2.2 Protocol Hierarchy

Open **Statistics → Protocol Hierarchy**.

![Wireshark Protocol Hierarchy window](https://www.wireshark.org/docs/wsug_html_chunked/images/ws-stats-hierarchy.png)

This window displays protocols as a tree that follows encapsulation. A typical branch might look like:

```text
Frame
└── Ethernet
    └── IPv4
        └── TCP
            └── HTTP
```

Important columns include:

- **Packets** — packets containing the protocol.
- **Percent Packets** — percentage of capture packets containing it.
- **Bytes** and **Percent Bytes** — amount and proportion of traffic attributed to it.
- **Bits/s** — average bandwidth across the capture duration.
- **End Packets** — packets where this was the highest protocol Wireshark dissected.
- **PDUs** — decoded protocol data units; this can exceed the packet count after reassembly or when a packet contains multiple PDUs.

#### Why percentages do not add up to 100%

One packet normally contains several protocols. An HTTP packet may also count as Ethernet, IPv4, TCP, and HTTP. The rows overlap rather than represent exclusive categories.

#### Investigation examples

- A capture expected to contain web browsing but dominated by SMB deserves attention.
- A large amount of DNS traffic may indicate a configuration issue, tunnelling, or command-and-control traffic.
- Right-clicking a hierarchy entry lets the analyst apply that protocol as a filter.

### 2.3 Conversations

Open **Statistics → Conversations**.

![Wireshark Conversations window](https://www.wireshark.org/docs/wsug_html_chunked/images/ws-stats-conversations.png)

A **conversation** is traffic between two specific endpoints. Wireshark can group conversations at several layers:

| Tab | Conversation identity | Example |
|---|---|---|
| Ethernet | two MAC addresses | workstation NIC ↔ router NIC |
| IPv4 | two IPv4 addresses | `10.0.0.5` ↔ `93.184.216.34` |
| IPv6 | two IPv6 addresses | client IPv6 ↔ server IPv6 |
| TCP | IP addresses plus TCP ports | `10.0.0.5:51542` ↔ `93.184.216.34:80` |
| UDP | IP addresses plus UDP ports | `10.0.0.5:53001` ↔ `10.0.0.1:53` |

Common columns include:

- packets and bytes from **A → B**;
- packets and bytes from **B → A**;
- totals in both directions;
- conversation start time;
- duration; and
- average bits per second.

#### What A and B mean

`A` and `B` are the two sides of that displayed conversation. They are **not universal aliases for source and destination** across the whole capture. A host can be side A in one row and side B in another, and a two-way conversation contains traffic in both directions.

This makes Conversations excellent for questions such as:

- Which two hosts exchanged the most data?
- Was a connection mostly uploads or downloads?
- How long did a particular connection last?

It is less convenient for a global question such as “Which single IP address received the most packets?” because the same endpoint can appear in many conversation rows.

### 2.4 Endpoints

Open **Statistics → Endpoints**.

![Wireshark Endpoints window](https://www.wireshark.org/docs/wsug_html_chunked/images/ws-stats-endpoints.png)

An **endpoint** is one address, or an address-and-port pair at the transport layer. Each row aggregates all observed traffic for that endpoint.

| Column | Meaning at an IPv4 endpoint |
|---|---|
| `Tx Packets` | packets **transmitted by** the address; the address was the IPv4 source |
| `Tx Bytes` | bytes transmitted by the address |
| `Rx Packets` | packets **received by** the address; the address was the IPv4 destination |
| `Rx Bytes` | bytes received by the address |
| `Packets` | total transmitted plus received packets |
| `Bytes` | total transmitted plus received bytes |

`Tx` means **transmit**, while `Rx` means **receive**. These names are always interpreted from the endpoint row's point of view.

#### Why “most-used IPv4 destination” means sorting by Rx Packets

The question is asking which IPv4 address appears most often in the packet field `ip.dst`.

For every row:

```text
packet's ip.src == endpoint address  →  Tx Packets increases
packet's ip.dst == endpoint address  →  Rx Packets increases
```

Therefore the correct method is:

1. Open **Statistics → Endpoints**.
2. Select the **IPv4** tab.
3. Click **Rx Packets** until it is sorted from largest to smallest.
4. Read the address in the first row.

For the exercise capture, the result is `10.100.1.33`.

#### Why not sort by Tx Packets?

`Tx Packets` answers a different question: **Which IPv4 source address sent the most packets?** Sorting by it would rank `ip.src`, not `ip.dst`.

#### Why Rx Packets is better than Rx Bytes here

“Most used” in this task refers to the **number of packets**, not traffic volume. One endpoint can receive many tiny packets while another receives fewer but much larger packets.

- Sort **Rx Packets** for “most frequent destination.”
- Sort **Rx Bytes** for “destination that received the most data.”

#### Why Conversations → Packets A → B is less reliable

That column measures one direction inside each individual pair. It does not automatically aggregate every packet received by an address across all of its conversations. It happens to produce the same lab answer in the walkthrough, but **Endpoints → IPv4 → Rx Packets** expresses the question directly and is the safer method.

### 2.5 Name resolution

The Endpoints and Conversations windows can translate technical identifiers into readable names:

- MAC address → manufacturer, using the Organizationally Unique Identifier in the first three bytes;
- IP address → hostname;
- TCP or UDP port → service name.

Use the **Name resolution** checkbox in the statistics window or configure **Edit → Preferences → Name Resolution**.

Be careful when interpreting the result:

- a MAC manufacturer identifies the registered vendor prefix, not necessarily the exact device;
- a resolved service name describes the conventional port assignment, not what is actually running there;
- a hostname does not prove ownership or intent.

### 2.6 GeoIP and MaxMind

Wireshark can enrich IP endpoints with data from a MaxMind database, including fields such as country, city, autonomous-system number, and organization.

Configuration path:

**Edit → Preferences → Name Resolution → MaxMind database directories**

GeoIP data helps answer questions such as:

- How many endpoints are associated with Kansas City?
- Which IP is associated with the `Blicnet` autonomous-system organization?

GeoIP is approximate. It indicates the registered or estimated network location, not the physical location of a person or computer. VPNs, proxies, mobile networks, CDNs, and stale database entries can all affect it.

The endpoint list can be sorted by **City** or **AS Organization**. A map view also requires network access, which is unavailable in the TryHackMe lab VM.

---

## 3. Protocol-specific statistics

### 3.1 IPv4 and IPv6 statistics

The IPv4 and IPv6 statistics views reduce the capture to a specific IP version. This prevents IPv4 and IPv6 results from being accidentally combined and can group data by address and transport protocol.

Use these views when the question explicitly says **IPv4** or **IPv6**. The protocol version is part of the scope, not an incidental detail.

### 3.2 DNS statistics

Open **Statistics → DNS**.

![Wireshark DNS statistics window](https://www.wireshark.org/docs/wsug_html_chunked/images/ws-dns.png)

The DNS statistics tree summarizes:

- queries and responses;
- query types such as A, AAAA, MX, and PTR;
- operation codes and response codes;
- classes;
- message sizes; and
- request-response timing.

#### Maximum service request-response time

Expand **Service Stats → Request-response time** and read the maximum value. In the exercise capture, it is `0.467897` seconds.

This value is the longest observed delay between a DNS request and its matched response. It can indicate latency or a slow resolver, but one maximum value alone is not enough to diagnose a problem. Also check:

- average and median response time;
- how often the delay occurs;
- unanswered queries;
- retransmissions; and
- whether packet loss or the capture location could explain the value.

### 3.3 HTTP statistics

The **Statistics → HTTP** submenu includes:

- **Packet Counter** — request methods and response status codes;
- **Requests** — hosts and requested URIs;
- **Load Distribution** — requests and responses grouped by server address and HTTP host;
- **Request Sequences** — relationships between requests using headers such as `Referer` and `Location`.

To answer “How many HTTP requests were made to `rad.msn.com`?” open **Statistics → HTTP → Load Distribution**, locate the hostname, and read its request count. The exercise result is `39`.

The HTTP `Host` header represents the requested virtual host. It may differ from the destination IP because many web domains can share one server address.

---

## 4. Capture filters and display filters

These filters solve different problems and use different languages.

| Property | Capture filter | Display filter |
|---|---|---|
| Applied | before or during capture | after packets have been captured |
| Purpose | decide what is recorded | decide what is visible |
| Language | libpcap/BPF syntax | Wireshark display-filter syntax |
| Nonmatching traffic | never saved | remains in the capture but is hidden |
| Example | `tcp port 80` | `tcp.port == 80` |

> [!CAUTION]
> A bad display filter can be cleared. A bad capture filter can permanently exclude the evidence you needed. When storage and authorization permit, beginners should capture broadly and narrow the results with display filters afterward.

### 4.1 Capture-filter structure

A capture filter can specify:

- **protocol** — `ether`, `ip`, `ip6`, `arp`, `tcp`, or `udp`;
- **direction** — `src`, `dst`, `src or dst`, or `src and dst`;
- **scope** — `host`, `net`, `port`, or `portrange`.

Examples:

```text
tcp port 80
src host 10.0.0.5
dst net 192.168.1.0/24
udp portrange 53-67
```

### 4.2 Display-filter toolbar

![Wireshark display-filter toolbar](https://www.wireshark.org/docs/wsug_html_chunked/images/ws-filter-toolbar.png)

The filter toolbar provides:

- **bookmark menu** — save and retrieve filters;
- **filter input** — enter a display-filter expression;
- **clear button** — remove the filter and show all packets;
- **apply button** — run the filter;
- **recent-filter menu** — reuse recent expressions;
- **add button** — turn the current filter into a one-click button.

The input background is normally:

- **green** when the expression is syntactically valid;
- **red** when it is incomplete or invalid.

Green means only that Wireshark understands the expression. It does **not** mean that the logic is correct or that matching packets exist.

---

## 5. Display-filter syntax

### 5.1 Protocol presence filters

A protocol name by itself tests whether Wireshark dissected that protocol in the packet:

```wireshark
ip
tcp
dns
http
```

For example, `ip` displays IPv4 packets. It does not include pure IPv6 packets because those use the `ipv6` protocol field.

### 5.2 Common comparison operators

| Meaning | C-style | English form | Example |
|---|---|---|---|
| equal | `==` | `eq` | `tcp.dstport == 80` |
| not equal | `!=` | `ne` | `ip.ttl != 64` |
| greater than | `>` | `gt` | `frame.len > 1000` |
| less than | `<` | `lt` | `ip.ttl < 10` |
| greater or equal | `>=` | `ge` | `tcp.window_size >= 8192` |
| less or equal | `<=` | `le` | `frame.len <= 128` |

### 5.3 Logical operators

| Meaning | C-style | English form |
|---|---|---|
| both conditions must match | `&&` | `and` |
| at least one condition must match | `||` | `or` |
| condition must not match | `!` | `not` |

Use parentheses when mixing operators so the intended grouping is obvious:

```wireshark
http.request.method == "GET" && (tcp.dstport == 80 || tcp.dstport == 8080)
```

### 5.4 Direction and port fields

```wireshark
ip.src == 10.0.0.5
ip.dst == 10.0.0.5
ip.addr == 10.0.0.5

tcp.srcport == 80
tcp.dstport == 80
tcp.port == 80
```

The unsuffixed convenience fields match either direction:

- `ip.addr` means source **or** destination address.
- `tcp.port` means source **or** destination TCP port.

Use `src` or `dst` when direction matters. For example, an HTTP server response normally originates from source port 80, while a request to that server normally uses destination port 80.

---

## 6. Explaining the protocol-filter exercises

### 6.1 Count all IPv4 packets

```wireshark
ip
```

Why it works: `ip` tests for the presence of the IPv4 dissector. Read the displayed-packet count in the status bar after applying it.

Exercise result: `81420`.

### 6.2 Find TTL values below 10

```wireshark
ip.ttl < 10
```

Why it works: `ip.ttl` is a numeric IPv4 field and `< 10` performs a numeric comparison. A low TTL can mean the sender intentionally used a small value, a packet has crossed many routers, or traffic is part of a diagnostic or unusual network condition. It is not automatically malicious.

Exercise result: `66` packets.

### 6.3 Find traffic using TCP port 4444

```wireshark
tcp.port == 4444
```

Why it works: `tcp.port` matches either `tcp.srcport` or `tcp.dstport`, so it catches packets traveling to or from port 4444.

Exercise result: `632` packets.

### 6.4 Find HTTP GET requests sent to port 80

```wireshark
http.request.method == "GET" && tcp.dstport == 80
```

Why it works:

- `http.request.method == "GET"` selects request packets whose HTTP method is GET.
- `tcp.dstport == 80` proves the packet is going **to** the server-side port.
- `&&` requires both facts to be true in the same packet.

Using `tcp.port == 80` also produces the room's intended count in many captures, but `tcp.dstport == 80` expresses “requests sent to port 80” more precisely.

Exercise result: `527` packets.

### 6.5 Find type A DNS queries

```wireshark
dns.flags.response == 0 && dns.qry.type == 1
```

Why it works:

- `dns.flags.response == 0` limits the result to queries rather than responses.
- `dns.qry.type == 1` selects DNS type A, which requests an IPv4 address.

If the exercise capture places `dns.qry.type` only on query packets, the shorter filter below can give the same result:

```wireshark
dns.qry.type == 1
```

Exercise result: `51` packets.

The **Analyze → Display Filter Expression** dialog is useful when a field name or numeric enumeration is unknown.

![Wireshark Display Filter Expression dialog](https://www.wireshark.org/docs/wsug_html_chunked/images/ws-filter-add-expression.png)

---

## 7. Advanced display filters

### 7.1 `contains`

`contains` checks whether a string or byte sequence includes a literal value.

```wireshark
http.server contains "IIS"
tcp contains 48:65:6c:6c:6f
```

Use it when the target is a literal substring. It is not a regular-expression operator, and it cannot be applied directly to atomic values such as an integer or IP address.

### 7.2 `matches`

`matches` applies a PCRE2 regular expression. Matching is case-insensitive by default.

```wireshark
http.host matches "(?i)example\\.(com|org)$"
string(ip.ttl) matches "[02468]$"
```

Use `matches` when the pattern has alternatives, anchors, character classes, or other regular-expression features.

### 7.3 `in`

`in` checks whether a field belongs to a set or range. It is clearer than repeating the same field with several `or` conditions.

```wireshark
tcp.port in {3333, 4444, 9999}
http.request.method in {"HEAD", "GET"}
ip.addr in {10.0.0.5..10.0.0.9}
```

The first expression is equivalent to:

```wireshark
tcp.port == 3333 || tcp.port == 4444 || tcp.port == 9999
```

> The Medium walkthrough says to combine the port checks with `&&`, but its actual filter uses `or`. `&&` would incorrectly demand that one packet simultaneously use all three different port values. The required operation is **OR**, or the cleaner `in` form.

### 7.4 `upper()` and `lower()`

These functions normalize the case of a string field before comparison:

```wireshark
upper(http.server) contains "APACHE"
lower(http.server) contains "apache"
```

They are useful when capitalization is inconsistent. They do not decrypt data and cannot expose a header that Wireshark was unable to dissect.

### 7.5 `string()`

`string()` converts a non-string field to text so string or regular-expression operations can be applied.

```wireshark
string(ip.ttl) matches "[02468]$"
```

The final decimal digit of every even number is `0`, `2`, `4`, `6`, or `8`. `$` anchors that digit to the end of the string. This is why the expression finds even TTL values.

Exercise result: `77289` packets.

---

## 8. Explaining the advanced exercises

### 8.1 IIS packets not originating from port 80

```wireshark
http.server contains "IIS" && tcp.srcport != 80
```

Reasoning:

- `http.server contains "IIS"` selects HTTP responses whose Server header identifies Microsoft IIS.
- “Originate from port 80” refers to the TCP **source** port, so the correct directional field is `tcp.srcport`.
- `!= 80` keeps IIS-identifying packets whose source port is something other than 80.

Exercise result: `21` packets.

### 8.2 IIS version 7.5

```wireshark
http.server contains "IIS" && http.server contains "7.5"
```

A shorter equivalent for this particular capture is:

```wireshark
http.server contains "IIS/7.5"
```

Exercise result: `71` packets.

The `Server` header is supplied by the remote application and can be changed, removed, or forged. Treat it as an investigative clue rather than conclusive software identification.

### 8.3 Ports 3333, 4444, or 9999

Preferred form:

```wireshark
tcp.port in {3333, 4444, 9999}
```

Equivalent form:

```wireshark
tcp.port == 3333 || tcp.port == 4444 || tcp.port == 9999
```

Exercise result: `2235` packets.

### 8.4 Even TTL values

```wireshark
string(ip.ttl) matches "[02468]$"
```

Exercise result: `77289` packets.

### 8.5 Bad TCP checksums under the Checksum Control profile

1. Open **Edit → Configuration Profiles**.
2. Select **Checksum Control**.
3. Open **Analyze → Expert Information**.
4. Locate the Bad TCP Checksum entry.

Exercise result: `34185` packets.

A large number of bad checksums does not automatically indicate corrupted network traffic. On a machine performing the capture, **checksum offloading** can cause packets to be captured before the network card inserts the final checksum. Wireshark then sees an incomplete checksum even though the transmitted packet is valid. Always consider the capture point and offloading configuration.

### 8.6 Existing filter button

The room's profile already contains a display-filter button. Clicking it applies the saved expression immediately.

Exercise result: `261` displayed packets.

Always inspect a saved button's expression before relying on it. The label is only a friendly name and may hide outdated or overly broad logic.

---

## 9. Filter bookmarks, buttons, and profiles

### Filter bookmarks

Bookmarks store named display filters in the bookmark menu. They are best for filters that are useful but not applied constantly.

Example:

```wireshark
dns.flags.response == 1 && dns.flags.rcode != 0
```

Suggested bookmark name: `DNS errors`.

### Filter buttons

Buttons place a saved filter directly on the toolbar. They are useful for frequent actions such as:

- DNS errors;
- TCP resets;
- HTTP error responses;
- low TTL values; or
- traffic involving a known investigation host.

Too many buttons make the toolbar difficult to scan, so keep only high-value filters visible.

### Configuration profiles

Open **Edit → Configuration Profiles** or use the profile control in the lower-right status bar.

A profile can preserve investigation-specific settings such as:

- display-filter buttons;
- coloring rules;
- columns;
- protocol preferences;
- name-resolution settings; and
- checksum-validation behavior.

Useful profile ideas include:

- `Default` — general packet inspection;
- `DNS Investigation` — query, response, timing, and error columns;
- `Web Analysis` — HTTP/TLS fields and status-code filters;
- `Malware Triage` — suspicious ports, DNS, and stream shortcuts;
- `Checksum Control` — checksum-validation-focused settings.

Record the active profile in investigation notes because two analysts can open the same capture and see different results when their protocol preferences differ.

---

## 10. Packet Operations workflow

### Workflow A: Identify the busiest destination

1. Open the capture.
2. Select **Statistics → Endpoints**.
3. Choose the **IPv4** tab.
4. Sort **Rx Packets** descending.
5. Record the leading address and packet count.
6. Right-click the endpoint and apply it as a display filter.
7. Inspect its protocols and conversations.

### Workflow B: Investigate an unfamiliar hostname

1. Open **Statistics → Resolved Addresses**.
2. Search for the hostname.
3. Record every associated IP address.
4. Apply `dns.qry.name == "hostname.example"` if the exact query is needed.
5. Filter each relevant address with `ip.addr == x.x.x.x`.
6. Review Conversations and Follow Stream results.
7. Do not contact the domain outside the authorized lab.

### Workflow C: Build a filter safely

1. State the question in plain English.
2. Identify the protocol field for each fact.
3. Decide whether direction matters: source, destination, or either.
4. Choose comparison operators.
5. Combine conditions with explicit parentheses.
6. Confirm that the filter bar is green.
7. Apply the filter and check the displayed count.
8. Inspect several matches to verify that the filter means what you intended.
9. Save the filter as a bookmark or button only after validation.

### Workflow D: Investigate high DNS response time

1. Open **Statistics → DNS** and note the maximum response time.
2. Use DNS timing fields or packet details to locate slow request-response pairs.
3. Confirm that requests and responses were matched correctly.
4. Check retransmissions and unanswered queries.
5. Compare the slow result with the average and surrounding traffic.
6. Correlate timestamps with other logs if available.

---

## 11. Quick-reference filter sheet

```wireshark
# IPv4 packets
ip

# One address in either direction
ip.addr == 10.100.1.33

# Packets sent to an IPv4 destination
ip.dst == 10.100.1.33

# TTL below 10
ip.ttl < 10

# TCP port in either direction
tcp.port == 4444

# One of several TCP ports
tcp.port in {3333, 4444, 9999}

# HTTP GET requests sent to TCP port 80
http.request.method == "GET" && tcp.dstport == 80

# DNS type A queries
dns.flags.response == 0 && dns.qry.type == 1

# Microsoft IIS Server header
http.server contains "IIS"

# IIS 7.5 Server header
http.server contains "IIS/7.5"

# Even IPv4 TTL values
string(ip.ttl) matches "[02468]$"

# DNS error responses
dns.flags.response == 1 && dns.flags.rcode != 0
```

> Lines beginning with `#` are explanatory comments for this Markdown example. Paste only the actual filter expression into Wireshark unless your version explicitly supports comments in that context.

---

## 12. Exercise results and reasoning summary

<details>
<summary><strong>Show TryHackMe answers</strong></summary>

| Question | Method | Result |
|---|---|---|
| Hostname beginning with `bbc` | Statistics → Resolved Addresses | `199.232.24.81` |
| Number of IPv4 conversations | Statistics → Conversations → IPv4 | `435` |
| Kilobytes transferred from `Micro-St` MAC | Statistics → Endpoints → Ethernet, enable name resolution | `7474` |
| IP addresses associated with Kansas City | Endpoints → IPv4, sort City | `4` |
| IP associated with Blicnet organization | Endpoints → IPv4, sort AS Organization | `188.246.82.7` |
| Most-used IPv4 destination | Endpoints → IPv4, sort Rx Packets descending | `10.100.1.33` |
| Maximum DNS request-response time | Statistics → DNS → Service Stats | `0.467897` seconds |
| HTTP requests for `rad.msn.com` | Statistics → HTTP → Load Distribution | `39` |
| IPv4 packets | `ip` | `81420` |
| TTL below 10 | `ip.ttl < 10` | `66` |
| TCP port 4444 | `tcp.port == 4444` | `632` |
| HTTP GET to destination port 80 | `http.request.method == "GET" && tcp.dstport == 80` | `527` |
| Type A DNS queries | `dns.flags.response == 0 && dns.qry.type == 1` | `51` |
| IIS packets not from source port 80 | `http.server contains "IIS" && tcp.srcport != 80` | `21` |
| IIS version 7.5 | `http.server contains "IIS/7.5"` | `71` |
| TCP ports 3333, 4444, or 9999 | `tcp.port in {3333, 4444, 9999}` | `2235` |
| Even TTL values | `string(ip.ttl) matches "[02468]$"` | `77289` |
| Bad TCP checksums in Checksum Control profile | Profile → Expert Information | `34185` |
| Existing filter button | Click the supplied profile button | `261` |

</details>

---

## 13. Key lessons

- Start with statistics to understand the capture before creating narrow filters.
- Use **Endpoints** for one-host totals and **Conversations** for traffic between pairs.
- For an endpoint, **Rx** corresponds to destination traffic and **Tx** corresponds to source traffic.
- Packet counts measure frequency; byte counts measure volume.
- Capture filters control what is saved, while display filters control what is shown.
- Directional questions require directional fields such as `ip.dst` or `tcp.srcport`.
- Use `or` or `in` when any one of several values may match; use `and` only when every condition must be true in the same packet.
- A syntactically valid filter can still be logically wrong, so inspect sample matches.
- Statistics, GeoIP, server headers, Expert Information, and checksum warnings are clues, not final conclusions.
- Profiles make investigations repeatable, but analysts should record which profile was active.
