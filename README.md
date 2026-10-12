# Reconstructing Chrome Web History — Digital Forensics Case Study

**Course:** SBT-DF204 — Computer Forensics Case Studies  
**Case Study:** Case Study 2 — Reconstructing Suspected Drug Trafficking From Chrome Web History  
**Investigator:** Athanasius Alekwe  
**Student ID:** 2025/FWSD/11230  
**Institution:** International Cybersecurity and Digital Forensics Academy (ICDFA)  
**Examination Environment:** Kali Linux, VMware Workstation, SQLite3

---

## Project Overview

This project documents a digital forensic investigation conducted as part of the SBT-DF204 Computer Forensics Case Studies course.

The investigation involved examining a supplied Google Chrome History SQLite database to reconstruct browsing activity associated with a suspected drug trafficking case.

The primary objective was to identify and analyse browser records relating to online advertisements, email communications, cryptocurrency transactions, and downloaded files.

All findings were obtained through forensic examination of the supplied database without interacting with live websites, accounts, or cryptocurrency wallets.

The investigation focuses on distinguishing recorded browser activity from assumptions about the identity or intentions of the individual operating the device.

---

## Investigation Objectives

The objectives of this investigation were to:

1. Preserve the original Chrome History database and verify evidence integrity.
2. Create a forensic working copy for examination.
3. Identify the relevant SQLite database tables.
4. Extract and analyse Chrome browsing records.
5. Convert Chrome WebKit timestamps into UTC.
6. Examine browser navigation transitions and relationships.
7. Investigate Craigslist posting activity.
8. Examine Gmail communication-related records.
9. Identify cryptocurrency-related browsing activities.
10. Examine downloaded files and their associated URLs.
11. Reconstruct an evidence-based browsing timeline.
12. Document findings, limitations, and conclusions.

---

## Laboratory Environment and Tools

The investigation was conducted using the following tools:

| Tool | Purpose |
|------|---------|
| Kali Linux | Forensic investigation environment |
| VMware Workstation | Virtual machine hosting |
| SQLite3 | Chrome History database examination |
| Linux Terminal | Executing forensic commands |
| SHA-256 | Evidence integrity verification |
| SQL Queries | Extracting and correlating browser records |
| CSV | Exporting the reconstructed timeline |

---

## Evidence Acquisition and Preservation

The supplied evidence consisted of a Google Chrome History SQLite database named `History`.

To maintain evidence integrity, separate directories were created for the original database, working copy, screenshots, SQL results, and investigation report.

### Investigation Directory Structure

```text
SBTDF204_CaseStudy2/
|
|-- Original_Evidence/
|
|-- Working_Copy/
|
|-- Screenshots/
|
|-- SQL_Results/
|
|-- Report/
```

The original database was preserved, and a separate working copy was created for examination.

SHA-256 hashes were calculated for both files.

### Evidence Verification Results

| Evidence Property | Result |
|-------------------|--------|
| Evidence File | History |
| Database Format | SQLite 3.x |
| Original File Size | 196,608 bytes |
| Working Copy Size | 196,608 bytes |
| SHA-256 Comparison | Matching |
| Database Integrity Check | Passed |

The matching hash values confirmed that the working copy was identical to the preserved original at the time of verification.

---

## SQLite Database Examination

The Chrome History database was examined using SQLite3.

The examination identified 17 database tables.

Four tables were particularly important:

| Table | Description |
|-------|-------------|
| urls | Stores recorded website addresses and page titles |
| visits | Contains browsing timestamps and navigation information |
| downloads | Stores downloaded file information |
| downloads_url_chains | Contains URLs associated with downloads |

### SQL Query — Identify Database Tables

```sql
SELECT name
FROM sqlite_master
WHERE type = 'table'
ORDER BY name;
```

### SQL Query — Examine Table Structure

```sql
PRAGMA table_info(urls);

PRAGMA table_info(visits);

PRAGMA table_info(downloads);

PRAGMA table_info(downloads_url_chains);
```

### Database Record Counts

The examination identified:

| Record Type | Total |
|-------------|-------|
| URL Records | 57 |
| Browser Visits | 74 |
| Downloads | 1 |
| Download URL Chains | 1 |

