# The Researcher's Toolkit for Email Archive Analysis

_Processing PST mailbox archives for Investigative Research_

Among the common types of email archives, PST is not the most popular, but it often appears in data leaks. Since popular tools like Datashare and Pinpoint do not recognize and index it, there arises the task of automatically extracting files from PST.

While there are numerous online services for converting individual files, this approach is impractical for gigabytes and terabytes of data.

**This guide will explain how to research large amounts of non-standard mailbox archives** using the example of the [Metprom email breach](https://ddosecrets.substack.com/p/release-metprom-184-gb).

<details>
  <summary>Reference: mailbox formats</summary>
  - PST (Personal Storage Table) - Used by Microsoft Outlook to store email messages, attachments, folders, and other items on a local computer.
  - OST (Offline Storage Table) - Also used by Microsoft Outlook; it allows users to work offline and then synchronize changes with the email server once online.
  - MBOX - A generic file format for storing email messages where each file can contain multiple emails. It is used by various email clients such as Mozilla Thunderbird, Apple Mail, and formerly by Eudora and Entourage.
  - EML - Files typically used by email clients like Microsoft Outlook Express, Windows Mail, and others where each file contains a single email message.
  - DBX - Used by older versions of Microsoft Outlook Express, where each DBX file represents a mail folder.
  - MBX - Older file format used by Eudora and some other email clients to store emails.
  - NSF (Notes Storage Facility) - Used by IBM Lotus Notes to store emails along with other items like calendar entries and contacts.
  - MSG - Represents a single email message saved in Microsoft Outlook. It can include attachments and rich text formatting.
</details>

## Convert PST to EML

**MacOS:**

`brew install libpst`

Usage for one file:

`readpst -D -S -r -e -o OUTPUT_DIR PST_FILENAME`
