# The Researcher's Toolkit for Email Archive Analysis

## Introduction

**Mailbox archives** are one of the most common formats in data leaks, often requiring investigation by investigative journalists. These archives typically consist of hundreds of files totalling gigabytes in size.

There are various archive formats, and while some of them are easily indexed and searched using standard tools, others **require specialized software**, such as importing into Outlook or Thunderbird.

While there are numerous online services for converting individual files, this approach is impractical for gigabytes of data.

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

## Preparation of mailbox archives

Large volumes of email archives are often distributed via torrents. However, there may not be any seeders to distribute the torrent to you, requiring you to manually download the files from a web interface. This process can be automated using download manager software.

Environment: **MacOS**:

Installation:
```sh
brew install wget
```

Downloading:
```sh
wget -m -np -nH --cut-dirs=1 -A '*' https://data.ddosecrets.com/Metprom%20Group/
```

Add `-c` flag option to continue downloading after any interruption.

## Convert PST to EML

Among the common types of email archives, PST is not the most popular, but it often appears in data leaks. Since popular tools like Datashare and Pinpoint do not recognize and index it, there arises the task of automatically extracting files from PST.

Environment: **MacOS:**

```sh
brew install libpst
```

Convert one file:

```sh
readpst -D -S -r -e -o OUTPUT_DIR PST_FILENAME
```

Convert all PST files in the current folder to EML and save them to folder `~/Datashare`:

```sh
for f in `ls *.pst`; do mkdir ~/Datashare/$f && readpst -D -o ~/Datashare/$f -S -r -e $f; done
```

## Convert EML/MSG to PDF

Despite EML being a popular format and easily opened by email clients, your search tool may only support regular PDFs.

We will use [email-to-pdf-converter](https://github.com/nickrussler/email-to-pdf-converter) project, which suggests CLI and GUI interfaces to convert files.

Environment: **MacOS** (You need Java installed)

```sh
brew install wkhtmltopd wget
wget https://github.com/nickrussler/email-to-pdf-converter/releases/download/2.6.0/emailconverter-2.6.0-all.jar
```

Convert one file + extract attachments:

```sh
java -jar emailconverter-2.6.0-all.jar FILENAME_EML_OR_MSG -a
```

Run the converter in the graphical interface:

```sh
java -jar emailconverter-2.6.0-all.jar -gui
```

<img width="812" alt="email-to-pdf-converter" src="https://github.com/soxoj/email-archive-analysis/assets/31013580/1ade502b-d9d5-4560-9009-5171e2bf305b">

## Convert PST to MBOX/EML

Converting PST to MBOX is somewhat of an exotic task because MBOX files are large, undivided files consisting of email headers, HTML, and Base64 encoded email bodies. However, this conversion might be necessary to prepare MBOX files for import into your email client.

### pstconv

**Warning: [pstconv](https://github.com/cjmach/pstconv) does not automatically handle directory encodings, which is critical for files in other languages**

Environment: **MacOS:** (You need Java installed)

```sh
wget https://github.com/soxoj/pstconv/raw/main/pstconv-0.9.7.jar
```

Convert one file to MBOX format (alternatively, you can put `eml` and have EML output):

```sh
java -jar pstconv-0.9.7.jar -i INPUT_FILE.pst -o OUTPUT_DIR -f mbox
```

Convert all PST files in the current folder to MBOX and save them to folder `~/Datashare`:

```sh
for f in `ls *.pst`; do java -jar pstconv-0.9.7.jar -i $f -o ~/Datashare -f mbox; done
```

## Convert MBOX to EML

Sometimes you may encounter archives consisting of MBOX files. These can be imported into Thunderbird; however, if you plan to use programs for automated searching, the large MBOX files may not be processed correctly and may not be fully indexed.

To resolve this issue, you can convert MBOX into a multiple EML files.

### mboxzilla

We will use a powerfull tool [mboxzilla](https://github.com/noelmartinon/mboxzilla) working with MBOX files in different ways: splitting, converting to EML, cleaning, etc.

Environments:
**MacOS**: [download](https://github.com/soxoj/mboxzilla/releases/download/macos-m2/mboxzilla)
**Linux**: [download](https://github.com/noelmartinon/mboxzilla/releases/download/1.3.0/mboxzilla_1.3.0_debian.bin)
**Windows x64**: [download](https://github.com/noelmartinon/mboxzilla/releases/download/1.3.0/mboxzilla_1.3.0_win64.exe)

Extract all emails from MBOX file:
```sh
mboxzilla -f MBOX_FILENAME -o OUTPUT_DIR -e
```
