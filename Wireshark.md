# Wireshark features 

## Main Wireshark window

The tutorial describes Wireshark as an all-in-one desktop interface. Its important regions are:

- Main menu: commands grouped under File, Edit, View, Go, Analyze, Statistics, and related menus.
- Toolbar: shortcuts for capture and navigation operations.
- Display-filter bar: accepts queries that restrict which loaded packets are visible.
- Packet List pane: one row per packet, with summary columns.
- Packet Details pane: expandable protocol fields for the selected packet.
- Packet Bytes pane: raw packet bytes, normally in hexadecimal and text form.
- Status bar: capture interface, packet counts, displayed-packet count, profile, and status indicators.

Selecting a field in Packet Details highlights its corresponding bytes in Packet Bytes.

## Loading captures

Wireshark can open .pcap, .cap, and .pcapng packet-capture files through:

- File → Open.
- Drag and drop.
- Double-clicking a capture file.
- Selecting a recently used file.

The tutorial uses:

- http1.pcapng to reproduce screenshots.
- Exercise.pcapng to solve questions.

## Packet coloring

Packet rows are colored to make different protocols and conditions recognizable.

Two kinds of coloring are described:

- Permanent coloring rules: saved in the active Wireshark profile.
- Temporary conversation coloring: lasts for the current session.

Relevant controls include:

- View → Coloring Rules.
- View → Colourise Conversation.
- View → Colourise Conversation → Reset Colourisation.
- Colourise Packet List.
- Packet right-click menu.

Users can create custom permanent colors using display-filter expressions.

## Live traffic capture

The toolbar uses colored capture controls:

- Blue shark-fin button: start capturing traffic.
- Red button: stop capture.
- Green button: restart capture.

The status bar shows the active interface and number of captured packets. The article correctly notes that Wireshark observes packets; it does not block or modify them and is not itself an intrusion-detection
system.

## Merging capture files

File → Merge combines a second capture with the currently open capture.

Workflow:

1. Open the first capture.
2. Select File → Merge.
3. Choose another capture.
4. Review its packet count.
5. Open it to perform the merge.
6. Save the resulting combined capture.

The merged result must be saved before it becomes a persistent file.

## Capture File Properties

Statistics → Capture File Properties displays information such as:

- File path and size.
- File hashes.
- Capture time.
- Interface information.
- Packet statistics.
- Capture comments.

The same dialog can reportedly be reached through a capture-file icon at the bottom-left of the window.

## Packet dissection

Wireshark decodes raw network data into protocol-specific fields. A selected HTTP packet can expose:

- Frame metadata.
- Ethernet source and destination MAC addresses.
- IPv4 source and destination addresses.
- TCP or UDP ports and transport fields.
- TCP reassembly information or errors.
- Application protocols such as HTTP, FTP, or SMB.
- Application payload data.

A single click selects a packet. Double-clicking opens packet details in a separate window.

## Packet navigation and search

### Go to Packet

Available through:

- Go → Go to Packet.
- Ctrl+G.
- Toolbar navigation controls.

The user enters a packet number and Wireshark selects that packet.

### Find Packet

Edit → Find Packet or Ctrl+F searches loaded data.

Supported search inputs include:

- Display-filter expression.
- Hexadecimal bytes.
- Plain string.
- Regular expression.

Users must also choose where to search:

- Packet List.
- Packet Details.
- Packet Bytes.

Searching the wrong pane may return no result even when the content exists elsewhere. Case sensitivity can be enabled or disabled.

## Marking packets

Edit or the right-click menu can mark and unmark packets.

Marked packets:

- Appear black regardless of their normal protocol color.
- Help identify important evidence.
- Can be used when selecting packets to export.
- Are temporary and disappear when the capture session closes.

## Packet comments

A right-click command allows users to add comments to individual packets.

Unlike marks, comments can be stored in an appropriate capture file and shared with another analyst. Saving the capture is necessary to retain them.

## Exporting packets

File → Export Specified Packets creates a smaller capture containing selected or filtered evidence. This is useful when the original capture contains large amounts of unrelated traffic.

## Exporting transferred objects

File → Export Objects extracts files reconstructed from supported application protocols, including those listed in the article:

- HTTP.
- SMB.
- TFTP.
- DICOM.
- IMF.

The user selects a protocol, reviews the discovered objects, chooses one or more entries, and saves them to disk. Exported objects should be handled carefully because network captures can contain malicious
files.

## Time Display Format

View → Time Display Format changes how packet timestamps are presented.

The article contrasts:

- Seconds since the beginning of the capture.
- UTC-based clock time.

The most useful setting depends on whether the analyst is studying relative timing or correlating events with external logs.

## Expert Information

