# Simczech election predictions

Predictions of elections made by Simczech, sealed before the polls close and published when they close.

## How it works

1. **Sealed.** Before the polls close we fix the prediction and publish only its fingerprint:
   `sealed/<election>/<version>/SHA256SUMS` lists the SHA-256 hash of every file of the prediction.
   The list carries independent timestamps: RFC 3161 tokens from FreeTSA and DigiCert, and an
   OpenTimestamps proof that is anchored in the Bitcoin blockchain within a few hours.
2. **Revealed.** When the polls close, the files themselves are published in
   `predictions/<election>/<version>/`, next to a copy of the same list.
3. **Checked.** Anyone can confirm that the published files are the sealed ones, and that the list existed
   before the polls closed:

   ```sh
   # from the repository's top folder
   E=bulgaria/2026/president/round-1; V=<version>
   cmp sealed/$E/$V/SHA256SUMS predictions/$E/$V/SHA256SUMS
   (cd predictions/$E/$V && shasum -a 256 -c SHA256SUMS)
   openssl ts -reply -in sealed/$E/$V/SHA256SUMS.freetsa.tsr -text
   ots verify sealed/$E/$V/SHA256SUMS.ots
   ```

Every prediction file contains a random number (`nonce`), so its fingerprint cannot be matched by trying out
possible results.

## Elections

| Election | Polls close | Folder | Page |
|---|---|---|---|
| Bulgaria, presidential election, first round, 25 Oct 2026 | 20:00 local time (19:00 CET) | `bulgaria/2026/president/round-1` | [simczech.cz/world/bulgaria/2026/president](https://simczech.cz/world/bulgaria/2026/president) |
| Serbia, National Assembly election, 25 Oct 2026 | 20:00 CET | `serbia/2026/parliament` | [simczech.cz/world/serbia/2026/parliament](https://simczech.cz/world/serbia/2026/parliament) |
| Brazil, presidential election, runoff, 25 Oct 2026 | 17:00 Brasília time (21:00 CET) | `brazil/2026/president/round-2` | [simczech.cz/world/brazil/2026/president](https://simczech.cz/world/brazil/2026/president) |

## Methods

Each prediction is made by three methods. Every one of them uses only material published before the polls
opened: official results of past elections, registered voters and published polls.

- **Simczech Original** is the main prediction: the combination of the two methods below.
- **Simczech Loquat** is a statistical model built on official past results by municipality and on published polls.
- **Simczech Lucuma** is analysts' research of sources published before the polls opened: polls and how they
  erred in the past, betting markets, the campaign.

## Experimental series

Beside a prediction we sometimes seal an experimental series. It follows the same rules (only material published
before the polls opened; sealed before the polls close; revealed when they close), but it is not part of the
prediction and not shown on the page: it is there to be tested against the result.

- **Simczech Rambutan** (25 October 2026, all three elections): archetype opinions. Groups of voters by their vote
  in the last comparable election and where they live, sized by the official counts, each answering as a simulated
  voter: its chance of voting today and the chance of each option. The campaign brief they answer from leaves out the
  polls and the betting markets. Version `2026-10-25_1417-rambutan`: `prediction.json` (the series' figure and two
  variants, more deliberation and finer groups), `archetypes*.csv` (every archetype and its answers), `questionnaire.md`.

## Files of a prediction

- `prediction.json`: the national figures of all three methods (vote shares with 80 % ranges, turnout,
  probabilities, and seats where they apply) and the main prediction by region.
- `municipalities.csv`: the main prediction for every municipality.
- `runs/`: each method's own estimate as it was delivered.
