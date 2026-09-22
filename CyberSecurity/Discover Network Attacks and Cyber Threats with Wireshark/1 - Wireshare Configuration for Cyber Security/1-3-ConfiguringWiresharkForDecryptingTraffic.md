<details>
<summary><strong>Capturing the TLS Session Keys</strong></summary>

## Capturing the TLS Session Keys
- Configure a client to record TLS session secrets so encrypted traffic can be decrypted and analyzed in Wireshark.

### TLS Session Keys
- The client and server derive encryption secrets when establishing a secure TLS connection.

#### Modern TLS
- Modern TLS implementations generate connection-specific secrets that protect the data exchanged during that session.

#### Collection Locations
- Session secrets may be collected from an authorized client, server, or TLS-inspection proxy that terminates the encrypted connection.

### Collection Requirement
- The session secrets must normally be recorded while the TLS connection is being created.

#### Packet Capture Limitation
- Session secrets cannot be passively obtained by simply monitoring encrypted packets on the network.

#### Existing Captures
- A packet capture cannot be decrypted later unless the matching session secrets were recorded or another valid decryption method is available.

## Configuring Windows

### Step 1: Open Environment Variables
- Create an environment variable that tells supported applications where to save TLS session secrets.

1. Open the Windows search bar.
2. Search for **Edit the system environment variables**.
3. Open **System Properties**.
4. Select the **Advanced** tab.
5. Click **Environment Variables**.

### Step 2: Create SSLKEYLOGFILE
- Add a user environment variable containing the full path of the key log.

1. Under **User variables**, click **New**.
2. Enter `SSLKEYLOGFILE` as the variable name.
3. Enter a writable file path as the variable value.
4. Click **OK** to save the variable.

#### Example Value
- Use `C:\Users\<username>\sslkeylog.log`.

#### Important Reminder
- Replace `<username>` with the actual Windows account name.

### Step 3: Restart the Application
- Restart the supported browser or application so it can read the new environment variable.

1. Completely close the browser or application.
2. Confirm that no related process remains open.
3. Reopen the application.
4. Visit a website that uses HTTPS.
5. Check whether `sslkeylog.log` was created and contains new entries.

#### Application Support
- The environment variable works only with applications and TLS libraries that support the `SSLKEYLOGFILE` format.

#### Empty Log File
- If the file remains empty, confirm that the application supports key logging, inherited the environment variable, and can write to the selected directory.

## Configuring Linux

### Step 4: Export SSLKEYLOGFILE
- Set the environment variable within a terminal session.

1. Open a terminal.
2. Run the following command:

   `export SSLKEYLOGFILE="/home/<username>/sslkeylog.log"`

3. Replace `<username>` with the actual Linux username.
4. Launch the supported application from the same terminal.
5. Generate new HTTPS traffic.

#### Environment Scope
- The variable normally applies only to the current shell and applications started from that shell.

#### Example
- After exporting the variable, start a supported browser from the same terminal so it inherits the setting.

### Step 5: Verify the Key Log
- Confirm that the application is recording session secrets.

1. Check whether the file exists.
2. Open it using a text editor or monitoring command.
3. Confirm that new lines appear when new TLS sessions are created.

#### Example Command
- Use `tail -f /home/<username>/sslkeylog.log`.

#### Expected Content
- The file should contain labels and hexadecimal values representing TLS session secrets.

## Capturing the Matching Packets

### Step 6: Start the Packet Capture
- Record the same network traffic for which the session secrets are being generated.

1. Open Wireshark.
2. Select the correct network interface.
3. Start the packet capture.
4. Generate new HTTPS or TLS traffic using the configured application.
5. Stop and save the packet capture.

#### Matching Requirement
- The key log and PCAP must contain information from the same TLS connections.

#### Existing Connections
- Close and reopen the application or connection when necessary so that a new TLS handshake and new secrets are recorded.

## Importing the Key Log into Wireshark

### Step 7: Configure the TLS Key Log
- Tell Wireshark where the recorded session secrets are stored.

1. Open Wireshark.
2. Select **Edit → Preferences**.
3. Expand **Protocols**.
4. Select **TLS**.
5. Locate **(Pre)-Master-Secret log filename** or **TLS key log file**.
6. Browse to `sslkeylog.log`.
7. Click **OK**.
8. Reopen or reload the packet capture if necessary.

### Step 8: Verify TLS Decryption
- Confirm that Wireshark can now interpret the encrypted application data.

1. Apply the display filter `tls`.
2. Select a packet from the captured TLS session.
3. Check whether Wireshark displays decrypted application protocols such as HTTP/2 or HTTP.
4. Right-click a matching TCP packet and select **Follow → TLS Stream** when available.

#### Successful Decryption
- Wireshark displays decrypted protocol details instead of only encrypted TLS application data.

#### Failed Decryption
- Decryption may fail if the application did not log secrets, the key log does not match the capture, the capture missed required handshake packets, or the application used an unsupported implementation.

## Security Warning
- A TLS key log contains highly sensitive session secrets that can remove the confidentiality provided by TLS.

