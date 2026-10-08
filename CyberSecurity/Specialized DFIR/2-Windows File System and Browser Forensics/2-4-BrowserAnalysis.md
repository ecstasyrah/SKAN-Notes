## Introduction

- Examine browser artifacts on the **compromised system** to determine what the attacker did in the browser.
- Learn what **tools** can help in the analysis.
- Determine what the compromised user named **accounting** did within the browsers they used.
- Create a **timeline of activities** for forensic analysis.

<details>
<summary>Browser History Analysis</summary>

## NirSoft BrowsingHistoryView

- Takes in the history files of **all major browsers** and lets us view them together.
- Filters activity by:
  - **Dates and times**
  - **Contents of the URL**
  - **Type of browser**
- Exports browser history into different formats.

## History Files Examined

| Browser | History file |
|---|---|
| **Microsoft Edge** | `History` |
| **Internet Explorer** | `WebCacheV01.dat` |
| **Mozilla Firefox** | `places.sqlite` |

- The **accounting user did not have any legitimate activity**.
- Activity within these browser history files therefore requires investigation.

## Configure BrowsingHistoryView

1. Set the displayed time to **GMT or UTC**.
2. Open **Advanced Options**.
3. Select **Load history items from any time**.
4. Select **Load history from the specified history files**.
5. Add the folders and files containing the history.
6. In the demonstrated version, add **Microsoft Edge** history under **Chrome history files**, since Edge uses Chromium.

## Information Displayed

- **Browser type**
- **URL visited**
- **Visit time**
- **Page title**
- **Visit type**, such as a link or typed URL
- **Visit duration**, where available

## Findings

| Date | Activity | Interpretation |
|---|---|---|
| **February 7th** | Access to `Wallet.txt` and `Exodus_Wallet.pdf` appeared in IE history. | The attacker likely opened the files through Windows Explorer. |
| **February 7th–9th** | Banking-site activity, including `regions.com`. | Possible attempted or successful logons; history alone does not establish which. |
| **February 11th** | Microsoft Edge accessed `microsoft.com`, `regions.com` and `webmail.techkuber.com`. | Shows the sites accessed, but not why they were accessed. |
| **February 11th** | Access to the accounting user's Opera program directory. | Indicates another browser to investigate. |

- Approximately **57 history items** appeared across Internet Explorer, Microsoft Edge and Mozilla Firefox.
- Google Chrome required further analysis.
- Banking and webmail accounts could have belonged to the attacker or been compromised accounts; **this was not established**.

</details>

<details>
<summary>Google Chrome Analysis</summary>

## Obsidian Forensics Hindsight

- Processes **Chromium artifacts**, including Google Chrome and Microsoft Edge.
- Examines more than browser history to provide a broader view of browser activity.
- Provides **command-line and browser interfaces**.
- Exports data into:
  - **Excel**
  - **SQLite**
  - **JSON**
- Excel output **color codes the artifacts**.

## Command-Line Options Used in the Demo

| Option | Purpose |
|---|---|
| **-t UTC** | Makes all timestamps UTC. |
| **-f xlsx** | Outputs the results in Excel format. |
| **-b Chrome** | Identifies the browser as Chrome. |
| **-i** | Specifies the Chrome user profile data directory; the demo uses `Default`. |
| **-o** | Names the output file; the demo uses `accounting`. |
| **-nocopy** | Avoids copying the files during analysis, making processing faster. |

## Extracted Information

| Artifact | Results |
|---|---:|
| **URL records** | 1,083 |
| **Download record** | 1 |
| **Autofill records** | 26 |
| **Extensions** | 3 |
| **Login data records** | 13 |

- Output file: **`accounting.xlsx`**
- Hindsight reported a **cookie-processing failure**, although cookie records still appeared in the output.

## Output Columns

| Column | Information |
|---|---|
| **Artifact type** | Cookies, URLs, saved settings and other artifacts. |
| **UTC timestamp** | Time associated with the record. |
| **URL** | Associated website or resource. |
| **Title/Name/Status** | Details about the record. |
| **Data/Value/Path** | Additional artifact information. |

## Browser Activity

| Date | Activity |
|---|---|
| **February 7th** | Access to `regions.com` and `onlinepayments.regions.com`. |
| **February 8th** | Further access to `onlinepayments.regions.com`. |
| **During the examined period** | Access to `2ip.me`, apparently to check the IP address, and banking sites including Bank of America. |
| **February 10th** | Google login activity; success could not be determined. |
| **February 11th, 18:43:56 UTC** | Download of **`OperaSetup.exe`** from **`opera.com`**. |

## Interpret Cookies Carefully

- Cookies created around a visit can show related browser activity.
- **A cookie does not mean the attacker deliberately visited that site**.
- Other sites may have been loaded by the page being visited.
- Some cookie data was **encrypted**.
- Browser data may be inaccessible because it is **encrypted or gone**.

## Limits of the Findings

- Banking activity suggested possible logons or payments, but **successful authentication and money transfers were not confirmed**.
- The only download identified in Chrome was **OperaSetup.exe**.
- Other executables found during file system analysis were **not identified as Chrome downloads**.
- They may have been:
  - Transferred a different way.
  - Downloaded through another browser.
- **More forensic analysis is needed** to determine how they reached the system.

</details>

<details>
<summary>Conclusion</summary>

## Main Findings

- The investigation identified artifacts involving:
  - **Internet Explorer**
  - **Microsoft Edge**
  - **Google Chrome**
  - **Mozilla Firefox**
  - **Opera**
- The attacker accessed **multiple banking and webmail sites**.
- The purpose of those visits and ownership of the accounts remained **unknown**.
- Chrome download records showed that **OperaSetup.exe was downloaded**.
- The reason for using multiple browsers could not be determined.

## Continue the Investigation

- Examine **all browser artifacts**, beyond history alone.
- Correlate browser findings with **file system evidence**.
- Further analysis may reveal additional activity.
- **Next module:** The overall timeline from both file system and browser analysis.

</details>