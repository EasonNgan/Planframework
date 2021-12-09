# Update Flow

#### Floor

1. update the combine floor (47  > 47-48 where statement) [Ref_code](#update floor)
2. delete old floor (48) [Ref_code](#delete floorplan data)
3. delete related table data (FloorplanpatternCenbldg, FloorPlanPattern) [Ref_code](#delete related table)

#### Unit

1. Insert new imgID for unit [Ref_code](#insert new imgID for unit)
2. update unit imgid [Ref_code](#update unit imgid)

# SQL code example

```sql
  --==================================================UP=============================================================================--
update [PlanFramework].[dbo].[UnitPlanPattern]
  set [FloorDesc_c] = N'35樓', [FloorDesc_s] = N'35楼', [FloorDesc_e] = N'35/F', startfloor = 35, endfloor = 35
  --select * from [PlanFramework].[dbo].[unitPlanPattern]
  where estatename_c = N'喜雅'  and flat_c in (N'A室', N'B室', N'C室') and floordesc_e in (N'35/F-36/F')
  --where patternid in()
  order by patternid, phasename_c, buildingname_c, flat_e, floordesc_c

  insert into unitplanpatternplanimage values (

  select * from UnitplanpatternPlanimage
  where imgid in ()
  order by imgid

  update unitplanpatternplanimage
  set imgid = 116213
  --select * from Unitplanpatternplanimage
  where patternid in(82620)

  select * from planimage order by imgid desc
  insert into [PlanFramework].[dbo].[PlanImage] (PlanType, CreateDate, flagdeleted)
  values ('UP', getdate(), 0)
  GO 

  --==================================================FP=============================================================================--
update [PlanFramework].[dbo].[floorPlanPattern]
  set [FloorDesc_c] = N'1樓至3樓', [FloorDesc_s] = N'1?至3?', [FloorDesc_e] = N'1/F-3/F', startfloor = 1, endfloor = 3
  --select * from [PlanFramework].[dbo].[floorPlanPattern]
  where estatename_c = N'翠竹花園' and buildingname_c in (N'5座', N'7座') and floordesc_e in (N'1/F-2/F') --and flat_c in (N'C室', N'M室') and floordesc_e in (N'1/F-27/F')
  --where patternid in()
  order by patternid, phasename_c, buildingname_c, floordesc_c

  insert into floorplanpatternplanimage values (

  select * from floorplanpatternplanimage
  where imgid in ()
  order by imgid

  update floorplanpatternplanimage
  set imgid = 79449
  --select * from floorplanpatternplanimage
  where patternid in (10372)

  select * from planimage order by imgid desc
  insert into [PlanFramework].[dbo].[PlanImage] (PlanType, CreateDate, flagdeleted)
  values ('FP', getdate(), 0)
  GO 7
```



#### Delete 7 tables in same estate name

```sql
   --==================================================DELETE_UP=============================================================================--

select * 
--delete b
  FROM [PlanFramework].[dbo].[UnitPlanPattern] a
  inner join [dbo].[UnitPlanPatternPlanimage] b on a.PatternID = b.PatternID
  --inner join [dbo].[UnitPlanPatternCenunit] b on a.PatternID = b.PatternID
  inner join [dbo].[Planimage] c on b.ImgID = c.ImgID 
  where a.patternid in ()

   --==================================================DELETE_FP=============================================================================--
 
select *
--delete b
  FROM [PlanFramework].[dbo].[FloorPlanPattern] a
  inner join [dbo].[FloorPlanPatternPlanimage] b on a.PatternID = b.PatternID
  --inner join [dbo].[FloorPlanPatternCenbldg] b on a.PatternID = b.PatternID
  inner join [dbo].[Planimage] c on b.ImgID = c.ImgID
  where a.patternid in ()
```

Step: 

1. delete c
2. comment **inner join [dbo].[Planimage] c on b.ImgID = c.ImgID** 
3. delete b (FloorPlanPatternPlanimage)
4. change **FloorPlanPatternPlanimage** to **FloorPlanPatternCenbldg**
5. delete b (FloorPlanPatternCenunit)
6. delete a

**Unit step is same step but step 4 change cenunit**



## Remap unitplanpatterncenunit

```sql
select b.* 
--delete b
from UnitPlanPattern a inner join UnitplanpatternCenunit b on a.PatternID = b.PatternID where estatename_c = N'家壹' or buildingname_c = N'家壹'

-- Map cenunit which y_axis only contain number
;with t1 as (select b.c_estate, b.c_phase, b.c_property, b.cblgcode,
  a.cuntcode,a.x_axis,a.y_axis from three..cenunit a inner join three..cenbldg b on a.cblgcode = b.cblgcode where c_estate in (N'家壹') or c_property in (N'家壹'))
  --insert into UnitplanpatternCenunit(patternid,cuntcode)
  select distinct patternid,cuntcode from UnitPlanPattern a inner join t1 on a.EstateName_c = t1.c_estate and isnull(a.PhaseName_c,'') = isnull(t1.c_phase,'') 
  and a.BuildingName_c = t1.c_property
  and isnumeric(replace(trim(t1.y_axis),'/F','')) = 1
  and case when y_axis not like '%G%' then cast(replace(trim(t1.y_axis),'/F','') as int) end >= a.startfloor 
  and case when y_axis not like '%G%' then cast(replace(trim(t1.y_axis),'/F','') as int) end <= a.endFloor
  and a.Flat_e = t1.x_axis
  where estatename_c = N'家壹' or buildingname_c = N'家壹'

-- Map cenunit which have G in y_axis
;with t1 as (select b.c_estate, b.c_phase, b.c_property, b.cblgcode,
  a.cuntcode,a.x_axis,a.y_axis from three..cenunit a inner join three..cenbldg b on a.cblgcode = b.cblgcode 
  where (c_estate in (N'家壹') or c_property in (N'家壹')) and a.y_axis like '%G%')
  --insert into UnitplanpatternCenunit(patternid,cuntcode)
  select distinct patternid,cuntcode from UnitPlanPattern a inner join t1 on a.EstateName_c = t1.c_estate and isnull(a.PhaseName_c,'') = isnull(t1.c_phase,'') 
  and a.BuildingName_c = t1.c_property
  and substring(a.floordesc_e, 1, 3) = t1.y_axis
  and a.Flat_e = t1.x_axis
  where estatename_c = N'家壹' or buildingname_c = N'家壹'
```

## Remap floorplanpatterncenbldg

```sql
--==================================================REMAP CENBLDG=============================================================================--
select b.*
--delete b
from FloorPlanPattern a inner join FloorplanpatternCenbldg b on a.PatternID = b.PatternID where estatename_c = N'家壹' or buildingname_c = N'家壹'

;with t1 as (select b.c_estate, b.c_phase, b.c_property, b.cblgcode
 from three..cenbldg b where c_estate in (N'家壹') or c_property in (N'家壹'))
  --insert into floorplanpatterncenbldg(patternid,cblgcode)
  select distinct patternid,cblgcode from floorPlanPattern	a 
  inner join t1 on a.EstateName_c = t1.c_estate 
  and isnull(a.PhaseName_c,'') = isnull(t1.c_phase,'') 
  and a.BuildingName_c = t1.c_property
  where estatename_c = N'家壹' or buildingname_c = N'家壹'
```