Analyze → Expert Information summarizes notable protocol states.

The dialog includes information such as:

- Severity or category.
- Packet number.
- Protocol.
- Summary.
- Number of occurrences.

These are analytical hints, not confirmed security incidents. False positives and false negatives remain possible.

## Filtering

### Capture filters

A capture filter limits what Wireshark records during live collection. Packets that do not match are not placed in the capture.

### Display filters

A display filter hides nonmatching packets in an existing capture without deleting them.

Example from the tutorial:

http

This shows packets dissected as HTTP.

### Apply as Filter

Right-click a visible protocol field and select Apply as Filter. Wireshark:

1. Generates the filter expression.
2. Places it in the filter bar.
3. Immediately executes it.
4. Shows matching packets and hides the rest.

### Conversation Filter

Filters for all packets belonging to a related endpoint conversation, typically using address and port information.

Accessible through:

- Analyze → Conversation Filter.
- Right-click menu.

### Colourise Conversation

Highlights a conversation without hiding unrelated packets. Reset the temporary coloring through the View menu.

### Prepare as Filter

Creates a filter expression but does not immediately execute it. The user can extend it with logical conditions such as and or or, then press Enter.

### Apply as Column

Turns a selected packet field into a Packet List column, allowing that value to be compared across many packets.

Columns can be enabled or disabled from the Packet List’s column-header controls.

### Follow Stream

Analyze → Follow TCP/UDP/HTTP Stream reconstructs application-level conversation data from multiple packets.

The article describes:

- Server data displayed in blue.
- Client data displayed in red.
- A separate stream dialog.
- Automatic creation of a stream display filter.

The filter bar’s X button clears the generated filter and restores the full packet list. Unencrypted streams may expose sensitive information, including usernames and passwords.

———

# 4. Common user workflows

## Workflow A: Complete the TryHackMe exercise

1. Launch the attached TryHackMe virtual machine.
2. Use its split-view interface; SSH and RDP are unnecessary.
3. Open Wireshark.
4. Select File → Open.
5. Open Exercise.pcapng.
6. Follow each task in the article.
7. Inspect the relevant packet, field, dialog, or exported object.
8. Enter the discovered answer in TryHackMe.

## Workflow B: Inspect a packet

1. Open a capture file.
2. Select a row in the Packet List.
3. Review its source, destination, protocol, length, and summary.
4. Expand protocol trees in Packet Details.
5. Select a field.
6. Observe the highlighted bytes in Packet Bytes.
7. Double-click the packet if a separate inspection window is preferable.

## Workflow C: Find a known string

1. Press Ctrl+F.
2. Select String.
3. Choose Packet Details, Packet Bytes, or another appropriate search area.
4. Enter the search text.
5. Set case sensitivity if required.
6. Press Enter.
7. Inspect the selected matching packet.

The walkthrough uses this process to locate r4w.

## Workflow D: Investigate one conversation

1. Select a relevant packet.
2. Right-click a field or protocol.
3. Choose Conversation Filter to hide unrelated traffic, or Colourise Conversation to retain it visibly.
4. Use Follow Stream to reconstruct the application dialogue.
5. Search within the reconstructed stream.
6. Close the dialog.
7. Clear the generated display filter using the X button.

## Workflow E: Extract a transferred file

1. Open the capture.
2. Select File → Export Objects.
3. Choose the relevant protocol, such as HTTP.
4. Sort or search the object list.
5. Select the desired filename.
6. Save it to a controlled location.
7. Inspect or hash it using appropriate security precautions.

The article uses this workflow to extract a text file containing the name PACKETMASTER.

## Workflow F: Prepare an evidence subset

1. Search for or filter the relevant activity.
2. Mark suspicious packets if needed.
3. Add explanatory packet comments.
4. Choose File → Export Specified Packets.
5. Select the displayed, marked, or chosen packets.
6. Save the new capture.
7. Verify the resulting file and record its cryptographic hash.

———

# 5. UI controls and components

## Medium controls

- Search: searches Medium content.
- Write: opens Medium’s story-creation flow; unauthenticated users are prompted to register.
- Sign up: begins account registration.
- Sign in: opens the login flow and returns the user to the article afterward.
- Get app/Open in app: directs the visitor to Medium’s mobile application.
- Author name/avatar: opens the author’s profile.
- Listen: initiates text-to-speech access, with an account or plan prompt visible in the observed link flow.
- Share: opens sharing options or copies a shareable link.
- Table of contents links: scroll to corresponding article headings.
- Article image: can be clicked or activated with Enter to view it at a larger size.
- Topic tags: lead to related Medium content.
- Footer links: open Medium’s support, company, status, and policy pages.

## Important Wireshark controls shown or discussed

