## Introduction

- **Internet browsers** contain some of the best forensic information on a system.
- This module will examine:
  - Why we analyze **internet browser activity**.
  - What we can find when investigating browsers.
  - Where **browser artifacts** are located.
  - How to **forensically analyze the browser**.

<details>
<summary>Browser Analysis</summary>

## Why Analyze Browsers?

- **Regular users and attackers** use browsers to access the internet.
- Attackers may launch browsers to **access the internet or download tools**.
- Browsers can be involved in multiple phases of an attack:
  - Accessing a **phishing site**.
  - Being attacked by a **drive-by download**.
  - Downloading attack tools.
- Some applications, such as **Office**, use the browser when users access resources.

## Browser Artifacts

| Artifact | Forensic information |
|---|---|
| **History** | What sites were accessed and when; page titles, visit counts, and how the user reached a page. |
| **Cache** | Files accessed or downloaded while visiting web pages; may help reconstruct a page or examine its source code. |
| **Downloads** | Evidence of downloaded documents and files, including phishing documents or attack tools. |
| **Bookmarks** | Sites saved for later use and potentially when they were saved. |
| **Cookies and saved settings** | Clues about sites accessed; may remain after browsing history is cleared. |
| **Saved logins** | Information about sites and accounts used. |

## Investigation Examples

- **Cached phishing page:** May show what a page looked like after it has been taken down.
- **Bookmarked phishing site:** Helped identify the site and establish an investigation timeframe.
- **Attacker browser synchronization:** An attacker logged into Google, and Chrome downloaded their previous history, cookies, bookmarks and saved logins.

</details>

<details>
<summary>Internet Explorer</summary>

## Versions and Base Directory

- The course covers **Internet Explorer 10 and 11**.
- Versions before IE 10 worked differently.
- Base directory:

`%USERPROFILE%\AppData\Local\Microsoft\Windows\`

## Artifact Locations

Paths below are relative to the base directory.

| Artifact | Internet Explorer 10 | Internet Explorer 11 |
|---|---|---|
| **History** | `WebCache\WebCacheV01.dat` | `WebCache\WebCacheV01.dat` |
| **Cache** | `Temporary Internet Files\Content.IE5\` | `InetCache\` |
| **Cookies** | `Cookies\` | `InetCookies\` |
| **Downloads** | `WebCache\WebCacheV01.dat` | `WebCache\WebCacheV01.dat` |

## Database Format

- **WebCacheV01.dat** is an **Extensible Storage Engine database**.
- Tables within the database contain browser history and download information.
- Analysis tools can parse the database without requiring raw queries.
- Even if the user utilizes a different browser, **check the IE files** for valuable information.

</details>

<details>
<summary>Mozilla Firefox</summary>

## Profiles and Base Directory

- Windows base directory:

`%USERPROFILE%\AppData\Roaming\Mozilla\Firefox\Profiles\<profile name>\`

- The profile name is typically composed of **random characters** followed by a suffix such as `.default`.
- A user could have **several profiles**.
- **Examine each profile directory.**

## Artifact Locations

| Artifact | File or directory described in the course |
|---|---|
| **History** | `places.sqlite` |
| **Downloads** | `places.sqlite` |
| **Cookies** | `cookies.sqlite` |
| **Cache** | `Cache\` — location and name depend on the Firefox version. |

- **places.sqlite** and **cookies.sqlite** are SQLite databases.
- Firefox on other operating systems may have a **different base directory**.

</details>

<details>
<summary>Windows File Sys Brows Forensic Special Dfir M4 5</summary>

## Chromium-Based Browsers

- Many browsers use **Google's open-source Chromium project** as their base code.
- **Google Chrome and Microsoft Edge** use Chromium.
- Their similar artifact structures allow analysis tools to be reused across browsers.

## Google Chrome Base Directory

`%USERPROFILE%\AppData\Local\Google\Chrome\User Data\Default\`

## Artifact Locations

Paths below are relative to the browser profile directory.

| Artifact | File or directory |
|---|---|
| **History** | `History` |
| **Downloads** | `History` |
| **Cache** | `Cache\` |
| **Cookies** | `Network\Cookies` |

- **History** and **Cookies** are SQLite databases.
- Chromium-based **Microsoft Edge** uses the same artifact filenames, but its **base directory is different**.
- Tools that analyze Google Chrome artifacts can also be useful for examining Microsoft Edge artifacts.

</details>

<details>
<summary>Analysis Tips</summary>

## Create a Timeline

- Create a **timeline of suspicious activities within the browser**.
- Focus on the **timeframe of interest**.
- Look for:
  - Access to **phishing sites**.
  - Sites with **unusual TLDs**.
  - Downloaded files that could signify an **infection vector or attack tool**.

## Examine Searches and Login Information

- History files record **searches performed in the browser**.
- Searches may provide context about what an attacker or user was looking for.
- Login IDs or user information may be stored in:
  - **Saved logins**
  - **History files**
  - **Cookies**

## Deleted Browser History

- **Browser history can be deleted.**
- Deleting history does not mean **all other artifacts are deleted**.
- **Site settings** may remain after history removal.
- These may identify sites visited, even when individual pages cannot be determined.

## Incognito or Private Modes

- Private browsing may leave **little or no persistent browser history**.
- A browser or plugin crash may leave useful artifacts.
- **Network and proxy logging** may be needed to investigate browsing activity.

</details>

<details>
<summary>Conclusion</summary>

- Browser analysis should be part of a forensic investigation.
- Examine **history, cache, cookies, downloads, bookmarks and saved logins**.
- Each artifact provides different information about what happened.
- Browser artifacts are stored in **different locations and files**, depending on the browser.
- Check **all relevant browsers and profiles**.
- Create a **timeline** to focus on important questions.
- **Don't limit analysis to browser history**—other artifacts may open new avenues of investigation.
- **Next module:** Browser analysis on the compromised system.

</details>