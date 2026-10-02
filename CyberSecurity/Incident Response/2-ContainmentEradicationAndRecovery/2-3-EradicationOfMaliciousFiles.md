<details>
<summary>Focus Efforts on the Endpoint</summary>

## Completely Wipe and Reload

- It is impossible to be **100% positive that a device has been cleaned** simply by deleting:
  - Files
  - Processes
  - Services
  - Tasks
  - Keys
  - Anything else that you found associated with an intrusion
- **The only way to be sure that you have fully removed any malicious code from an endpoint is to completely wipe and reload.**

## Endpoint Eradication Plan

Take into consideration:

- Where it makes sense to spend time **trying to clean up**:
  - Mitigate the risk with **monitoring and detection**.
- Where it makes more sense to save that time and just **wipe the system and rebuild**.

## Acceptable Risk Limits

- Globomantics may not care to be **100% positive** that they have removed even the chance that some advanced nation state actor rode along this intrusion and left some spyware.
- That may fall within their **acceptable risk limits**.
- The alternative would be to **wipe a system that they can't afford to go down**.

## Victim 2

- Devices in the Globomantics network that show **open port 8080**:
  - At least means that they've been compromised with the ransomware.
- **Victim 2**:
  - They have to have that data.
  - They can't afford for the machine to be wiped.
- **Malware analysis team**:
  - They may be able to save the files.
  - They need you to clear the infected device first.

## Endpoint Agents

- An **endpoint agent on every device** in the Globomantics network.
- Manage these agents from a **centralized location**.
- Take actions on **all of the affected devices at the same time**.

</details>

<details>
<summary>Demo: Eradicating Host Persistence</summary>

## Identify the Scheduled Task

- **Console host popping up**:
  - Even if we exit out of it, it just keeps coming back every minute or so.
  - Associated with a **scheduled task**.
- **schtasks**:
  - That'll list everything out.
- **IAMNOTACAT**:
  - The running IAMNOTACAT task.
  - On every single system that the ransomware executed on.
- **Remove this scheduled task from all of the endpoints all at the same time**:
  - Or it's just going to continue to open back up and call back out.

## Create the Remediation Hunt

1. Use **Velociraptor**.
2. Make a **new hunt**.
3. Call this one **Remediation**.
4. Go to **Select Artifacts**.
5. Look for a set of plugins that are **remediation plugins**:
   - Windows Remediation Quarantine
   - Scheduled Tasks
   - Sinkhole
6. Configure these parameters:
   - **Each one has to be configured individually.**

## Configure Sinkhole

- **iamironcat.com**:
  - Tell it not to call out.
  - Not to allow DNS to go reach out to anything at iamironcat.com.
- **Host file**:
  - Will only affect this individual host, not any other hosts.
  - Use Velociraptor to change this host file across all the endpoints that it exists on.
- Change **evil.com** to **iamironcat.com**.
- Whenever it reaches out there, send it to **127.0.0.1**:
  - The **loopback address**.
  - Sending them back to the individual box.
  - That traffic is going to fail.

## Configure Scheduled Tasks

1. **Delete all of the tasks by name**.
2. Put in the **IAMNOTACAT** name.
3. Check the **task path**:
   - Make sure that some older versions of Windows didn't store the tasks in this location.
4. **Delete these arguments**:
   - So you're not worried about these arguments associated with this task.
5. **ReallyDoIt**:
   - The difference between seeing what happens if you did run this versus **actually deleting the task**.

## Permission Before Remediation

- Depending on the engagement:
  - You really want the administrators to do this.
  - They may be fine with you taking this action on the device.
- **Their IT staff needs to be aware before you do anything that actually changes their environment.**

## Launch and Verify

1. Review our settings.
2. Save this hunt if you want to reuse it later.
3. Go to **Launch**.
4. Click **Play**.
5. Wait until it's finished.
6. Take a look at the **notebook**:
   - Identify that it did successfully add this host name to the localhost file for this specific device.
7. Check the scheduled task using that same command "schtasks" :
   - **The IAMNOTACAT scheduled task is no longer there.**

## Execute Across All Endpoints

- Execute things across **all of the endpoints at the same time**.
- Use that **centralized server**.
- It could be anything you're using for your **endpoint detection response** that has an **active or remediation capability**:
  - Remove the scheduled task.
  - Set that file for the host file all at the same time.

## Remaining Malicious Files

- **Program data folder**:
  - There were some batch files.
  - One of the places that the batch file called out to that was in the scheduled task.
  - A **hidden folder**, so you have to type it in directly to get there.
- **mjfy**:
  - This specific batch file.
  - Didn't show up in any of our other detections.
- **It's impossible to know for sure that something's completely clean.**
- You really would prefer to **wipe everything**.
- If you can't do that:
  - Do the best job you can with eradication.
  - **Keeping services up with a plan to still wipe and go back to something else.**

</details>

<details>
<summary>Post Eradication Considerations</summary>

## Changing Function Names

- Looking for **specific function names in a binary**.
- That binary is **deployed individually to each device**.
- The logic could easily be created to:
  - **Patch those names with different names for each deployment.**

## Breaking File Patterns

- There is a **pattern to the names of the files dropped**.
- A **false sense of confidence**:
  - Hiding sets of files completely broken from the pattern.
- Instead of long names:
  - **They are short.**
  - **They use a different encoding.**
- Any number of options exist.

## Recovery Plan

- **Some devices simply can't be wiped and reloaded at any given moment.**
- Part of the recovery plan has to include:
  - **Standing up alternate clean infrastructure.**
  - **Swapping over dependency as soon as possible.**
- You removed:
  - **The attacker.**
  - **Any code that you could find.**

</details>