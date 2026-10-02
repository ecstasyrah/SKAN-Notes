<details>
<summary>Assist in Recovery of Operations</summary>

## Malware Analysis Findings

- The malware analysis team used:
  - **Static and dynamic analysis of the executable**.
  - Captures of the **live RAM**.
  - **Packet traffic** that you pulled in initial triage.
- They are very confident that you can **use the executable to decrypt the data**.

## Ransomware Infrastructure Requirements

1. The executable needs to create a **unique identifier**:
   - An identifier for the **device and the network**.
2. Using either a **preshared or generated key**, files are encrypted.
3. If it is **preshared**:
   - There are potentially **artifacts of that key inside the executable**.
4. If a **generated key** is used:
   - That key needs to be shared with a **centralized server**.
   - Along with the **uniquely identifiable device information**.
5. Otherwise, they would not be able to **decrypt the data if the client pays up**.
   - There has to be some sort of way to decrypt said data.

## Why Recovery Was Possible

- **Did the ransomware portal actually work?**
  - No. It was deemed a **distraction**.
- The programmer would likely not write all of that code from scratch:
  - Buy it from a group that provides **ransomware as a service**.
  - Go to the evil version of GitHub or Stack Overflow and just copypasta until it works.
- **Code was reused or even not properly implemented**.
- **The key used for encryption was leaked**.

## Victim 2: Recovering the Data

- Globomantics needs that data from the **victim 2 server**.
- You started to clean up the mess that the ransomware made and **left it in service**.
- The catch:
  - You have to use the **original executable**.
  - It apparently has a usable **decrypt function**.

## Risks to Convey to Globomantics

- What confidence do you have in the **malware analysts**?
- What could the **potential consequences** be?

**Could this reinfect the system?**

- Sure.
- If you can un encrypt the files and **upload those to a new host to serve the same function**:
  - Maybe that is a viable risk mitigation.

**Could those unencrypted files now be themselves compromised?**

- The decrypt function could trigger **more obfuscated and malicious behavior**.
- You think that the files are decrypted and maybe they are:
  - Globomantics loads them up to a **new server**.
  - On a time delay, one of those files has a **hidden payload that calls back out**.

## Who Makes the Decision?

- **Is this your decision? No.**
- Your job is to **convey that risk**.
- Allow the **incident manager** to come to the decision with **Globomantics**.
- They were ready to just instantly pay the ransom.
- They approve of you **attempting to get back their data**.

</details>

<details>
<summary>Demo: Use Forensic Analysis to Recover Data</summary>

## Run the Decrypt Function

- Inside the **lab environment**:
  - Run the code in the way that the malware analyst thinks it can be used to **decrypt these files**.
- As soon as it goes to run:
  - An error because it **doesn't have access to the outside environment**.
- Reconstruct the situation in which this ran:
  - So that it can actively **connect back out to the internet**.

## Remove the Blocks in the Lab Environment

**Outbound rules**

- Get rid of these **outbound rules** that we created for **iamironcat.com**.

**Host file block**

1. Go into **drivers** inside **System32**, into the **etc** folder.
2. Change this to look for **all files**:
   - It's not labeled as a text document.
3. Go to **hosts**.
4. Velociraptor made a **backup of that file** when it did the change.
5. Delete the current host file.
6. Rename this backup the host file.

**Result**

- We won't be blocked when we try to go to **iamironcat.com**.
- With everything back in the state that it was before:
  - Use the **decrypt function**.

## Verify the Decryption

- Going through **every single file using the key**.
- Files popping up without the **.encrypted** ending.
- Wait for it to **finish decrypting everything**.

**Check the recovered files**

- The previously encrypted **ransom note**:
  - We open it, and it's good to go.
- **Icons**:
  - All of our icons are back or at least most of our icons are back.
- **Firefox**:
  - We have Firefox back.
- **Batch file**:
  - Back the way it's supposed to be.
- **Lab info**:
  - Lab info is here.
- **Documents that we needed**:
  - This looks like it's unencrypted as well.
  - The leak of dark energy being fake is now also unencrypted.

## What the Demonstration Shows

- **We were able to decrypt the data.**
- **Is this really realistic? No.**
- But there are versions of ransomware executions that allow you to:
  - **Pull keys** that can be used to recover **some of your data**.
- There are also **decrypters for a large number of ransomware**.
- That means:
  - You don't have to pay up.
  - You can recover the data.
- This is a version of that to try to **simulate it**:
  - Something you can follow along with in the **lab environment**.

</details>

<details>
<summary>Do Not Let This Happen Again</summary>

## Returning to Normal Operations

- Globomantics is happy, feeling like they've made it back to **normal operations**.
- But there are **dangers introduced that either can't or haven't been fully eradicated**.

## Malware Reverse Engineering

- Good **malware reverse engineering** is straight magic:
  - Magic that you can learn.
- Pluralsight has a **malware analysis path**.

## Active Directory and Golden Tickets

- In an **Active Directory-based environment**:
  - If the attackers had gained **domain admin**:
  - They likely were able to create what's called a **golden ticket**.
- Regardless of if you change the **accounts and passwords**:
  - They can simply generate **valid Kerberos tickets**.

> **Technical clarification:** The transcript says to “roll the domain controllers” so the “KRBTGT value changes.” The precise remediation is to reset the **KRBTGT account password twice**, allowing proper replication and timing between resets. Changing ordinary user passwords or restarting domain controllers does not invalidate golden tickets.

## Lessons Learned and Mitigations

- That's just one example of a number of in-depth concepts under:
  - **Digital forensics and incident response**.
- Work with Globomantics to:
  - **Implement mitigations based on the lessons learned from this intrusion**.

</details>