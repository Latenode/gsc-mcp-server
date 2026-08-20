# Tool reference

All nine tools are read-only. Parameters marked *assistant* are supplied by the AI from your question; parameters marked *fixed* are locked in the scenario and cannot be changed by the model.

---

## list_properties

Lists every property the connected account can access, with permission level.

| Parameter | Source | Notes |
|---|---|---|
| `siteUrl` | assistant, optional | Returns details for one property instead of the full list |

---

## search_analytics_query

The general-purpose report. Eleven parameters are open to the assistant, which makes it the most flexible tool and the one most exposed to model error. If a result looks wrong, ask the assistant which parameters it used.

| Parameter | Source |
|---|---|
| `siteUrl`, `startDate`, `endDate` | assistant |
| `dimensions`, `dataState`, `rowLimit`, `startRow` | assistant |
| `filterGroupType`, `filterDimension`, `filterOperator`, `filterExpression` | assistant |
| `type` = `web`, `aggregationType` = `auto` | fixed |

---

## compare_periods

Five steps: date resolution, two Search Console calls, comparison, response.

Date handling - if `currentEnd` is missing it becomes today minus three days; if `currentStart` is missing it becomes 28 days before `currentEnd`; if the previous period is missing it becomes an equally long range ending the day before `currentStart`.

Both calls are locked to `dataState = final`. Mixing final and partial data would manufacture a decline.

Comparison - rows are matched by query key. Each gets a status of `up`, `down`, `flat`, `new` or `lost`. Rows with fewer than ten impressions in both periods are dropped.

Response - `periods` (ranges and whether they are equal length), `summary` (totals and status counts), `gainers` and `losers` (25 each), and a `note` on interpretation.

---

## get_search_by_page_query

| Parameter | Source | Notes |
|---|---|---|
| `siteUrl`, `startDate`, `endDate` | assistant | |
| `filterExpression` | assistant | The page URL |
| `filterOperator` | assistant | `equals` is case-sensitive and needs the exact URL; `contains` matches a path fragment |
| `dimensions` = `query`, `filterDimension` = `page`, `dataState` = `final`, `rowLimit` = 500 | fixed | |

The operator is left to the assistant so it can recover when a user names a page imprecisely.

---

## find_striking_distance

Selection: position between 8 and 20, at least 50 impressions. Estimates missed clicks, sorts by that estimate, returns 50.

`dimensions` is fixed to `query` - the calculation reads the first key of each row as a search term.

---

## find_low_ctr_pages

Benchmark by position: 1 = 28%, 2 = 15%, 3 = 11%, 4 = 8%, 5 = 6%, 6 = 5%, 7 = 4%, 8 = 3%, 9 = 2.8%, 10 = 2.5%, beyond = 1%.

Selection: at least 100 impressions, actual CTR below benchmark. Sorted by missed clicks, returns 50.

`dimensions` is fixed to `page`.

Two caveats travel with this tool. Average position at page level is averaged across every query the page ranks for. And a weak CTR is often caused by an AI Overview above the result rather than by a weak title.

---

## keyword_cannibalization

Two stages, because query-and-page together exceeds the row limit on a large site.

1. Fetch the top 50 queries by impressions.
2. For each query, fetch competing pages with a filter on that query, up to 25 pages.

Hard ceiling: 51 API calls per invocation. A page counts at 20 or more impressions and position 30 or better. A query is reported when at least two such pages compete.

The `conflict` field is true when the best-ranking page is not the page with the most clicks.

---

## inspect_url

| Parameter | Source |
|---|---|
| `siteUrl`, `inspectionUrl`, `languageCode` | assistant |

Google allows 2,000 inspections per property per day.

---

## sitemap_audit

Returns total sitemap count, total submitted URLs, a flag for whether problems exist, a list of problems, and full detail per sitemap.

A sitemap Google has never downloaded appears in the problem list - that usually means it is unreachable or was submitted with the wrong address.
