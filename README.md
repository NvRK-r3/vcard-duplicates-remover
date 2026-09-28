# vCard Duplicate Contacts Remover

A small Python command-line tool that finds and merges duplicate contacts in
a `.vcf` (vCard) file — the format exported by iPhone, Android, Outlook,
Google Contacts, and most address book apps.

## How duplicates are detected

Two contacts are considered duplicates if **any** of the following match:

1. **Exact phone number** (numbers are normalized — spaces, dashes,
   parentheses, and country-code prefixes are ignored, comparing the last
   10 digits).
2. **Exact email address** (case-insensitive).
3. **Similar name** (fuzzy match, e.g. "Jon Smith" vs "John Smith") — used
   only as a fallback, and only when no phone/email match already exists.

When contacts are merged, the tool keeps **all unique phone numbers and
emails** from the group, the longest/most complete name, and any
organization, title, address, or note fields it finds — so no information
is lost.

## Usage

```bash
python3 Dedupe-Contacts input.vcf -o output.vcf
```

### Options

| Flag | Default | Description |
|---|---|---|
| `-o`, `--output` | `deduped.vcf` | Where to write the cleaned vCard file |
| `--threshold` | `0.90` | Fuzzy name-match sensitivity (0–1). Lower = more aggressive merging |
| `--report` | `dedupe_report.txt` | Where to write a plain-text summary of what was merged |

### Example

```bash
python3 Dedupe-Contacts Sample_Contacts.vcf -o deduped.vcf --threshold 0.92
```

Output:

```
Loaded 5 contacts from sample_contacts.vcf

Merged 3 contacts -> kept 1: [John Smith, John Smith, Jon Smith]
Merged 2 contacts -> kept 1: [Jane Doe, Janet Doe]

Duplicate groups found: 2
Contacts removed: 3
Final contact count: 2

Cleaned vCard written to: deduped.vcf
Report written to: dedupe_report.txt
```

## Getting a .vcf file from your phone

- **iPhone**: Contacts app → select contacts (or "Select All") → Share →
  Mail/AirDrop the vCard to yourself.
- **Android**: Contacts app → Settings → Export → Export to .vcf file.
- **Outlook / Google Contacts**: Use the "Export" option in the contacts
  list and choose vCard (.vcf) format.

Then import the cleaned `deduped.vcf` back into the same app.

## License

MIT — see [LICENSE](LICENSE).
