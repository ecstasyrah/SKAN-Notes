<details>
<summary><strong>Reviewing tshark Uses</strong></summary>

## Reviewing tshark Uses

- TShark is Wireshark’s command-line alternative, useful for remote analysis, scripting, and environments without a graphical desktop.
- It supports packet capture, filtering, protocol decoding, statistics, stream following, and object export.

### Main Options

| Option | Purpose |
|---|---|
| `-D` | List available capture interfaces |
| `-i` | Select a capture interface |
| `-f` | Apply a capture filter using BPF syntax |
| `-w` | Write packets to a capture file |
| `-P` | Print packet summaries even when writing to a file |
| `-r` | Read an existing capture |
| `-Y` | Apply a Wireshark display filter |
| `-q` | Suppress normal packet output, useful for statistics |
| `-n` | Disable name resolution |
| `-z` | Generate statistics or follow streams |
| `-d` | Override protocol decoding |
| `--export-objects` | Export supported objects into a directory |

- Options are case-sensitive: uppercase `-P` prints summaries; lowercase `-p` disables promiscuous mode. The introductory transcript confuses these options.

### Termshark

- Termshark is a separate terminal interface that uses TShark as its backend.
- It provides Wireshark-like packet panes and conversation views without requiring a graphical desktop.
- It is not included as a native component of Wireshark and offers fewer features.
- Project website: https://termshark.io

</details>

<details>
<summary><strong>Demo: tshark Basics</strong></summary>

## Demo: tshark Basics

- Capture traffic, save it, and apply filters from the command line.
- Replace example interface and file names with those in your environment.

### 1. List Capture Interfaces

- Identify the available interfaces:

`tshark -D`

- The lab’s physical interface is `eth0`.
- Linux also provides an `any` capture interface for traffic across interfaces.

### 2. Start a Capture

- Start using TShark’s default interface selection:

`tshark`

- Explicitly select an interface:

`tshark -i eth0`

- Capture through Linux’s `any` interface:

`tshark -i any`

1. Start the capture.
2. Generate traffic, such as a ping, from another terminal.
3. Review the displayed packets.
4. Press **Ctrl+C** to stop.

### 3. Apply a Capture Filter

- Capture only TCP traffic:

`tshark -i any -f "tcp"`

- `-f` uses capture-filter/BPF syntax.
- Only matching packets are collected.
- TCP-carried applications may appear as TLS or another decoded protocol in the output.

### 4. Save Packets to a File

- Write captured packets to disk:

`tshark -i eth0 -w out.pcapng`

- Stop with **Ctrl+C** when enough traffic has been collected.
- The saved capture can be opened in TShark or Wireshark.

### 5. Read and Filter a Capture

- Display the saved packets:

`tshark -r out.pcapng`

- Display only TCP packets:

`tshark -r out.pcapng -Y "tcp"`

| Filter Type | Option | Effect |
|---|---|---|
| Capture filter | `-f` | Controls which packets are collected |
| Display filter | `-Y` | Controls which packets are displayed during analysis |

- `-Y` uses Wireshark display-filter syntax and does not modify the original capture.
- In the lesson, only six packets match TCP in a capture of roughly 642 packets; much of the remaining traffic is QUIC.

### 6. Display and Save Simultaneously

- Print packet summaries while writing the capture:

`tshark -i eth0 -P -w out2.pcapng`

### 7. Try Termshark

- If installed, launch its terminal interface:

`termshark`

- Review its packet list, details, bytes, and conversation views.
- In the demonstration, it captures from the same default Ethernet interface.

</details>

<details>
<summary><strong>Demo: tshark Statistics</strong></summary>

## Demo: tshark Statistics

- Use `-z` to generate statistics comparable to Wireshark’s Statistics menu.

### 1. List Available Statistics

`tshark -z help`

- Options include protocol hierarchy, conversations, endpoints, stream following, and protocol-specific statistics.

### 2. Display Protocol Hierarchy

`tshark -r ftp.pcapng -q -z io,phs`

- Shows the protocols identified in the capture and their traffic breakdown.
- `-r` selects the capture.
- `-q` suppresses individual packet summaries.
- Without `-q`, packet summaries appear before the statistics.

### 3. Display Conversations

| Conversation Type | Command |
|---|---|
| Ethernet | `tshark -r ftp.pcapng -q -n -z conv,eth` |
| IPv4 | `tshark -r ftp.pcapng -q -n -z conv,ip` |
| TCP | `tshark -r ftp.pcapng -q -n -z conv,tcp` |
| UDP | `tshark -r ftp.pcapng -q -n -z conv,udp` |

- `-n` keeps addresses and ports numeric by disabling name resolution.
- Conversation output includes directional packet and byte counts, totals, relative start times, and duration.
- TCP and UDP views also identify endpoint ports.

