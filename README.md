# near-duplicate finder

Exact copies are easy. `Acme Ltd`, `ACME Limited`, `The Acme Company` and `Acme, Inc.` are the real mess: one company, four rows.

**Try it in your browser:** https://alvinaayaz.github.io/near-duplicate-finder/

Pick a CSV file, choose the column to match (a company or name column), and it groups rows that are probably the same thing. You choose which row to keep in each group, then export a cleaned CSV. Nothing is uploaded: your list is read and compared inside your browser. It is one `index.html` file with no dependencies.

Built with AI's help. This is the next step after my [downloads-toolkit](https://github.com/alvinaayaz/downloads-toolkit): that one finds exact copies of files, this one finds near-copies of names.

## How to use it

1. Choose a CSV file, or click "try a sample list".
2. Pick the column to match on. It guesses a column called company, name, organisation, client and so on.
3. Set the similarity (85% is the default) and click "Find near-duplicates".
4. In each group, pick the row to keep. The default is the row with the most filled cells. Tick "not duplicates" to keep every row in a group.
5. Download or copy the cleaned CSV. Your original file is never changed.

## How it matches

- Names are cleaned first: lower case, no accents, no punctuation, `&` becomes `and`, and words like Ltd, Limited, Inc, Corp, Co, Pvt, LLC, The, and, of are dropped.
- Two names are scored with word overlap and with edit distance, also on the words sorted alphabetically, so `Khan Brothers Trading` matches `Brothers Khan Trading`. The best score counts.
- If the numbers in two names differ, they are never matched, so `Plant 1 Fabrication` and `Plant 2 Fabrication` stay apart.
- Rows that score above the similarity are joined into groups.

## Tested

- A 12-row sample list, in Edge from a local file.
- A 67-row list of made-up companies with a known answer. At 85% it found all 8 groups I planted (typos, accents, `&` vs `and`, `Ltd` vs `Limited`, word order), plus 3 more pairs (two typos, and `Sunrise Bakers` / `Sunrise Bakery`, which is a judgment call). It kept `Atlas Steel Works` / `Atlas Steel Traders`, `First Capital Bank` / `First Capital Insurance` and `Apex Pharma` / `Apex Pharmacy` apart. The exported CSV had exactly the 50 rows I expected.
- The matching logic ran on 3,000 generated rows in about 20 milliseconds.

## Limits

- It cannot match abbreviations. `IBS Ltd` and `International Business Systems` share no letters, so they are not matched.
- It only compares names that start with the same three letters or share the start of a word. A typo in the first letters of a one-word name (`Senith` vs `Zenith`) can be missed when nothing else in the name lines up.
- Any name part that appears in more than 400 rows is skipped as a comparison key, to keep it fast.
- One column at a time, CSV only (no Excel files), and the export keeps your file's delimiter.
- I have not tried files with tens of thousands of rows.
- Look at the groups before you export. A pair like `Sunrise Bakers` / `Sunrise Bakery` could be one company or two.

## License

MIT. See `LICENSE`.
