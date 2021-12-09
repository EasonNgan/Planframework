Raw_floorplan_unhandled and 10.1.1.237  insert into temp_unitplanpattern

Unit explosion

```sql
  --using unitplan image

  select distinct c_estate as [Estate Name], c_phase as [Phase Name], c_property as [Building Name], [floor desc] as [Floor Desc], b.cx_axis as Flat
  , [floor from] as [Floor From], [floor to] as [Floor To], d.cblgcode , floorplan_url as [Url], null as Final_Url 
  from raw_floorplan_unhandled a
  inner join three..cenunit b on a.cblgcode = b.cblgcode
  inner join unit_floorplan_with_centaunit c on b.centaunit = c.centaunit
  and case when y_axis not like '%G%' and y_axis not like '%M%' then cast(replace(trim(c.[floor]),'/F','') as int) end >= a.[floor from] 
  and case when y_axis not like '%G%' and y_axis not like '%M%' then cast(replace(trim(c.[floor]),'/F','') as int) end <= a.[floor to]
  inner join three..cenbldg d on a.cblgcode = d.cblgcode
  where a.insertdate > '2021-10-01'
  order by c_estate, c_phase, c_property, b.x_axis
  
  
--using floorplan image

  select distinct c_estate as [Estate Name], c_phase as [Phase Name], c_property as [Building Name], [floor desc] as [Floor Desc], b.cx_axis as Flat
  , [floor from] as [Floor From], [floor to] as [Floor To], d.cblgcode , final_url as [Url], null as Final_Url 
  from raw_floorplan_unhandled a
  inner join three..cenunit b on a.cblgcode = b.cblgcode
  inner join unit_floorplan_with_centaunit c on b.centaunit = c.centaunit
  and case when y_axis not like '%G%' and y_axis not like '%M%' then cast(replace(trim(c.[floor]),'/F','') as int) end >= a.[floor from] 
  and case when y_axis not like '%G%' and y_axis not like '%M%' then cast(replace(trim(c.[floor]),'/F','') as int) end <= a.[floor to]
  inner join three..cenbldg d on a.cblgcode = d.cblgcode
  where a.insertdate > '2021-10-01'
  order by c_estate, c_phase, c_property, b.x_axis
```

1. insert into temp_unitplanpattern
2. sql generate pattern prototype
3. excel delete duplicate except **url** (if include)
4. floordesc and flat checking
5. put it into 3 places (planpattern log, midland floor, tableau)

```sql
with t1 as(
  select estate_name, '' as test, count(1) as test1 from [PlanFramework].[dbo].[temp_floorplanpattern_Eason]
  where estate_name != ''
  group by estate_name
  union
  select building_name, '', count(1) from [PlanFramework].[dbo].[temp_floorplanpattern_Eason]
  where estate_name = ''
  group by building_name
  )
select * from [dbo].[estatelist_centascore] where estatename_c collate database_default not in (select distinct estate_name from t1)
select * from dbo.estatelist_centascore
```