### Analysis Uses

- Identify heavily communicating hosts.
- Compare traffic sent and received.
- Locate unusual ports or repeated connections.
- Find conversations worth inspecting in more detail.

</details>

<details>
<summary><strong>Demo: tshark Stream Following</strong></summary>

## Demo: tshark Stream Following

- Reassemble a selected conversation using the `follow` statistics option.

### Basic Syntax

`tshark -r capture.pcapng -q -z "follow,tcp,ascii,0"`

- The follow arguments specify the protocol, output mode, and stream selector.

| Argument | Example | Meaning |
|---|---|---|
| Protocol | `tcp` | Protocol stream to follow |
| Mode | `ascii` | Output representation |
| Selector | `0` | Stream index |

- Common modes include `ascii`, `hex`, and `raw`.
- Supported protocols and additional stream/substream selectors depend on the follow option.

### Follow the Demonstrated FTP Streams

| Stream | Content | Command |
|---|---|---|
| `0` | FTP login, features, and commands | `tshark -r ftp.pcapng -q -z "follow,tcp,ascii,0"` |
| `1` | Directory listing | `tshark -r ftp.pcapng -q -z "follow,tcp,ascii,1"` |
| `2` | Transferred file | `tshark -r ftp.pcapng -q -z "follow,tcp,ascii,2"` |

- These stream IDs apply to the example capture.
- Binary file content will not look readable in ASCII mode.

### Select by Endpoints

- A TCP stream can also be selected using both endpoint IP addresses and ports.
- The lesson identifies one endpoint as `172.16.1.129:60048`; obtain the complete endpoint pair from the capture.
- This is useful when the stream index is unknown.

### Save Follow Output

`tshark -r ftp.pcapng -q -z "follow,tcp,raw,2" > stream-output.txt`

- TShark’s raw follow output is a formatted report containing hexadecimal data and headers, not a ready-to-use binary file.
- Redirecting it does not perform the same operation as **Save As → Raw** in Wireshark.
- Additional parsing and conversion are required; use object export when supported.

</details>

<details>
<summary><strong>Demo: tshark Decode As</strong></summary>

## Demo: tshark Decode As

- Use `-d` to assign the correct dissector when an application uses an unexpected port.

### 1. Inspect the Original Decoding

`tshark -r telnet-alt.pcapng`

- The demonstration uses TCP port `19` for Telnet.
- TShark initially interprets it as character-generation traffic because of the port.

### 2. Override the Dissector

`tshark -r telnet-alt.pcapng -d "tcp.port==19,telnet"`

- `tcp.port==19` selects the port mapping.
- `telnet` specifies the dissector.
- This is Decode As syntax, not an unrestricted display-filter expression.

### 3. Review the Result

- Confirm that the output now identifies Telnet and shows meaningful protocol content.
- An incorrect dissector can produce misleading results; verify it against the actual exchange.

### Additional Example

- Decode SSH running on TCP port `18`:

`tshark -r ssh-alt.pcapng -d "tcp.port==18,ssh"`

- Selecting an SSH dissector identifies protocol structure; it does not decrypt the session.

</details>

<details>
<summary><strong>Demo: tshark Object Exporting</strong></summary>

## Demo: tshark Object Exporting

- Export reconstructed objects using TShark’s equivalent of Wireshark’s **Export Objects** feature.

### Basic Syntax

`tshark -r capture.pcapng -q --export-objects "protocol,directory"`

- `protocol` selects a supported object exporter.
- `directory` specifies where the extracted files will be saved.
- `./` means the current working directory, not the filesystem root.

### 1. Export an FTP File

`tshark -r ftp.pcapng -q --export-objects "ftp-data,./"`

1. Run the command and wait for it to finish.
2. Check the output directory for `testfile`.
3. Verify the recovered file with an appropriate archive tool.

- Successful export may produce little or no terminal output.
- The demonstrated file is already binary and does not require Base64 decoding.
- Renaming it with a suitable extension does not change its contents.

### 2. Export Other Supported Objects

| Capture Type | Example Command |
|---|---|
| HTTP | `tshark -r http.pcapng -q --export-objects "http,./"` |
| SMTP email | `tshark -r smtp.pcapng -q --export-objects "imf,./"` |
| IMAP email | `tshark -r imap.pcapng -q --export-objects "imf,./"` |
| SMB | `tshark -r smb.pcapng -q --export-objects "smb,./"` |

- IMF exports email messages; recovering an encoded attachment may still require extracting its body and decoding Base64.
- Export capabilities depend on protocol support and the completeness of the capture.
- Use the exact option name `--export-objects`, including the final `s`.

</details> 