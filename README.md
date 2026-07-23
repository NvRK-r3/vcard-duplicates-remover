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

## Installation

```bash
git clone https://github.com/<your-username>/vcard-duplicate-remover.git
cd vcard-duplicate-remover
pip install -r requirements.txt
```

## Usage

```bash
python3 dedupe_contacts.py input.vcf -o output.vcf
```

### Options

| Flag | Default | Description |
|---|---|---|
| `-o`, `--output` | `deduped.vcf` | Where to write the cleaned vCard file |
| `--threshold` | `0.90` | Fuzzy name-match sensitivity (0–1). Lower = more aggressive merging |
| `--report` | `dedupe_report.txt` | Where to write a plain-text summary of what was merged |

### Example

```bash
python3 dedupe_contacts.py sample_contacts.vcf -o deduped.vcf --threshold 0.92
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

## Alternative: SysTools Duplicate Contacts Remover (commercial GUI tool)

If you'd rather use a point-and-click desktop application instead of this
script, **SysTools Duplicate Contacts Remover** is a paid Windows utility
that does a similar job with a graphical interface.

**What it offers:**
- Supports several contact file formats, including vCard/VCF, MAB, LDIF, and SQLite, so it can handle exports from more than just phones.
- Lets you filter and match duplicates by fields such as email address, phone number, first name, last name, or date, similar to the matching logic this script uses.
- Offers two actions once duplicates are found: permanently delete them, or copy the duplicates into a separate file instead of deleting them outright.
- Can process multiple contact files at once, which is useful if you're merging exports from several devices or accounts.
- It's Windows-only and commercial (a free demo is typically limited in how many contacts it will process — check the vendor site for current pricing/limits).

**Basic steps to use it:**
1. Download and install SysTools Duplicate Contacts Remover from the vendor's site.
2. Launch the program, then use "Add File(s)" or "Add Folder" to load your contacts files (.vcf, .mab, etc.).
3. Open the advanced options to choose which fields (email, phone, name, date, etc.) determine a duplicate.
4. Choose whether to permanently delete duplicates or export them to a separate file instead.
5. Click the dedupe/remove button to run the process, then review the cleaned output file.

> This is a third-party commercial product, not affiliated with this
> repository. See the vendor's official page for current features, system
> requirements, and pricing: https://www.systoolsgroup.com/duplicate/contacts-remover/

**When to use which:**
- Use **this script** if you're comfortable with the command line, want a
  free/open-source and scriptable solution, or need to automate dedup as
  part of a pipeline.
- Use **SysTools** if you prefer a GUI, need to handle non-VCF formats
  (MAB, LDIF, SQLite) in one tool, or want built-in filters without writing
  any code.

## Getting a .vcf file from your phone

- **iPhone**: Contacts app → select contacts (or "Select All") → Share →
  Mail/AirDrop the vCard to yourself.
- **Android**: Contacts app → Settings → Export → Export to .vcf file.
- **Outlook / Google Contacts**: Use the "Export" option in the contacts
  list and choose vCard (.vcf) format.

Then import the cleaned `deduped.vcf` back into the same app.

## Running the tests

```bash
python3 -m pytest test_dedupe.py -v
```

## License

MIT — see [LICENSE](LICENSE).