---

## Browser History Extraction

Browser records were extracted by joining the `visits` and `urls` tables.

This allowed the investigation to identify:

- Individual visit IDs
- URL identifiers
- Recorded website addresses
- Page titles
- Visit timestamps
- Navigation transitions
- Relationships between browser visits

### SQL Query — Browser History Extraction

```sql
SELECT
    v.id AS visit_id,
    u.id AS url_id,
    datetime(
        v.visit_time / 1000000 - 11644473600,
        'unixepoch'
    ) AS visit_utc,
    v.from_visit,
    v.transition,
    (v.transition & 255) AS base_type,
    u.title,
    u.url

FROM visits AS v

JOIN urls AS u
ON v.url = u.id

ORDER BY v.visit_time, v.id;
```

Chrome WebKit timestamps were converted into Coordinated Universal Time (UTC).

The recorded browsing activity occurred on:

**19 April 2022**

**Timeline:** 13:54:18 UTC – 15:02:56 UTC

---

## Investigation Findings

### 1. Craigslist Posting Activity

The examination identified browser visits associated with Craigslist.

The records showed navigation through several posting-related pages, including:

- Category selection
- Posting creation
- Location and map information
- Posting confirmation
- Posting management

A listing titled **"Cheaper than Rx Supplements"** was identified.

The recorded posting identifier was:

`7473121658`

The matching identifier in the posting management and listing records provided evidence of a connection between these pages.

However, the complete advertisement content was not recovered.

### 2. Gmail Communication-Related Activity

Following the Craigslist activity, browser records showed visits to Gmail and Google account pages.

Several Gmail page titles contained references to:

**"cheaper than Rx supplements - order"**

Additional records included compose-related navigation and cryptocurrency transaction references.

These findings established email-related browsing activity potentially associated with the earlier advertisement.

However, the browser history did not provide complete message contents or independently confirm that messages were sent.

### 3. Imgur Image Download

The investigation identified one recorded file download.

**Filename:** `proof_of_payment.png`

**Download Details:**

| Property | Value |
|----------|-------|
| Download ID | 1 |
| Date | 19 April 2022 |
| Start Time | 14:56:58 UTC |
| End Time | 14:57:10 UTC |
| File Size | 33,844 bytes |
| File Type | PNG Image |
| Download Status | Completed |
| Recorded Source | Imgur |

The download record was linked to an Imgur-hosted image.

Although the filename suggested possible payment-related material, the image contents were not independently examined.

### 4. Cryptocurrency-Related Browsing

The browser history contained records associated with several cryptocurrency platforms.

These included:

- Blockchain.com
- Kraken
- Mempool.space

The records showed navigation to cryptocurrency transaction pages, address-related pages, exchange account interfaces, and transaction-history pages.

A cryptocurrency transaction identifier appeared across selected Gmail-related and blockchain explorer records.

This provided evidence of a relationship between the recorded browsing activities.

However, the investigation did not independently establish wallet ownership or confirm that a cryptocurrency payment was completed.

---

## Reconstructed UTC Timeline

The following table summarises significant events identified during the investigation.

**Date: 19 April 2022 (UTC)**

| UTC Time | Activity |
|----------|----------|
| 13:54:18 | Google search for Craigslist |
| 13:55:10 | Craigslist category selection |
| 13:55:15 | Craigslist posting creation |
| 13:58:22 | Craigslist posting confirmation |
| 13:58:31 | "Cheaper than Rx Supplements" listing |
| 14:04:12 | Gmail order-related thread |
| 14:56:47 | Gmail transaction-related reference |
| 14:56:52 | Imgur browsing activity |
| 14:56:58 | Image download begins |
| 14:57:10 | Image download completion recorded |
| 14:59:06 | Blockchain.com transaction-related navigation |
| 15:00:21 | Kraken device-approval page |
| 15:01:33 | Mempool.space transaction-related page |
| 15:02:05 | Kraken transaction-history interface |
| 15:02:56 | Return to Gmail |

The timeline was reconstructed using timestamps obtained directly from the Chrome History database.

---

