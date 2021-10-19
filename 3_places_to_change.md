## 1.Temp_floorplanpattern_eason and unit_floorplan_with_centaunit_eason

1. planpattern log
2. midland floor
3. tableau

### planpattern log

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
select * from [dbo].[estatelist_centascore] a where estatename_c collate database_default not in (select distinct estate_name from t1)
select * from dbo.estatelist_centascore
```

### midland floor

```sql
SELECT [estate_name]
      ,[phase_name]
      ,[building_name]
      ,[floor_desc]
      ,[floor_from]
      ,[floor_to]
      ,[cblgcode]
	  ,'' as M
	  ,'' as Remark
      ,[final_url]
      ,[url]
  FROM [PlanFramework].[dbo].[temp_floorplanpattern_Eason]
```

### tableau (remote desktop  10.29.205.32)

1. copy temp_floorpattern_eason to tableau
2. publish tableau

### insert into two tables

```sql
  insert into [PlanFramework].[dbo].[temp_floorplanpattern]
  select * from [PlanFramework].[dbo].[temp_floorplanpattern_Eason]

  insert into [PlanFramework].[dbo].[unit_floorplan_with_centaunit]
  select * from [PlanFramework].[dbo].[unit_floorplan_with_centaunit_Eason]
  
  truncate table temp_floorplanpattern_Eason
  truncate table unit_floorplan_with_centaunit_Eason
```

## 2. Estatelist and truncate two tables

