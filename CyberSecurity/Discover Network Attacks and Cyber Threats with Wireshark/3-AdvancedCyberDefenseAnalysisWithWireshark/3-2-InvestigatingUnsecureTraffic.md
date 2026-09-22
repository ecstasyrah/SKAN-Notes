<details>
<summary><strong>Covering Common Insecure Network Protocols</strong></summary>

## Covering Common Insecure Network Protocols

- Unencrypted protocols can expose credentials, commands, messages, and files to anyone able to capture the traffic.
- Wireshark makes these exchanges easier to inspect through protocol decoding and stream reassembly.

### Common Protocols

| Protocol | Main Purpose | Information Potentially Exposed |
|---|---|---|
| FTP | Transfer files between devices | Credentials, commands, and files |
| Telnet | Remote terminal access and device management | Credentials, typed commands, and output |
| HTTP | Web browsing, application communication, and file transfers | Requests, responses, and transferred content |
| SMTP | Submit and transfer email | Email content, attachments, and some authentication exchanges |
| IMAP | Access and manage email stored on a server | Messages, attachments, and credentials |
| POP3 | Retrieve email from a server | Messages, attachments, and credentials |

### Security Considerations

- These demonstrations use unencrypted traffic; secure alternatives or TLS extensions are available for most of these protocols.
- SSH generally replaces Telnet for secure remote terminal access.
- HTTPS protects HTTP communication using TLS.
- Base64 and similar encodings represent data differently but do not encrypt it.
- Using a nonstandard port does not make a protocol secure.

</details>

<details>
<summary><strong>Reviewing Wireshark Features for Traffic Analysis</strong></summary>

## Reviewing Wireshark Features for Traffic Analysis

- Use protocol dissectors, statistics, conversation filters, Decode As, and stream following to investigate insecure traffic.

### Protocol Dissectors

- Dissectors interpret packet contents and display recognized protocol fields in a readable format.
- The Packet Details pane shows decoded fields; Packet Bytes shows their underlying bytes.

### Conversation Statistics

1. Open **Statistics → Conversations**.
2. Select the relevant protocol tab.
3. Compare endpoints, ports, packet counts, and transferred bytes.

- Traffic volume and repeated connections can help identify suspicious access attempts or service disruption.
- Statistics provide context, not proof of an attack.

### Conversation Filters

1. Right-click a relevant packet.
2. Select **Conversation Filter**.
3. Choose the appropriate available protocol, such as TCP or UDP.

- Filtering isolates related traffic and reduces background noise.

### Decode As

- Manually select a dissector when Wireshark does not recognize a protocol, especially on a nonstandard port.

1. Select a packet from the suspected application.
2. Right-click → **Decode As**.
3. Select the relevant TCP port.
4. Assign the appropriate protocol and apply the change.

- Example: decode TCP port `2100` as FTP.
- An incorrect dissector can produce misleading or meaningless output; confirm the protocol from its content.

### Follow a Stream

1. Right-click a relevant packet.
2. Select **Follow → TCP Stream**, or an available protocol-specific stream option.
3. Review the reassembled exchange.

- Different colors distinguish the two traffic directions.
- This is especially useful for cleartext commands, credentials, messages, and transferred data.
- Following a stream also applies a display filter; clear it to return to the full capture.

</details>

<details>
<summary><strong>Demo: File Transfer Protocol (FTP)</strong></summary>

## Demo: File Transfer Protocol (FTP)

- Analyze FTP authentication, commands, directory listings, and file transfers.
- FTP uses a control connection and separate data connections.

### 1. Inspect the Control Connection

1. Open the FTP capture.
2. Identify the initial **SYN → SYN-ACK → ACK** handshake.
3. Apply `ftp`.
4. Review the Info column and FTP packet details.

**Visible activity:**

- Username and password submission.
- Successful login and server feature information.
- Directory listing requests.
- A request to download `testfile`.

### 2. Understand the Data Connections

- The control connection normally uses TCP port `21`.
- In passive mode, the client connects to a separate server-selected data port.
- In active mode, the server normally initiates the data connection from TCP port `20` to a client-specified port.
- Directory listings and file downloads can use separate data connections.

1. Clear the `ftp` filter to restore TCP and data-transfer context.
2. Open **Statistics → Conversations → TCP**.
3. Compare packet and byte counts.