## Browser Navigation Analysis

The investigation examined Chrome navigation transitions and relationships between recorded visits.

Particular attention was given to:

- Link navigation
- Form submissions
- Browser-generated navigation
- Redirects
- Preceding visit references

The `transition` field was examined to identify basic navigation types.

### SQL Expression

```sql
(v.transition & 255) AS base_type
```

The `from_visit` field was used to examine navigation relationships between recorded webpages.

Several Craigslist records contained form-submission transitions, supporting the reconstruction of an interactive posting workflow.

---

## Evidence Documentation

A total of **19 evidence-log entries** were documented during the investigation.

### Evidence Log Summary

| Evidence ID | Description |
|-------------|-------------|
| E01 | Evidence acquisition and SHA-256 verification |
| E02 | SQLite database identification and integrity |
| E03 | Database table identification |
| E04 | URLs table examination |
| E05 | Visits table examination |
| E06 | Downloads table examination |
| E07 | Download URL-chain examination |
| E08 | Database record counts |
| E09 | Browser history extraction and UTC conversion |
| E10 | Navigation and transition analysis |
| E11 | Browser history records 16–35 |
| E12 | Browser history records 36–55 |
| E13 | Browser history records 56–74 |
| E14 | Download record examination |
| E15 | Download URL-chain analysis |
| E16 | Craigslist navigation analysis |
| E17 | Communications and cryptocurrency navigation |
| E18 | Browser navigation correlation |
| E19 | Complete timeline export and verification |

The investigation report also contains **22 labelled screenshots**, documenting the forensic examination process.

---

## Investigation Limitations

The following limitations were identified:

1. The investigation relied on the supplied Chrome History database.
2. The recorded browser activity did not establish the identity of the individual operating the device.
3. Complete Gmail message contents were not recovered.
4. The downloaded image was not independently examined.
5. Cryptocurrency transactions were not independently verified.
6. Some navigation records contained redirects or lacked linked preceding visits.
7. Browser history may not contain every activity performed on the device.

These limitations were considered when interpreting the recovered evidence.

---

## Attribution Assessment

The investigation established a high level of confidence in the recorded browser-history sequence.

Several browsing activities were connected through matching identifiers, timestamps, and navigation relationships.

However, the available evidence was insufficient to conclusively identify the individual operating the browser or establish that the suspected criminal activity occurred.

The findings therefore represent a reconstruction of recorded browser activity rather than definitive proof of criminal conduct.

---

## Conclusion

The forensic examination successfully reconstructed the Chrome browser activity recorded on 19 April 2022.

The investigation identified a sequence involving Craigslist posting activity, Gmail-related browsing, an Imgur image download, and visits to cryptocurrency-related platforms.

SQL queries, timestamp conversion, navigation analysis, and download examination helped establish the chronological relationships between these activities.

Although the recovered records were consistent with aspects of the suspected case scenario, they did not independently prove that controlled substances were sold or that a cryptocurrency payment was completed.

The investigation demonstrates how browser-history artefacts can support digital forensic reconstruction while highlighting the importance of evidence integrity, accurate documentation, and cautious interpretation.

---

## Ethical Considerations

This investigation was completed strictly for educational and authorised forensic purposes.

The examination was limited to the supplied evidence.

No attempts were made to access live accounts, communicate with individuals mentioned in the records, or interact with cryptocurrency wallets.

The original evidence database and forensic working copy should remain outside any public GitHub repository.

Only appropriately redacted investigation reports, screenshots, and supporting materials should be published.

---

## References

International Cybersecurity and Digital Forensics Academy (ICDFA).

SBT-DF204 — Computer Forensics Case Studies.

Case Study 2: Reconstructing Chrome Web History.

Student Assessment Brief.

---

## Author

**Athanasius Alekwe**

**Student ID:** 2025/FWSD/11230

**Course:** SBT-DF204 — Computer Forensics Case Studies

**Institution:** International Cybersecurity and Digital Forensics Academy (ICDFA)

---

**Disclaimer:** This repository documents an academic digital forensic investigation. The findings are based on the supplied training evidence and should not be interpreted as proof of criminal activity by any individual.
