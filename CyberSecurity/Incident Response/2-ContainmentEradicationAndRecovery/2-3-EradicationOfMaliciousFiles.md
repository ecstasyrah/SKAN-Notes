<details>
<summary><strong>Focus Efforts on the Endpoint</strong></summary>

## Focus Efforts on the Endpoint

- Objective: decide whether to rebuild affected systems or perform targeted cleanup.

### Rebuild Versus Cleanup

| Approach | Benefit | Limitation |
|---|---|---|
| Rebuild from trusted sources | Higher confidence in restoring endpoint integrity | Requires downtime and restoration planning |
| Targeted cleanup | May preserve critical services temporarily | Unknown artifacts or persistence may remain |

- Deleting known files, tasks, services, or registry entries cannot prove that every malicious component is gone.
- A trusted rebuild provides stronger assurance, but broader compromise may also require credential and infrastructure remediation.

### Business Decision

- Globomantics needs the data and continued availability of victim 2.
- The response therefore includes targeted cleanup while considering recovery and eventual migration.
- Residual risk must be explained and accepted by the appropriate decision-makers.

### Enterprise Execution

- Centralized endpoint agents can distribute remediation actions.
- Coordinate changes across affected systems.
- Preserve required evidence before removing artifacts.

### Exam Focus

- The decision depends on asset criticality, downtime tolerance, confidence in cleanup, and recovery options.
- Keeping a cleaned system operational should include monitoring and a plan to restore trust.

</details>

<details>
<summary><strong>Demo: Eradicating Host Persistence</strong></summary>

## Demo: Eradicating Host Persistence

- Objective: remove known scheduled-task persistence and interfere with malicious name resolution.

### Identify Persistence

- The malware repeatedly relaunches through the scheduled task `IAMNOTACAT`.
- `schtasks` can enumerate scheduled tasks.
- Closing the visible malware window does not remove the mechanism that starts it again.

### Velociraptor Remediation

1. Create a remediation hunt.
2. Select the relevant scheduled-task and sinkhole artifacts.
3. Configure the exact task name and domain entries.
4. Confirm authorization and intended targets.
5. Enable actual execution rather than a preview.
6. Launch and verify results.

- In the demonstrated artifact, `ReallyDoIt` distinguishes actual remediation from a dry run.

### Hosts-File Redirection

- Windows hosts-file location:
  - `C:\Windows\System32\drivers\etc\hosts`
- Mapping a hostname to `127.0.0.1` directs it to the local loopback address.
- The hosts file affects only that endpoint unless changes are deployed more broadly.

### Important Limitations

- Hosts-file entries match specific hostnames; blocking `iamironcat.com` does not automatically block `hello.iamironcat.com`.
- Malware using a direct IP address or another resolution method may bypass this approach.
- Removing a task does not necessarily terminate a process it already launched.
- Loopback redirection is not a replacement for network containment.

### Validate and Continue Hunting

- Confirm that the scheduled task is gone.
- Confirm that the intended hosts-file changes took effect.
- Investigate associated executables, scripts, and running processes.
- An additional batch file in `ProgramData` demonstrates that earlier detections missed artifacts.

### Exam Focus

- Removing one persistence mechanism does not prove complete eradication.
- Verify the outcome of remediation rather than relying only on a successful job status.

</details>

<details>
<summary><strong>Post Eradication Considerations</strong></summary>

## Post Eradication Considerations

- Known malicious artifacts may be removed while undiscovered code remains.

### Why Detections Can Miss Artifacts

- Attackers can change:
  - Function names.
  - Filenames and paths.
  - Embedded strings.
  - Encodings.
  - Payload types.
  - Persistence mechanisms.

- A clean result from one signature set is not proof of a clean system.

### When Immediate Rebuild Is Not Possible

1. Remove known malicious activity.
2. Maintain containment where required.
3. Monitor for recurrence and unexpected behavior.
4. Establish trusted replacement infrastructure.
5. Migrate validated data and dependencies.
6. Retire or rebuild the affected system.

### Exam Focus

- “No known indicators detected” and “confirmed trustworthy” are different conclusions.
- Temporary cleanup requires explicit residual-risk management.
- Recovery should restore confidence in systems, not merely their availability.

</details>