### Protection Requirements
- Use key logging only on systems and traffic you are authorized to inspect.
- Do not enable it on production systems.
- Restrict access to the key log.
- Do not send the file through insecure channels.
- Delete it securely after completing the authorized lab or investigation.

### Key Takeaway
- TLS decryption requires both the captured packets and their matching session secrets. Configure `SSLKEYLOGFILE` before generating the traffic, verify that the application writes keys, and import the log through Wireshark’s TLS preferences.

### Reference
- [RFC 9850: The SSLKEYLOGFILE Format for TLS](https://www.rfc-editor.org/info/rfc9850/)

</details>

<details>
<summary><strong>Lab 12 – Decrypting TLS Traffic in Wireshark</strong></summary>

## Lab 12 – Decrypting TLS Traffic in Wireshark
- Combine a TLS packet capture with its matching session key log to decrypt and inspect protected application traffic.

### Required Files
- Use the files provided with Lab 12.

#### Packet Capture
- The PCAP contains the TLS connection recorded while the browser accessed an encrypted website.

#### TLS Key Log
- The key log contains the session secrets generated by the client during the captured TLS connections.

#### Matching Requirement
- The key log must contain secrets from the same TLS sessions found in the packet capture.

## Traffic Before Decryption
- Wireshark can identify the TLS handshake but cannot initially read the protected application data.

### Visible Information
- The capture contains a TLS 1.3 **Client Hello**.

### Encrypted Information
- Starting around packet `10`, the payload appears as encrypted TLS application data.

## Importing the Key Log

### Step 1: Open Wireshark Preferences
- Access the protocol configuration settings.

1. Open the Lab 12 PCAP in Wireshark.
2. Select **Edit → Preferences**.
3. On some operating systems, open Wireshark’s application preferences or settings instead.

### Step 2: Open the TLS Settings
- Locate the preferences used for TLS decryption.

1. Expand **Protocols**.
2. Press the `T` key to jump to protocols beginning with T.
3. Select **TLS**.

### Step 3: Select the Key Log File
- Configure Wireshark to use the session secrets supplied with the lab.

1. Locate **(Pre)-Master-Secret log filename** or **TLS key log file**.
2. Click **Browse**.
3. Navigate to the Lab 12 key log file.
4. Select the file.
5. Click **Open**.
6. Click **OK** to save the preference.

### Automatic Reprocessing
- Wireshark re-dissects the packets using the session secrets from the selected key log.

## Verifying the Decryption

### Step 4: Check for HTTP/2
- Confirm that the previously encrypted application data is now decoded.

1. Return to the packet list.
2. Review the packets that previously appeared only as encrypted application data.
3. Look for packets identified as **HTTP/2**.
4. Apply the display filter `http2` if necessary.

### Decrypted Information
- Wireshark may now display:

  - HTTP/2 requests
  - Request paths and headers
  - Response status codes
  - Text strings
  - Transferred application data

### Result
- The appearance of HTTP/2 details confirms that Wireshark successfully decrypted the TLS traffic.

### Step 5: Inspect the Decrypted Session
- Examine the application data carried inside the TLS connection.

1. Select an HTTP/2 packet.
2. Expand the **Hypertext Transfer Protocol 2** details.
3. Review the request, response, headers, and data fields.
4. Use **Follow → TLS Stream** when available to inspect the reconstructed decrypted session.

## Troubleshooting Failed Decryption
- Check the capture and key-log relationship when the traffic remains encrypted.

### Possible Causes
- The key log does not match the captured TLS sessions.
- The wrong key log file was selected.
- The application did not record the required session secrets.
- The capture does not contain the required connection or handshake packets.
- The capture began after the TLS session was already established.
- The selected application or TLS library does not support session-key logging.
- The traffic uses another protocol, such as QUIC, that must be inspected differently.

### Collection Methods
- Obtain session secrets only from systems and connections you are authorized to inspect.

#### Client Collection
- Configure a supported client application to save session secrets using `SSLKEYLOGFILE`.

#### Server Collection
- A controlled server may record session secrets when its application and TLS library support key logging.

#### TLS-Inspection Proxy
- An authorized proxy or middlebox that terminates TLS may provide access to decrypted traffic or session secrets.

### Important Correction
- A passive packet-capture device cannot obtain TLS session secrets from network traffic alone.

#### TLS 1.3 Limitation
- Possessing only the server’s private certificate key is generally not enough to decrypt TLS 1.3 traffic because modern cipher suites use forward secrecy.

### Security Warning
- TLS session secrets can remove the confidentiality provided by TLS.

#### Safe Handling
- Use the key log only for authorized testing or investigation.
- Restrict access to the file.
- Do not use key logging on production systems.
- Delete the key log securely after completing the lab.

### Key Takeaway
- Wireshark can decrypt TLS traffic only when it has both the captured packets and their matching session secrets. After importing the key log under **Protocols → TLS**, previously encrypted packets can be decoded as HTTP/2 or another application protocol.

</details>