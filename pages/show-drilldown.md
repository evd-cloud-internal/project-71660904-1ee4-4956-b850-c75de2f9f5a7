---
name: Show Drilldown
assetId: 5347ff54-6e7b-42ff-ad17-a52800a787b8
type: page
---

# Average Concurrent Viewers by Title

{% dropdown
    id="title_filter"
    data="cle_video_updated"
    value_column="title"
    title="Select a Title"
    select_first=true
/%}

{% line_chart
    data="cle_video_updated"
    x="timestamp"
    y="avg(avg_concurrent_viewers)"
    filters=["title_filter"]
    title="Avg Concurrent Viewers Over Time"
    y_fmt="#,##0"
/%}