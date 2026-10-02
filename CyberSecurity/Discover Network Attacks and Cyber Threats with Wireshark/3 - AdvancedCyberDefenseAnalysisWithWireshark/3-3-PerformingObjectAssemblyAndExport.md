<details>
<summary><strong>Working up to Object Extraction</strong></summary>

## Working up to Object Extraction

- Reconstruct files transferred over FTP, HTTP, SMTP, IMAP, POP3, and SMB using the previous module’s captures.
- The demonstrations transfer a bzip2-compressed tar archive containing Thunderbird.

### Extraction Methods

| Protocol | Built-in Export Method Used | Manual Alternative |
|---|---|---|
| FTP | Export Objects → FTP-DATA | Save the separate data stream as Raw |
| HTTP | Export Objects → HTTP | Save the stream and isolate the response body |
| SMTP | Export Objects → IMF | Isolate and decode the attachment’s Base64 content |
| IMAP | Export Objects → IMF | Isolate and decode the attachment’s Base64 content |
| POP3 | Not available in the demonstrated setup | Follow the stream and decode the attachment |
| SMB | Export Objects → SMB | Not demonstrated |

### Important Considerations

- Wait for Wireshark to finish processing before saving an object or stream.
- A missing or incomplete capture can produce an incomplete file.
- Base64 is encoding, not encryption; the attachments in these email examples use it.
- POP3 also retrieves Internet-format email messages. The demonstrated export limitation is a Wireshark support issue, not an absence of that message format.
- Examine unknown extracted files in a controlled environment; do not execute them on your normal system.

</details>

<details>
<summary><strong>Demo: File Transfer Protocol (FTP)</strong></summary>

## Demo: File Transfer Protocol (FTP)

- Extract `testfile` from FTP’s separate data connection.
- In this capture, the data connection begins around **packet 67**, and file data starts at **packet 72**.

### Method 1: Export Objects

1. Open the FTP capture.
2. Select **File → Export Objects → FTP-DATA**.
3. Wait for object processing to finish.
4. Select `testfile`, associated with packet `72`.
5. Click **Save** and choose a location.
6. Inspect the archive using a suitable tool such as 7-Zip.

- The exported file is a bzip2-compressed tar archive containing Thunderbird.
- A filename without an extension does not change its actual file format.

### Method 2: Follow the Data Stream

1. Select an **FTP-DATA** packet belonging to the file transfer.
2. Right-click → **Follow → TCP Stream**.
3. Wait for reassembly to finish.
4. Change **Show data as** to **Raw**.
5. Select the direction carrying the file if necessary.
6. Wait for processing to finish, then click **Save As**.
7. Verify that an archive tool recognizes the saved file.

- Follow the file’s data connection, not the FTP control connection or directory-listing stream.
- Saving before processing finishes may produce a partial file.

</details>

<details>
<summary><strong>Demo: Hypertext Transfer Protocol (HTTP)</strong></summary>

## Demo: Hypertext Transfer Protocol (HTTP)

- Extract the downloaded `testfile` while separating file content from HTTP headers and other responses.

### Method 1: Export Objects

1. Open the HTTP capture.
2. Select **File → Export Objects → HTTP**.
3. Wait for processing to finish.
4. Identify the larger object named `testfile`.
5. Click **Save** and choose a location.
6. Verify that the exported archive is readable.

- The demonstration lists both the HTML webpage and the downloaded file.

### Method 2: Manually Extract the Response Body

1. Select a packet from the download.
2. Right-click → **Follow → TCP Stream**.
3. Locate the GET request for `testfile` and its matching response.
4. Change the display format to **Raw**.
5. Wait for processing, then save the stream.
6. Open a working copy in a binary-safe or hex editor.
7. Identify the exact beginning and end of the file’s response body.
8. Remove preceding requests, webpage content, HTTP headers, and any trailing unrelated data.
9. Save the remaining bytes and verify the archive.

### Demonstration Notes

- The instructor uses TCP Stream because HTTP Stream crashes with the large file in the demonstrated Wireshark version.
- Vim and `xxd` are used to inspect and edit the saved bytes.
- The line numbers shown in the lesson apply only to that file; use byte boundaries rather than copying those line numbers.
- Manual extraction must also account for transfer framing or content encoding when present. Removing headers alone is not sufficient for every HTTP response.

</details>

<details>
<summary><strong>Demo: Simple Mail Transfer Protocol (SMTP)</strong></summary>

## Demo: Simple Mail Transfer Protocol (SMTP)

- Recover the Base64-encoded attachment from an email uploaded through SMTP.
- The SMTP connection begins around **packet 227**.

