# The paid fix: the second-tab notice, seen in a browser
## 2026-11-03 · Builder session (Studio Mac) · branch `builder/paid-fix` · part 6

Follows part 5. After part 4, another session added one line to the report
page: when a second tab's payment is cancelled because the fix was already
paid for in the first tab, the page now says so under the download. That line
had not been seen in a browser. This session checked it.

Practice database and Stripe test mode only, test card. Nothing was merged or
deployed. Vercel, DNS, Doppler and the live database were not changed.

## Result

| Check | Result |
|---|---|
| Tab A pays, the fix runs | order `charged`, $19.00 captured |
| Tab B pays afterwards and comes back | order `released` within seconds; Stripe payment canceled, $0 captured |
| What tab B shows | the fixed file and download, with the new line saying the second payment was cancelled and the buyer was charged once |
| Screen readers | the line sits inside the panel's polite live region |
| 390px wide | readable, no sideways scroll |
| Console | no errors from the page (only the dev server's reload socket) |
| Charges for the document | one |

The line's wording is a draft for the owner's review, with the rest of the
buyer wording (D-027).

## Practice database rows from this test

1 more anonymous document and 2 more fix orders: 1 `charged` with the test
card, 1 `released` with $0 captured. The ordinary retention window removes them.
