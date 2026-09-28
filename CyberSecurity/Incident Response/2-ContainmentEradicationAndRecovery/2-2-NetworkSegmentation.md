<details>
<summary><strong>Decide on Effective Network Controls</strong></summary>

## Decide on Effective Network Controls

- Objective: select containment controls that block attacker access while preserving essential operations.

### Consider Network Architecture

- Identify every route from affected systems to attacker infrastructure.
- A rule on one firewall may be insufficient if other internet paths exist.
- Include alternate gateways, cloud networks, and relevant internal routes.
- Cloud controls may operate at interface, instance-group, or subnet boundaries.

### Business Constraints

- Globomantics must maintain essential satellite communications.
- Disconnecting all external access is therefore not a workable blanket solution.
- Targeted controls must be combined with endpoint remediation.

### Decision Process

1. Identify the destinations and services that must be blocked.
2. Locate enforcement points covering every relevant route.
3. Confirm which essential connections must remain available.
4. Coordinate changes with administrators and the incident manager.
5. Validate both the security effect and business functionality.

### Exam Focus

- A correct rule is ineffective if placed where the traffic does not pass.
- Required access and authorization depend on the responder’s engagement.
- Network containment must cover the actual architecture, not just the expected diagram.

</details>

<details>
<summary><strong>Demo: Cut Off Network Access</strong></summary>

## Demo: Cut Off Network Access

- Objective: block known C2 and ransomware communication through coordinated network controls.

### Direct and Daisy-Chained Implants

- A direct implant communicates with the C2 server itself.
- A daisy-chained implant communicates through another compromised device.
- Blocking the shared external connection can interrupt dependent implants.
- This interrupts communication; it does not delete implants or prove no alternate channels exist.

### AWS Controls in the Lab

| Control | Important Property |
|---|---|
| Security group | Stateful; uses allow rules |
| Network ACL | Stateless; supports allow and deny rules |
| Network ACL association | Applies at subnet boundaries within a VPC |

- Network ACL rules are evaluated in ascending rule-number order.
- The first matching rule determines the action.

### Rule Ordering Example

- Rule `90`: deny the known C2 connection.
- Rule `91`: deny communication to the ransomware infrastructure.
- Rule `100`: allow other traffic.

- Placing the deny below a matching allow would prevent the deny from taking effect.

### Lab Indicators

- The demo identifies the simulated C2 as `172.31.101.111` on TCP `443`.
- This is a private lab address representing attacker infrastructure.
- `hello.iamironcat.com` resolves in the lab to `52.251.113.157`.
- Verify exact indicators against evidence: the transcript contains inconsistent C2 address wording.

### Validate Results

- Check whether known C2 communication stops.
- Confirm essential business traffic still works.
- The lab ransomware fails to start encryption when its required remote connection is unavailable.

### Exam Focus

- Internet access is not a universal requirement for ransomware.
- Blocking a domain’s current IP may affect shared hosting and may become ineffective if the domain changes addresses.
- A blocked connection does not establish that the endpoint is clean.

</details>

<details>
<summary><strong>Next Steps in Denying the Attacker Access</strong></summary>

## Next Steps in Denying the Attacker Access

- Blocking known C2 communication reduces the attacker’s ability to issue remote commands through those channels.

### What Can Remain?

- Running malicious processes.
- Scheduled tasks.
- Malicious services.
- Copied executables and scripts.
- Other persistence mechanisms.
- Dormant code triggered by a timer or event.

### Manual Versus Automated Activity

| Activity | Example |
|---|---|
| Interactive attacker activity | Commands delivered through C2 |
| Automated malware activity | A scheduled task relaunching ransomware |
| Delayed activity | Code executing after a timer or other trigger |

### Exam Focus

- Network isolation buys time but does not remove malware.
- Automated actions may continue without a working C2 connection.
- Endpoint eradication and continued monitoring remain necessary.

</details>