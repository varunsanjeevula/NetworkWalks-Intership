# Week 3: Password Cracking with John the Ripper and NetworkWalks Tools

![Kali Linux](https://img.shields.io/badge/Platform-Kali%20Linux-557C94?logo=kalilinux&logoColor=white)
![Focus](https://img.shields.io/badge/Focus-Password%20Auditing-red)

This repository documents **Week 3** of my Cybersecurity Internship at **NetworkWalks**. The task involved extracting password hashes from password-protected PDF files, auditing them with **John the Ripper** from the Kali Linux command line, and repeating the exercise with the **NetworkWalks Hash Calculator and Password Cracker**.

> **Authorization and safety:** These activities were performed only against sample files supplied for the educational exercise. Password cracking must be limited to systems and files that you own or have explicit permission to test.

## Contents

- [Objectives](#objectives)
- [Tools](#tools)
- [Part 1: John the Ripper](#part-1-john-the-ripper)
- [Part 2: NetworkWalks tools](#part-2-networkwalks-tools)
- [Results](#results)
- [Key learnings](#key-learnings)
- [Conclusion](#conclusion)

## Objectives

- Understand how password-protected PDF files store crackable password hashes.
- Extract PDF hashes with `pdf2john`.
- Use the `rockyou.txt` wordlist with John the Ripper.
- Display recovered passwords with `john --show`.
- Generate and inspect common hashes with the NetworkWalks Hash Calculator.
- Use the NetworkWalks tools to complete the same password-auditing workflow in a browser.

## Tools

| Tool | Purpose |
| --- | --- |
| Kali Linux terminal | Command-line password-auditing environment |
| John the Ripper 1.9.0 | Password recovery using a wordlist |
| `pdf2john` | Extracting a crackable hash from a protected PDF |
| `rockyou.txt` | Dictionary wordlist used for the audit |
| NetworkWalks Hash Calculator | Generating MD5, SHA-1, SHA-256, SHA-384, and SHA-512 hashes and extracting PDF hashes |
| NetworkWalks Password Cracker | Browser-based password-auditing exercise |

## Part 1: John the Ripper

### 1. Verify John the Ripper

The `john` command was run first to confirm that John the Ripper was installed and to view its usage information.

```bash
john
```

![John the Ripper usage output](screenshots/Screenshot%202026-09-26%20222726.png)

### 2. Extract the PDF password hash

The protected PDF was converted into a John-compatible hash file with `pdf2john`. The output was redirected to `pdf_hash1.txt`.

```bash
pdf2john "My-Locked-PDF2.pdf" > pdf_hash1.txt
```

The terminal listing confirms the presence of the input PDFs, the extracted hash files, and the `rockyou.txt` wordlist.

![Extract PDF hash with pdf2john](screenshots/Screenshot%202026-09-26%20223300.png)

### 3. Crack the first PDF hash

John was run with the `rockyou.txt` wordlist against `pdf_hash1.txt`.

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt pdf_hash1.txt
```

John identified the PDF hash format, loaded one password hash, and completed the audit. The recovered password shown in the terminal was:

```text
password1
```

The result was verified with:

```bash
john --show pdf_hash1.txt
```

![John cracking the first PDF](screenshots/Screenshot%202026-09-26%20223539.png)

### 4. Confirm the first result

The `john --show` output reported one cracked password and zero remaining hashes. A NetworkWalks completion page also confirmed the first exercise flag.

![First password-cracking completion](screenshots/Screenshot%202026-09-26%20223812.png)

### 5. Crack the second PDF

A second PDF hash was extracted into `pdf_hash2.txt` and audited with the same wordlist.

```bash
pdf2john "My-Locked-PDF3.pdf" > pdf_hash2.txt
john --wordlist=/usr/share/wordlists/rockyou.txt pdf_hash2.txt
john --show pdf_hash2.txt
```

The recovered password shown by John was:

```text
1qaz2wsx
```

John again reported one cracked password and zero remaining hashes.

![John cracking the second PDF](screenshots/Screenshot%202026-09-26%20224234.png)

![Second password-cracking completion](screenshots/Screenshot%202026-09-26%20224338.png)

## Part 2: NetworkWalks tools

The same type of exercise was also performed using the NetworkWalks browser tools. The Hash Calculator provides separate **Text**, **File**, and **PDF** workflows. The PDF workflow accepts a protected PDF locally and extracts a crackable hash without uploading the file.

![NetworkWalks Hash Calculator](screenshots/Screenshot%202026-09-26%20224842.png)

### Hash generation and inspection

The Hash Calculator produced the following digest types for the test input:

- MD5
- SHA-1
- SHA-256
- SHA-384
- SHA-512

The result screen also marked MD5 and SHA-1 as **not secure**, reinforcing that they should not be used for password storage. The page states that hashing was performed locally in the browser.

![NetworkWalks generated hashes](screenshots/Screenshot%202026-09-26%20224809.png)

### Password-cracking results

The NetworkWalks password-cracking activity was completed successfully, as shown by the two completion screenshots above. This provided a browser-based comparison with the command-line workflow and demonstrated the same overall process: obtain a crackable hash, test it against a wordlist or cracker, and verify the recovered result.

## Results

| Activity | Result |
| --- | --- |
| PDF hash extraction | Completed with `pdf2john` |
| First PDF | Cracked with `rockyou.txt`; password: `password1` |
| Second PDF | Cracked with `rockyou.txt`; password: `1qaz2wsx` |
| John verification | `1 password hash cracked, 0 left` for each file |
| NetworkWalks Hash Calculator | Generated MD5, SHA-1, SHA-256, SHA-384, and SHA-512 outputs |
| NetworkWalks browser exercise | Completion flags displayed successfully |

## Key learnings

- Password-protected PDFs can expose a crackable hash even though the document contents are encrypted.
- `pdf2john` prepares the extracted PDF hash for John the Ripper.
- Wordlist quality has a direct effect on password-auditing results.
- `john --show` is useful for confirming recovered credentials without rerunning the cracking process.
- Different tools can implement the same security workflow while presenting different levels of automation and visibility.
- MD5 and SHA-1 are unsuitable for secure password storage because they are considered weak for modern security requirements.

## Conclusion

This exercise provided practical experience with password auditing in both a Linux command-line environment and a browser-based NetworkWalks tool. John the Ripper successfully recovered the passwords for both sample PDFs using `rockyou.txt`, while the NetworkWalks tools demonstrated hash generation, PDF hash extraction, and completion of the corresponding password-cracking task.

## Internship details

| Field | Value |
| --- | --- |
| Internship | Cybersecurity Internship |
| Organization | NetworkWalks |
| Week | 03 |
| Focus | Password hashing and password auditing |