- Blue shark-fin icon: starts capture.
- Red stop icon: stops capture.
- Green restart icon: restarts capture.
- File menu: open, merge, save, and export operations.
- Edit menu: find and mark packet operations.
- View menu: time format and coloring options.
- Go menu: packet-number navigation.
- Analyze menu: filters, streams, columns, and Expert Information.
- Statistics menu: Capture File Properties.
- Display-filter field: accepts filter expressions.
- Filter X button: clears the active display filter.
- Expand/collapse arrows: expose or hide protocol fields.
- Packet right-click menu: context-sensitive filtering, marking, comments, exporting, and stream commands.
- Status bar: reports total and displayed packet counts and other capture information.

———

# 6. Images and visual elements

The article relies heavily on embedded screenshots rather than decorative artwork. Medium labels most of them “Press enter or click to view image in full size.”

## Types of screenshots

The screenshots document:

- Empty and populated Wireshark windows.
- The three packet-analysis panes.
- Default packet colors.
- Capture start, stop, and restart controls.
- Merge-file dialog.
- Capture File Properties and comments.
- Packet-list status counts.
- Expandable Frame, Ethernet, IP, TCP, HTTP, and payload fields.
- Go to Packet controls.
- Find Packet search options.
- Marked packets.
- Packet comments.
- Packet and object export dialogs.
- Time-format menus.
- Expert Information results.
- Right-click filtering commands.
- Filter-bar output.
- Follow Stream windows.
- Exercise-specific answer locations.

## What the visuals contribute

The images help beginners by showing:

- Exactly where a menu or value is located.
- Which pane contains a requested field.
- How the interface changes after filtering.
- What an expanded protocol tree looks like.
- Where packet totals and warning counts appear.
- How extracted HTTP objects and reconstructed streams are presented.

The teaching pattern is usually:

1. Explain the feature.
2. Show the relevant control.
3. Show the resulting dialog or value.
4. State the TryHackMe answer.

## Visual limitations and accessibility

Several images appear to rely on surrounding prose rather than descriptive alt text. Repeated generic labels such as “See image” or “Press enter or click…” do not explain the screenshot to someone who cannot
see it.

This is a significant accessibility weakness. A better version would give each screenshot meaningful alternative text, such as “Wireshark Capture File Properties dialog with SHA-256 field highlighted.”

———

# 7. Technical observations

## Medium platform

Directly observable characteristics include:

- Server-delivered HTML with headings and internal anchor links.
- Responsive global navigation elements.
- Account flows that preserve a redirect back to the article.
- Images served through Medium’s miro.medium.com image infrastructure.
- Deep links for opening the Medium mobile application.
- A text-to-speech integration associated with Speechify.
- Registration gating for writing and some listening functionality.

The page almost certainly uses client-side JavaScript for menus, engagement controls, image enlargement, and account state, but the specific framework and internal API calls were not verified. It would be
inappropriate to assign a framework solely from the rendered page.

## Authentication

Public reading is available without authentication. Authentication is invoked for actions such as:

- Writing a story.
- Account-specific engagement.
- Some listening access.
- Personal publishing or library features.

The inspected links show redirect parameters that bring users back to the article or intended action after authentication.

## Forms and data flow

Observed or implied forms include:

- Site search input.
- Sign-in and registration forms.
- Story-writing flow for authenticated authors.

No user form is embedded in the tutorial body. TryHackMe answers are entered on TryHackMe, not on Medium.

## Wireshark data flow

Within the application:

1. Packets arrive from a live network interface or capture file.
2. Wireshark decodes them using protocol dissectors.
3. Summaries appear in Packet List.
4. Structured fields appear in Packet Details.
5. Original bytes appear in Packet Bytes.
6. Display filters alter visibility, not the underlying capture.
7. Comments or coloring rules may be saved depending on their type and file format.
8. Exports create new capture files or reconstructed objects.

## Responsive behavior

Medium normally collapses or simplifies its header on narrow screens and keeps the article in a single readable column. The page also promotes opening the content in the mobile app. Exact breakpoints and
control placement could not be tested in this session.

Wireshark itself is a desktop application. Its three-pane layout is designed for a large screen and is not a mobile interface.

———

# 8. Important terminology