- The demonstrated capture contains three TCP conversations: control, directory listing, and file transfer.
- The larger data conversation carries the file.
- The transcript gives inconsistent data-port numbers; verify the negotiated ports directly in the capture.

### 3. Follow the Control Stream

1. Select a control packet.
2. Right-click → **Follow → TCP Stream**.
3. Review the commands and responses.

- In the demonstration, red represents the client and blue represents the server.
- The file contents are absent because they use a different TCP connection.

### 4. Follow the File Transfer

1. Locate the file-transfer conversation in **Statistics → Conversations → TCP**.
2. Note its stream ID.
3. Apply `tcp.stream == 2` for the demonstrated file-transfer stream.
4. Right-click a matching packet → **Follow → TCP Stream**.
5. Compare the ASCII and Raw display formats.

- Stream `2` contains the transferred file in this capture.
- Binary file bytes may appear unreadable when displayed as ASCII.
- Clear the stream filter when finished.

### 5. Decode FTP on Port 2100

1. Open the alternative FTP capture.
2. Inspect the TCP traffic on port `2100`.
3. Use **Follow → TCP Stream** to recognize FTP commands.
4. Right-click a packet → **Decode As**.
5. Assign TCP port `2100` to **FTP**.

- Wireshark now displays FTP fields and command summaries.
- This alternative capture shows login and logout without a file transfer.

</details>

<details>
<summary><strong>Demo: Hypertext Transfer Protocol (HTTP)</strong></summary>

## Demo: Hypertext Transfer Protocol (HTTP)

- Inspect HTTP requests, responses, webpage content, and binary downloads.
- The source transcript swaps the HTTP and Telnet headings; these notes match the demonstrated protocols.

### 1. Inspect More Than the Info Column

1. Open the HTTP capture.
2. Identify the TCP handshake.
3. Select an HTTP response.
4. Expand **HTTP** and its associated content in Packet Details.

- A summary such as `200 OK` does not show the complete response body.
- The payload may contain webpage source or downloaded file content.

### 2. Isolate the HTTP Conversation

1. Open **Statistics → Conversations → TCP**.
2. Locate the port `80` conversation.
3. Apply `tcp.stream == 0` for the demonstrated stream.

- The capture includes conversations on ports `80` and `443`; the lesson focuses on port `80`.
- A stream filter includes associated TCP packets even when their Protocol column does not say HTTP.
- File content can span many TCP segments after the HTTP headers.

### 3. Follow the Transfer

1. Select an HTTP packet.
2. Choose **Follow → HTTP Stream** or **Follow → TCP Stream**.
3. Locate the client’s GET request.
4. Inspect the response headers and body.

- The demonstrated download uses `Content-Type: application/octet-stream`, indicating generic binary data.
- Binary content may look like gibberish in a text view.
- File extraction is covered in the next module.

### 4. Examine HTTP on Port 1234

- The alternative capture uses TCP port `1234` instead of `80`.

1. Open the alternative capture.
2. Inspect the detected protocol and HTTP fields.
3. Follow the stream to review the exchange.

- Wireshark recognizes HTTP from the content in this example despite the unusual port.
- This capture does not include the additional file download shown in the first example.

</details>

<details>
<summary><strong>Demo: Telnet</strong></summary>

## Demo: Telnet

- Inspect a cleartext terminal session containing login information, commands, and responses.
- This demonstration does not transfer a file.

### 1. Review the Session

1. Open the Telnet capture.
2. Inspect the small packets carrying characters and terminal control information.
3. Right-click a relevant packet → **Follow → TCP Stream**.

- Interactive Telnet can send individual keystrokes or small groups of characters.
- This produces many small packets even during a short command exchange.

### 2. Understand Echoed Characters

- In the demonstrated session, the server echoes typed characters back to the client.
- Viewing both directions together makes the username `kali` appear as `kkaallii`.
- Alternating colors distinguish the original characters from the echoed copies.
- Password entry is not echoed, but the password is still transmitted without encryption.

### 3. Review Conversation Statistics

1. Open **Statistics → Conversations → TCP**.
2. Inspect the single captured Telnet conversation.

- Packet counts reflect interactive commands and echo traffic rather than a large file transfer.

### 4. Decode Telnet on Port 19

1. Open the alternative capture using TCP port `19`.
2. Select a relevant packet.
3. Right-click → **Decode As**.
4. Assign the port to **Telnet**.

- The correct dissector makes terminal commands and data easier to interpret.
- Port `19` is the lab’s alternative port, not Telnet’s standard port.

