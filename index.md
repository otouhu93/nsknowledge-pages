---
layout: report
title: NSknowledge - AI News Archive
---

# NSknowledge — AI News Archive

{% assign dailies = site.pages | where_exp: "p", "p.path contains 'daily/'" | sort: "date" | reverse %}
{% assign weeklies = site.pages | where_exp: "p", "p.path contains 'weekly/'" | sort: "date" | reverse %}

{% if weeklies.size > 0 %}
## 週次レポート

{% for report in weeklies %}
- [{{ report.date }}]({{ report.url | relative_url }})
{% endfor %}
{% endif %}

## 日次レポート

{% for report in dailies %}
- [{{ report.date }}]({{ report.url | relative_url }}) — {{ report.total_articles }}件
{% endfor %}