- Packet: a discrete unit of network data.
- Frame: the link-layer representation of transmitted data.
- PCAP/PCAPNG: file formats used to store captured network traffic.
- Packet sniffing: collecting traffic visible through a network interface.
- Packet analyzer: software that decodes and displays captured traffic.
- Protocol dissector: code that interprets the fields of a particular protocol.
- OSI model: conceptual layers used to describe network communication.
- MAC address: identifier used at the data-link layer.
- IP address: logical address used to route traffic between networks.
- Port: transport-layer number identifying an application or service endpoint.
- TCP: connection-oriented transport protocol.
- UDP: connectionless transport protocol.
- HTTP: web application protocol.
- Payload: data carried inside protocol headers.
- TTL: “Time to Live,” an IP field limiting how far a packet can travel.
- ETag: HTTP identifier used for validating a representation or cache.
- Capture filter: controls which packets are recorded.
- Display filter: controls which recorded packets are currently shown.
- Conversation: related traffic between communicating endpoints.
- Stream: reconstructed application data spanning multiple packets.
- Packet reassembly: combining fragments or TCP segments into higher-level data.
- Expert Information: Wireshark-generated notices about potentially notable protocol states.
- False positive: normal behavior incorrectly considered suspicious.
- False negative: suspicious behavior that is not flagged.
- IDS: Intrusion Detection System; Wireshark is not one.
- SOC analyst: security professional who monitors and investigates events.
- Hash: fixed-length identifier used to verify file integrity.
- SHA-256/MD5: cryptographic hash algorithms; MD5 is unsuitable for modern security guarantees but can still be used as a basic file identifier.
- VM: virtual machine.
- Split View: TryHackMe interface that places instructions and the lab machine together.

———

# 9. Frequently asked questions

## Is this the official Wireshark documentation?

No. It is an independent Medium walkthrough of a TryHackMe lesson. For authoritative behavior, consult Wireshark’s official documentation.

## Do I need a Medium account?

Not to read the public article. An account may be required for writing, some engagement features, and listening access.

## Do I need to install Wireshark?

Not necessarily for this exercise. The TryHackMe room supplies a VM with Wireshark and the required capture files.

## Which file should I use?

- Use http1.pcapng to imitate tutorial screenshots.
- Use Exercise.pcapng to answer the room’s questions.

## Does Wireshark stop attacks?

No. It captures and analyzes traffic. It does not automatically block malicious packets.

## Does applying a display filter delete packets?

No. It only changes which packets are visible.

## Why did Find Packet return no results?

The wrong search area may be selected. A value visible in Packet Details will not necessarily be found when searching only Packet List.

## Why did my marked packets disappear?

Packet marks are session-specific. They are lost when the capture is closed.

## Are packet comments permanent?

They can be retained in a compatible capture file when the file is saved.

## Why did the packet count change after Follow Stream?

Wireshark automatically applies a stream display filter. Clear it with the X at the right of the filter bar.

## Can Wireshark recover files?

Sometimes. Export Objects can reconstruct files transferred through supported, visible protocols. Encryption, missing packets, or unsupported protocols may prevent recovery.

## Can Wireshark display passwords?

It can expose credentials in unencrypted traffic. Encrypted traffic normally prevents direct reading unless appropriate decryption material and configuration are available.

## Are Expert Information warnings proof of an attack?

No. They are protocol-analysis hints that require interpretation.

## Is it safe to open an exported object?

Not automatically. Treat files extracted from unknown captures as potentially malicious.

## Does the walkthrough reveal challenge answers?

Yes. It supplies direct answers for most exercises, so readers seeking an unaided challenge should avoid the answer paragraphs.

———

# 10. Summary

## Key takeaways

The article is a broad beginner introduction to Wireshark. It teaches the reader to:

- Open and merge packet captures.
- Understand the three principal analysis panes.
- Navigate to and search for packets.
- Decode protocol layers.
- Filter and color traffic.
- Add marks and comments.
- Export evidence and transferred objects.
- Reconstruct TCP, UDP, or HTTP streams.
- Interpret basic Expert Information.

## Main strengths

- Closely follows the TryHackMe room.
- Covers many essential Wireshark operations in one place.
- Uses numerous screenshots to demonstrate exact locations.
- Combines conceptual explanations with concrete exercises.
- Provides shortcuts such as Ctrl+F and Ctrl+G.
- Clearly distinguishes display filters from capture filters.
- Warns that Wireshark is not an IDS.

## Confusing or unusual aspects

- “Website” and “application” can be conflated: Medium hosts the tutorial, while Wireshark is the software being taught.
- The walkthrough mixes original author commentary with material closely following the TryHackMe lesson.
- Direct answers reduce its usefulness as a self-assessment exercise.
- Some menu names contain apparent mistakes, such as “Statistics>Status File Properties,” where “Capture File Properties” is intended.
- The terms “packet” and “package” are occasionally mixed.
- Screenshot descriptions are weak for screen-reader users.
- Some explanations simplify OSI-layer relationships; users should treat them as introductory guidance rather than a precise protocol-model reference.
- Interface details may differ slightly across Wireshark versions and operating systems.