</details>

<details>
<summary><strong>Demo: Internet Message Access Protocol (IMAP)</strong></summary>

## Demo: Internet Message Access Protocol (IMAP)

- Analyze unencrypted mailbox access, message retrieval, and attachment content.
- The source transcript swaps the IMAP and SMTP headings; these notes match the demonstrated protocols.

### 1. Identify the Relevant Streams

1. Open the IMAP capture.
2. Locate traffic on TCP port `143`.
3. Open **Statistics → Conversations → TCP**.

- Five conversations were captured, but the lesson focuses on two:

| Stream | Demonstrated Content |
|---|---|
| `0` | Commands and mailbox/message listing activity |
| `1` | Retrieved messages and attachment data |

- These stream assignments apply to this capture; IMAP does not require FTP-style separate control and data connections.

### 2. Follow the Commands and Messages

1. Apply `tcp.stream == 0`.
2. Use **Follow → TCP Stream** to inspect the command exchange.
3. Switch to `tcp.stream == 1`.
4. Review the retrieved email and attachment content.

### 3. Recognize Base64 Attachments

- The demonstrated email attachment is Base64-encoded.
- Base64 converts binary data into a text representation suitable for transport.
- The attachment can be reconstructed by isolating its encoded content and decoding it.
- Encoding provides no confidentiality; the email client normally handles decoding.

### 4. Decode IMAP on Port 144

1. Open the alternative capture using TCP port `144`.
2. Follow its TCP stream to identify IMAP commands.
3. Right-click a packet → **Decode As**.
4. Assign port `144` to **IMAP**.

- Wireshark can then display the recognized IMAP fields instead of only generic TCP data.

</details>

<details>
<summary><strong>Demo: Simple Mail Transfer Protocol (SMTP)</strong></summary>

## Demo: Simple Mail Transfer Protocol (SMTP)

- Analyze an email upload from a client to a server, including an encoded attachment.

### 1. Locate the SMTP Exchange

1. Open the SMTP capture.
2. Move to approximately **packet 227**, where the demonstrated connection begins.
3. Inspect the TCP handshake and subsequent SMTP traffic.
4. Open **Statistics → Conversations → TCP**.
5. Locate TCP port `25`, identified as **stream 5**.

- Earlier packets include unrelated email-client activity.
- This stream contains thousands of packets associated with the email upload.

### 2. Follow the Upload

1. Apply `tcp.stream == 5`.
2. Right-click a relevant packet → **Follow → TCP Stream**.
3. Review server capabilities, client commands, and message content.
4. Locate the attachment’s Base64 transfer-encoding declaration.

- In this demonstration, red shows client traffic and blue shows server traffic.
- The attachment travels from the client to the server, unlike the download examples.
- The receiving email client can decode the Base64 attachment.

### 3. Decode SMTP on Port 26

1. Open the alternative capture using TCP port `26`.
2. Follow the TCP stream to recognize SMTP commands.
3. Right-click a packet → **Decode As**.
4. Assign port `26` to **SMTP**.

- Wireshark then displays the appropriate SMTP fields and command summaries.

</details>

<details>
<summary><strong>Demo: Post Office Protocol (POP)</strong></summary>

## Demo: Post Office Protocol (POP)

- Analyze a POP3 session retrieving email and an attachment from a server.

### 1. Inspect the Conversation

1. Open the POP capture.
2. Identify the TCP handshake and decoded POP traffic.
3. Open **Statistics → Conversations → TCP**.
4. Follow the single captured TCP stream.

- The server reports five available messages.
- The client downloads one message that it has not previously retrieved.
- Previously downloaded messages can remain on the server depending on client settings.

### 2. Examine the Attachment

- The retrieved message includes a Base64-encoded attachment.
- As with IMAP and SMTP, the encoded content can be decoded to recover the original file.
- Base64 does not protect the attachment from someone inspecting the capture.

### 3. Decode POP on Port 111

1. Open the alternative capture using TCP port `111`.
2. Inspect the traffic, which may initially be interpreted as RPC.
3. Right-click a packet → **Decode As**.
4. Assign port `111` to **POP**.

- The corrected dissector reveals the POP commands and fields.
- Port `111` is used only as an alternative in this example; it is not POP3’s standard port.

### Next Module

- Use these protocol-analysis techniques to extract and save files transferred within captured traffic.

</details>