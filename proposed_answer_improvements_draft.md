# Report updates based on v2 notebook analysis

Here are the changes to make to the main doc. They come from the new cells I added to `02_q1_data_analysis_v2.ipynb`. 

New stuff in the notebook:
1. Data profiling and logical checks.
2. Correlations between pain and app willingness, plus visit frequency vs queue pain.
3. Segmentation (food court vs restaurant, POS vs non-POS, group sizes).
4. Stats checks (chi-square failing for wrong orders, small sample sizes).
5. Keyword counts for comments and the 12-chart gallery.

## Q1(a) - Add objective data cleaning evidence

The current draft just says we checked it. Let's add this paragraph to prove it programmatically:

> We ran structured logical checks to validate data quality. All `pain_*` ratings and `willingness_to_use_app` values were within the 1–5 range, with no invalid roles or duplicate `respondent_id`s. Missingness matched expectations: 23 blank `group_size_typical` entries (22 vendors + 1 skipped diner), and vendor-specific fields like `vendor_uses_pos` were correctly empty for all 50 diners. We also converted 9 blank-equivalent comments ("N/A", "nil") to missing values, leaving 63 substantive comments.

## Q1(b) Problem 1 - Fix the POS assumption

We currently blame the sales records pain on not having a POS. The new data shows pain is high even for vendors who *do* have a POS (4.57 vs 4.47). It's an analytics problem, not a hardware one. Replace the second half of that section with:

> Notably, this pain exists even among the 31.8% of vendors who already use a POS, who rate `pain_sales_records` at 4.57 (almost identical to the 4.47 for non-POS users). The issue isn't missing hardware, but missing aggregation. Vendors can't see daily trends or best-sellers from the records they keep. This makes a lightweight sales dashboard a real solution, not just a hardware upgrade.

## Q1(b) Problem 2 - Add a caveat to the wrong-order stats

The 100% wrong order stat is technically true but the sample size is tiny (waiter n=9). The chi-square test actually fails because expected counts are below 5. Add this sentence to the end of that paragraph:

> These error rates are directional. The waiter-ordering subsample is only n=9, and a chi-square test cannot be performed because expected cell counts fall below 5. We should treat these as directional signals and validate the exact magnitudes in a live pilot.

## Q1(b) Opportunity 1 - Back up the "no resistance" claim

We need more than just mean scores to prove people actually want the app. Add these points:

> Queue pain correlates with app willingness at r=0.51, and wait uncertainty at r=0.54. Willingness is highest where pain is worst: food-court diners (n=37) average 4.56 willingness with 4.62 queue pain, compared to restaurant diners (n=13) at 3.77 willingness and 3.00 pain.

*Minor fix:* Change the vendor age stat to say "vendors aged 25–54 average 4.0 or above" (the 45-54 group is exactly 4.0, not above).

## Q1(b) Opportunity 2 - Tone down "completely eliminate"

Don't say it completely eliminates wrong orders since the kiosk cell is only n=4. Update the heading and text:

> **Mobile Online Ordering Strongly Reduces the "Wrong Order" Pain**
> In our sample, diners using Mobile/QR (n=9) or self-order kiosks (n=4) reported zero wrong orders, compared to 77.8% for waiter service. Because these subsamples are small, this is a strong directional signal rather than absolute proof. However, moving order entry to the diner's screen removes mishearing at the source, directly addressing the 4.3-rated vendor pain.

## Q1(b) Opportunity 3 - Highlight the high-value segment

Let's frame this around frequent diners and two-sided demand. Add:

> The value concentrates among high-frequency diners. Those visiting 4–5 times a week report the highest queue pain (4.67/4.69) and app willingness (5.0). Demand is also two-sided: "Order and pay from phone" is the top vendor feature (40.9%, tied with the dashboard), so reducing diner queues also relieves vendor peak-hour stress.

## Q1(c) - Pilot assumptions and targeting

For KQ #1 and #3, add this to justify the pilot location:

> Stated willingness strongly correlates with the pain the app relieves (r=0.51 for queue pain), which supports the assumption that willingness will convert to behavior. We should run the pilot at a food court first, as food-court diners report the highest pain (4.62) and willingness (4.56), and 89.2% already avoid peak hours, giving us a clear baseline for testing.

## Q3(b) Priority 7 - Split-bill workaround

Add this to justify dropping the split-bill feature for V1:

> App willingness drops as party size increases (solo 5.0 down to groups of four at 3.8). Since diners in larger groups can easily place individual orders or use PayNow to transfer funds, the workaround is behaviorally realistic and saves us from building a complex split-bill engine for V1.

## General - Point to the appendix

At the end of Q1(b), just add a line saying the full 12-chart gallery is in the `02_q1_data_analysis_v2.ipynb` notebook, and note that qualitative quotes were selected by keyword frequency (e.g., 'queue' 15 times, 'peak' 6 times) rather than just picking anecdotes.

## Q4 - Spec document fixes

Here are the final suggested improvements to add to the markdown file to fully close the gaps identified in the Q4 audit:

**Q4(a) - Explicit Data Relationships**
Update the `Key Data` section to define explicit entity relationships and multiplicities rather than just listing entities. Specify that one Diner creates many Orders, one Order contains many OrderItems, each OrderItem references one MenuItem, and each Order has one associated Payment. 

**Q4(b) - NFR Metric Justification**
Justify the specific numerical targets in the Non-Functional Requirements using Q1 survey evidence. Link the <3 second payment response time directly to the high queue pain score (4.20). For the 90% first-attempt completion rate for users aged 55+, justify it by referencing the survey's finding that older demographics have high app willingness but require accessible design, noting the exact percentage is an engineering target derived from this behavioral need.

**Q4(d) - Version Control and Deliverables**
Implement iterative version control for the specification document. Create multiple commits of `q4-spec.md` in the Git repository. Include a change log in each subsequent commit explaining what was modified and the rationale. Prepare the ordered Git commit URLs for the Canvas submission.

**Q4(a) - Traceability for Edge Cases**
Ensure the newly added failure responses (removing out-of-stock items from carts, auto-refunding) and state transitions (`Pending -> Cancelled`, `Ready <-> Not Ready`) explicitly reference the corresponding Q2 acceptance criteria to close the traceability gap between the user stories and the technical specification.