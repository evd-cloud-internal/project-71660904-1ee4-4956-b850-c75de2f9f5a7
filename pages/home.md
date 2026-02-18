---
name: Home
assetId: ff9ca568-1e60-4703-9ed1-3bb5dd1d220a
type: page
---

# CLE Video - Avg Concurrent Viewers

```sql cle_video
SELECT
    toHour(timestamp) AS hour_of_day,
    CASE toDayOfWeek(timestamp)
        WHEN 1 THEN 'Monday'
        WHEN 2 THEN 'Tuesday'
        WHEN 3 THEN 'Wednesday'
        WHEN 4 THEN 'Thursday'
        WHEN 5 THEN 'Friday'
        WHEN 6 THEN 'Saturday'
        WHEN 7 THEN 'Sunday'
    END AS day_of_week,
    concat(
        CASE toMonth(timestamp)
            WHEN 1 THEN 'January'
            WHEN 2 THEN 'February'
            WHEN 3 THEN 'March'
            WHEN 4 THEN 'April'
            WHEN 5 THEN 'May'
            WHEN 6 THEN 'June'
            WHEN 7 THEN 'July'
            WHEN 8 THEN 'August'
            WHEN 9 THEN 'September'
            WHEN 10 THEN 'October'
            WHEN 11 THEN 'November'
            WHEN 12 THEN 'December'
        END, ' ', toString(toYear(timestamp))
    ) AS month_year,
    toStartOfMonth(timestamp) AS month_sort,
    avg_concurrent_viewers
FROM cle_video_updated
ORDER BY hour_of_day
```

```sql demographics
SELECT
    Target as audience_comp,
    toFloat64OrNull("Audience Comp.") / 100 AS audience_comp_pct
FROM espn_cleveland_demographics
WHERE Target IS NOT NULL
  AND trim(Target) != ''
  AND "Audience Comp." IS NOT NULL
  AND trim("Audience Comp.") != ''
ORDER BY Target
```

{% dropdown
    id="demo_filter"
    data="demographics"
    value_column="audience_comp"
    title="Audience Composition"
    select_first=true
/%}

{% dropdown
    id="hour_filter"
    data="cle_video"
    value_column="hour_of_day"
    title="Hour of Day"
    order="hour_of_day"
    multiple=true
/%}

{% dropdown
    id="day_filter"
    data="cle_video"
    value_column="day_of_week"
    title="Day of Week"
    multiple=true
/%}

{% dropdown
    id="month_filter"
    data="cle_video"
    value_column="month_year"
    title="Month"
    order="month_sort"
    multiple=true
/%}

```sql cle_video_demo
SELECT
    v.hour_of_day,
    v.day_of_week,
    v.month_year,
    v.month_sort,
    v.avg_concurrent_viewers,
    v.avg_concurrent_viewers * d.audience_comp_pct AS demo_avg_concurrent_viewers
FROM {{cle_video}} v
CROSS JOIN (
    SELECT toFloat64OrNull("Audience Comp.") / 100 AS audience_comp_pct
    FROM espn_cleveland_demographics
    WHERE Target = {{demo_filter}}
    LIMIT 1
) d
ORDER BY v.hour_of_day
```

{% bar_chart
    data="cle_video_demo"
    x="day_of_week"
    y="avg(demo_avg_concurrent_viewers)"
    x_sort=["Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"]
    filters=["hour_filter", "day_filter", "month_filter"]
    title="Avg Concurrent Viewers by Day of Week ({{demo_filter.literal}})"
    y_fmt="#,##0"
/%}
