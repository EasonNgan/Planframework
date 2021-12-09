Raw_floorplan_checking

```sql
select * from raw_floorplan a
inner join raw_floorplan_inserted b on a.[estate name] = b.[estate name] 
and a.[phase name] = b.[phase name]
and a.[building name] = b.[building name]
and a.[floor from] = b.[floor from]
and a.[Floor To] = b.[Floor To]
and a.cblgcode = b.cblgcode 
where a.cblgcode in (
select distinct cblgcode from raw_floorplan_inserted) and a.cblgcode != ''
```

```sql
select * from raw_floorplan where cblgcode in(
select distinct cblgcode from raw_floorplan_inserted
where cblgcode != '')
```

raw_floorplan 6 tables and temp 2 tables

```sql
select * from [dbo].[Raw_Floorplan] --cblgcode
select * from [dbo].[Raw_Floorplan_INSERTED]
select * from [dbo].[Raw_Floorplan_UNHANDLED]
select * from [dbo].[Raw_Unitplan]
select * from [dbo].[Raw_Unitplan_INSERTED]
select * from [dbo].[Raw_Unitplan_redundant]
select * from [dbo].[temp_FloorplanPattern]
select * from [dbo].[unit_floorplan_with_centaunit] --centabldg

update a set [estate name] = c_estate, [phase name] = c_phase, [building name] = c_property
--select *
from [Raw_Floorplan] a 
inner join three..cenbldg b on a.cblgcode = b.cblgcode 

update a set [estate_name] = c_estate, [phase_name] = c_phase, [building_name] = c_property
--select *
from [unit_floorplan_with_centaunit] a 
inner join three..cenbldg b on a.centabldg = b.centabldg 
```

