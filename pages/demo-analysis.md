---
name: Demo Analysis
assetId: 4d5267a4-c055-4ff0-85aa-ee4d0a8238f2
type: page
---

# New Page

```sql top_demographics
SELECT
    Target,
    toFloat64OrNull("Audience Comp.") AS audience_comp
FROM espn_cleveland_demographics
WHERE Target IS NOT NULL
  AND trim(Target) != ''
  AND "Audience Comp." IS NOT NULL
  AND trim("Audience Comp.") != ''
ORDER BY audience_comp DESC
LIMIT 5
```

{% horizontal_bar_chart
    data="top_demographics"
    y="Target"
    x="audience_comp"
    x_fmt="#,##0.0"
    title="Top 5 Demographics by Audience Composition"
    order="audience_comp desc"
    data_labels={position="right" fmt="#,##0.0"}
/%}