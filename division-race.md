---
title: Division Race
layout: dashboard
---

```sql season_options
select season_year
from team_info_common
group by season_year
order by season_year desc
```

```sql conference_options
select team_conference as conference
from team_info_common
where season_year = $selected_season
  and team_conference is not null
group by team_conference
order by conference
```

<Row>
  <Dropdown title="Season" name="selected_season" data="season_options" value="season_year" defaultValue="2022-23" />
  <Dropdown title="Conference" name="selected_conference" data="conference_options" value="conference" defaultValue="East" />
</Row>

```sql standings
select
  team_conference as conference,
  team_division as division,
  team_city || ' ' || team_name as team,
  team_abbreviation as abbr,
  w as wins,
  l as losses,
  round(pct * 100, 1) as win_pct,
  conf_rank,
  div_rank,
  round(pts_pg, 1) as pts_pg,
  round(opp_pts_pg, 1) as opp_pts_pg,
  round(pts_pg - opp_pts_pg, 1) as net_pts,
  round(reb_pg, 1) as reb_pg,
  round(ast_pg, 1) as ast_pg
from team_info_common
where season_year = $selected_season
  and team_conference = $selected_conference
order by conf_rank
```

```sql leader
select team, wins, losses, win_pct
from standings
where conf_rank = 1
```

```sql conference_summary
select
  round(avg(pts_pg), 1) as avg_pts,
  round(avg(opp_pts_pg), 1) as avg_opp_pts,
  round(avg(net_pts), 1) as avg_net_pts
from standings
```

<Row>
  <BigValue title="Conference Leader" data="leader" value="team" />
  <BigValue title="Leader Wins" data="leader" value="wins" />
  <BigValue title="Avg PTS/G" data="conference_summary" value="avg_pts" />
  <BigValue title="Avg OPP PTS/G" data="conference_summary" value="avg_opp_pts" />
</Row>

<Row>
  <BarChart
    title="Wins by team"
    data="standings"
    x="abbr"
    y="wins"
    splitBy="division"
    sort="wins desc"
    label
    height="340px"
  />
  <ScatterPlot
    title="Offense vs defense — bottom-right is best"
    data="standings"
    x="pts_pg"
    y="opp_pts_pg"
    splitBy="division"
    height="340px"
  />
</Row>

<Table title="Conference standings" data="standings" rows=16 sortable>
  <Column id="conf_rank" title="#" align="right" />
  <Column id="team" title="Team" />
  <Column id="division" title="Division" />
  <Column id="wins" title="W" align="right" />
  <Column id="losses" title="L" align="right" />
  <Column id="win_pct" title="Win %" align="right" />
  <Column id="pts_pg" title="PTS/G" align="right" />
  <Column id="opp_pts_pg" title="OPP/G" align="right" />
  <Column id="net_pts" title="Net" align="right" />
  <Column id="reb_pg" title="REB/G" align="right" />
  <Column id="ast_pg" title="AST/G" align="right" />
</Table>
