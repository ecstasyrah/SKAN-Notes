<details>
<summary><strong>Lessons Learned and Moving Forward</strong></summary>

## Lessons Learned and Moving Forward

- Objective: convert investigation findings into implemented and validated security improvements.

### Apply Controls Throughout the Attack Chain

| Attack Stage | Improvement |
|---|---|
| Malicious Office macro | Restrict macros through policy; monitor execution with EDR |
| HTTP payload download | Inspect relevant traffic and detect known malicious payloads |
| C2 communication | Monitor suspicious destinations and connection behavior |
| Lateral movement | Monitor unusual client-to-client traffic and services |
| Firewall modification | Collect and investigate relevant configuration-change events |
| Ransomware encryption | Detect unusual file-change activity and disk I/O |
| Critical application tampering | Monitor integrity and define rapid protective actions |

### Office Macro Controls

- Group Policy can enforce Office macro restrictions.
- Appropriate Office administrative templates are required.
- Test intended settings and business exceptions.
- Policy configuration alone does not prove enforcement.

### Logging and Detection

- Centralize relevant logs in a SIEM.
- Endpoint telemetry, including Sysmon where configured, adds investigative context.
- Preserve logs centrally to reduce dependence on potentially cleared endpoint records.
- Combine antivirus, EDR, network controls, and monitoring; no single layer guarantees prevention.

### File-Activity Monitoring

- Focus on critical assets to control noise and workload.
- Compare activity with established baselines.
- Large spikes in file modifications or disk I/O may indicate encryption, but require investigation.

### Why Earlier Detection Matters

- In this scenario, ransomware distracts from the attacker’s manual tampering with satellite operations.
- Detecting encryption could still allow responders to protect critical services.
- Preapproved response actions reduce delays.

### Two Forms of Validation

1. **Post-incident monitoring**
   - Look for residual activity, reinfection, and attempted C2 connections.
   - Alert on relevant attempts even when the connection is blocked.

2. **Control testing**
   - Use authorized threat emulation to test whether the new controls stop or detect the observed techniques.
   - Fix gaps and exceptions, then retest.

### Incident Response Outputs

- Improved security configuration.
- Better SOC detections and monitoring.
- Updated response procedures.
- Prioritized scenarios for red-team testing.
- Recommendations for administrators and business owners.

### Exam Focus

- Lessons learned must produce actionable changes.
- Implementation must be followed by validation.
- Incident response feeds continuous improvement across security operations.
- Closing an incident does not end monitoring, preparation, or training.

</details>