### Method 1: Export the Email

1. Open **File → Export Objects → IMF**.
2. Select the demonstrated message associated with **packet 4943**.
3. Save it as an `.eml` file.
4. Open a copy in a text editor.
5. Locate the attachment section marked `Content-Transfer-Encoding: base64`.
6. Copy only that attachment’s encoded body into `attachment.b64`.
7. Exclude email headers, MIME boundaries, and unrelated message content.

### Decode and Check the Attachment

- On the Linux demonstration system, decode the saved Base64 text:

`base64 --decode attachment.b64 > testfile.tar.bz2`

- Check that the archive can be read without extracting it:

`tar -tjf testfile.tar.bz2`

- The lesson also uses `--ignore-garbage`. This can skip non-Base64 characters, but it does not replace correctly isolating the attachment.

### Method 2: Follow the SMTP Stream

1. Select a packet from the relevant SMTP conversation.
2. Right-click → **Follow → TCP Stream**.
3. Locate the attachment’s Base64 body.
4. Copy it into a text file, excluding protocol commands and MIME boundaries.
5. Decode and verify it using the same commands.

- Exporting the email produces the message container; decoding the attachment produces the original binary archive.

</details>

<details>
<summary><strong>Demo: Internet Message Access Protocol (IMAP)</strong></summary>

## Demo: Internet Message Access Protocol (IMAP)

- Recover an attachment retrieved through IMAP.
- The demonstrated TCP handshake starts around **packet 24**, followed by IMAP commands at **packet 27**.

### Method 1: Export the Email

1. Open **File → Export Objects → IMF**.
2. Wait for the message list to populate.
3. Select the larger, second message.
4. Save the message and open a copy in a text editor.
5. Locate the attachment named `testfile` and its Base64 encoding declaration.
6. Save only the attachment’s Base64 body as `attachment.b64`.
7. Remove surrounding headers and MIME boundary lines.

- The instructor deletes specific opening and closing lines, but those positions are capture-specific.

### Decode and Verify

- Convert the Base64 body into the original archive:

`base64 --decode attachment.b64 > testfile.tar.bz2`

- Verify its contents:

`tar -tjf testfile.tar.bz2`

### Method 2: Follow the IMAP Stream

1. Select a packet from the message-retrieval conversation.
2. Choose **Follow → TCP Stream**.
3. Find the same Base64 attachment section.
4. Copy only the encoded body into a text file.
5. Decode it and verify the resulting archive.

- Exclude IMAP commands, email headers, and MIME boundaries from the encoded input.

</details>

<details>
<summary><strong>Demo: Post Office Protocol (POP)</strong></summary>

## Demo: Post Office Protocol (POP)

- Extract an email attachment manually from a POP3 stream.
- The demonstrated Wireshark setup does not expose this POP transfer through the IMF object exporter.

### Extract the Attachment

1. Select a packet from the POP conversation.
2. Right-click → **Follow → TCP Stream**.
3. Locate the email attachment’s Base64 section.
4. Copy only the encoded attachment body.
5. Save it as `attachment.b64`, excluding POP responses, headers, and MIME boundaries.
6. Decode it:

`base64 --decode attachment.b64 > testfile.tar.bz2`

7. Verify the archive:

`tar -tjf testfile.tar.bz2`

### Important Distinction

- POP3 can retrieve the same Internet-format email and MIME attachments used with SMTP and IMAP.
- The difference in this demonstration is the available Wireshark export support.
- Manual stream extraction still recovers the attachment when the necessary data is captured.

</details>

<details>
<summary><strong>Demo: Server Message Block (SMB)</strong></summary>

## Demo: Server Message Block (SMB)

- Extract the archive transferred through SMB using Wireshark’s built-in object exporter.
- The requested file is shown as `testfile.bz2` in the demonstration.

### Why Use Export Objects?

- SMB uses binary protocol structures, so following the TCP stream does not produce a simple text command exchange.
- File data is mixed with SMB protocol information.
- Saving the entire stream as a file would include unrelated protocol bytes.

### Export the File

1. Open the SMB capture.
2. Review the decoded SMB requests and filename.
3. Select **File → Export Objects → SMB**.
4. Wait for object searching and processing to finish.
5. Select the transferred archive.
6. Click **Save** and choose a destination.
7. Verify that an archive tool recognizes it.

### Exported Result

- The exporter reconstructs the file bytes directly; no separate Base64 decoding is required.
- The saved file is still a compressed archive, so decompression is needed to access its contents.
- This completes the demonstrations of object extraction across the six protocols.

</details>