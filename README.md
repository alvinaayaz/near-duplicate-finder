# near-duplicate finder

Exact copies are easy. `Acme Ltd`, `ACME Limited`, `The Acme Company` and `Acme, Inc.` are the real mess: one company, four rows.

**Try it in your browser:** https://alvinaayaz.github.io/near-duplicate-finder/

Pick a CSV file, choose the column with the names, and it groups rows that are probably the same thing. You review the groups, choose which row to keep in each, and export a cleaned CSV. Nothing is uploaded: your list is read and compared inside your browser. It is one `index.html` file with no dependencies and no network requests.

Built with AI's help. It is the next step after my [downloads-toolkit](https://github.com/alvinaayaz/downloads-toolkit): that one finds exact copies of files, this one finds near-copies of names.

## How to use it

1. Choose a CSV file, or click "Try a sample list".
2. Pick the column to match on. It guesses a column called company, name, security, client and so on, or else the column that looks most like names. If the column you pick looks like short codes (tickers, IDs), it warns you.
3. Optional: "Require identifier columns to match". Select a ticker, customer ID, email or registration number, and two rows only match when every selected field is filled in and identical. Same-named companies with different IDs stay apart.
4. Set the similarity (85% is the default) and click "Find near-duplicates".
5. Review the groups. Clear matches start as "keep the best row, remove the rest". Groups the tool is less sure about start as "Keep all rows" and need your decision:
   - **Share-class warnings**: names that differ only by a class, like `Alphabet Inc. (Class A)` and `(Class C)`.
   - **Name variations**: the same name once cleaned, written differently, like `Ltd` vs `Limited`. "Approve all name variations" accepts them in one click.
   - **Fuzzy matches**: the weakest pair in the group scores under 90%.
   Use the filters, "Review next group", and Undo / Redo.
6. Download the cleaned CSV. A final step shows how many rows will be removed. You can also copy the CSV, and copy or download an audit report of every decision.

Your original file is never changed. Rows are only removed from the exported copy.

## How it matches

- Names are cleaned first: lower case, no accents, no punctuation, `&` becomes `and`, and words like Ltd, Limited, Inc, Corp, Co, Pvt, LLC, The, and, of are dropped.
- Two names are scored with word overlap and with edit distance, also on the words sorted alphabetically, so `Khan Brothers Trading` matches `Brothers Khan Trading`. The best score counts. Two swapped letters count as one typo.
- If the numbers in two names differ, they are never matched, so `Plant 1 Fabrication` and `Plant 2 Fabrication` stay apart.
- Up to 1,000 rows, every pair of names is compared. Above that it switches to a faster mode that only compares names starting with the same three letters or sharing the start of a word, and skips name parts that appear in more than 400 rows. It can miss pairs in that mode.
- Similarity is a clue, not proof. Look at the groups before you export.

## Export safety

Any cell that starts with `=`, `+`, `@` or `-` gets a leading apostrophe in the exported CSV, so a spreadsheet won't run it as a formula. Numbers (`-5.5`) and phone numbers (`+44 20 7946 0101`) are left exactly as they were. Names and notes from your file are shown on the page as plain text.

## Tested

- A 12-row sample list.
- A 67-row list of made-up companies with a known answer. It finds 11 groups. By default 8 rows are marked for removal and 6 groups wait for review. "Approve all name variations" takes that to 13, and approving everything gives the 17 I expected. `Atlas Steel Works` / `Atlas Steel Traders`, `First Capital Bank` / `First Capital Insurance` and `Apex Pharma` / `Apex Pharmacy` stay apart.
- 503 real company names from a public list of the S&P 500 ([source](https://github.com/datasets/s-and-p-500-companies)), untouched. At 85% it finds 3 groups, all share classes (Alphabet, Fox, News Corp), and removes nothing by default. At 80% it starts joining different companies (`CBRE Group` / `CME Group`), which is why 85% is the default.
- The same list with 70 sloppy variants added by me (upper case, `The`, `Inc.`, typos). It finds 71 groups: 68 of my 70 variants, with no wrong groups, plus the 3 share-class groups. The 2 it misses are typos in 6-letter names (`Kenvue` / `Kenuve`). By default 57 rows are marked for removal and 14 groups wait for review.
- I clicked through the page in a simulated browser (jsdom) on all three lists: column guess, matching, "Approve all name variations", Undo and the export. I also fed it names containing HTML and values starting with `=`, `@` and `+`.
- Speed on my test machine: about 1 second for 573 rows and under 2 seconds for 1,000 rows, with a "Comparing…" message while it works.

## Limits

- It cannot match abbreviations. `IBS Ltd` and `International Business Systems` share no letters.
- Short names are hard: one typo in a 6-letter name scores about 83%, under the default 85%. Lower the similarity and check the groups.
- Above 1,000 rows it uses the faster mode described above and can miss pairs. In one synthetic test it found 234 groups where the full comparison found 236.
- One name column at a time, CSV only (no Excel files), and the export keeps your file's delimiter.
- I have not tried files with tens of thousands of rows.
- I have not tested it on phones.

## License

MIT. See `LICENSE`.
