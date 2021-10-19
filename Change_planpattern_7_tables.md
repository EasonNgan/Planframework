# Update Flow

#### Floor

1. update the combine floor (47  > 47-48 where statement) [Ref_code](#update floor)
2. delete old floor (48) [Ref_code](#delete floorplan data)
3. delete related table data (FloorplanpatternCenbldg, patternPlanimage, FloorPlanPattern) [Ref_code](#delete related table)

#### Unit

1. Insert new imgID for unit [Ref_code](#insert new imgID for unit)
2. update unit imgid [Ref_code](#update unit imgid)

# SQL code example

```sql
  --==================================================UP=============================================================================--
update [PlanFramework].[dbo].[UnitPlanPattern]
  set [FloorDesc_c] = N'1樓', [FloorDesc_s] = N'1楼', [FloorDesc_e] = N'1/F', endfloor = 1 , startfloor = 1
  --select * from [PlanFramework].[dbo].[unitPlanPattern]
  where estatename_c = N'爾巒' and buildingname_c like N'%茵娜大道 8座%' and flat_c = N'C室'
  order by buildingname_c, flat_e, floordesc_c

  update Unitplanpattern
  set [FloorDesc_c] = N'1樓', [FloorDesc_s] = N'1楼', [FloorDesc_e] = N'1/F', endfloor = 1 , startfloor = 1
  where patternid in (53234)
  
  insert into unitplanpatternplanimage values (106069, 102794)

  select * from UnitplanpatternPlanimage
  where imgid in()

  select * from unitplanpatternplanimage
  where patternid in()

  update unitplanpatternplanimage
  set imgid = 102799
  --select * from Unitplanpatternplanimage
  where patternid in(21997)

  select * from planimage order by imgid desc
  insert into [PlanFramework].[dbo].[PlanImage] (PlanType, CreateDate, flagdeleted)
  values ('UP', getdate(), 0)


  --==================================================FP=============================================================================--

update [PlanFramework].[dbo].[floorPlanPattern]
  set [FloorDesc_c] = N'UG樓至地下', [FloorDesc_s] = N'UG楼至地下', [FloorDesc_e] = N'UG/F-G/F' --, endfloor = 8 , startfloor = 7
  --select * from [PlanFramework].[dbo].[floorPlanPattern]
  where estatename_c = N'爾巒' and buildingname_c like N'%茵娜大道%' and floordesc_c = N'UG樓'
  order by buildingname_c, flat_e, floordesc_

  update floorplanpattern
  set [FloorDesc_c] = N'1樓', [FloorDesc_s] = N'1楼', [FloorDesc_e] = N'1/F', endfloor = 1 , startfloor = 1
  where patternid = 53231

  insert into floorplanpatternplanimage values (21887, 102780)
  
  select * from [PlanFramework].[dbo].[floorPlanPattern]
  where patternid in (21893)

  select * from floorplanpatternPlanimage
  where imgid = 32612

  update floorplanpatternplanimage
  set imgid = 102798
  --select * from floorplanpatternplanimage
  where patternid in()

  select * from planimage order by imgid desc
  insert into [PlanFramework].[dbo].[PlanImage] (PlanType, CreateDate, flagdeleted)
  values ('FP', getdate(), 0)
```



#### Delete 7 tables in same estate name

```sql
   --==================================================DELETE_UP=============================================================================--
 SELECT *
  FROM [PlanFramework].[dbo].[UnitPlanPattern] a
  inner join [dbo].[UnitPlanPatternPlanimage] b on a.PatternID = b.PatternID
  --inner join [dbo].[UnitPlanPatternCenunit] b on a.PatternID = b.PatternID
  inner join [dbo].[Planimage] c on b.ImgID = c.ImgID 
  where EstateName_c = N'GRAND AUSTIN' 
 
   --==================================================DELETE_FP=============================================================================--
 
 SELECT *
  FROM [PlanFramework].[dbo].[FloorPlanPattern] a
  inner join [dbo].[FloorPlanPatternPlanimage] b on a.PatternID = b.PatternID
  --inner join [dbo].[FloorPlanPatternCenbldg] b on a.PatternID = b.PatternID
  inner join [dbo].[Planimage] c on b.ImgID = c.ImgID 
  where EstateName_c = N'GRAND AUSTIN'
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
  --==================================================REMAP CENUNIT=============================================================================--
select * 
--delete b
from UnitPlanPattern a inner join UnitplanpatternCenunit b on a.PatternID = b.PatternID where EstateName_c in (N'東環')

-- Map cenunit which have G in y_axis
with t1 as (select b.c_estate, b.c_phase, b.c_property, b.cblgcode,
  a.cuntcode,a.x_axis,a.y_axis from three..cenunit a inner join three..cenbldg b on a.cblgcode = b.cblgcode where c_estate in (N'東環') and a.y_axis like '%G%')
  --insert into UnitplanpatternCenunit(patternid,cuntcode)
  select distinct patternid,cuntcode from UnitPlanPattern a inner join t1 on a.EstateName_c = t1.c_estate and isnull(a.PhaseName_c,'') = isnull(t1.c_phase,'') 
  and a.BuildingName_c = t1.c_property
  and a.floordesc_e = t1.y_axis
  and a.Flat_e = t1.x_axis
  where EstateName_c in (N'東環')

-- Map cenunit which y_axis only contain number
;with t1 as (select b.c_estate, b.c_phase, b.c_property, b.cblgcode,
  a.cuntcode,a.x_axis,a.y_axis from three..cenunit a inner join three..cenbldg b on a.cblgcode = b.cblgcode where c_estate in (N'東環'))
  --insert into UnitplanpatternCenunit(patternid,cuntcode)
  select distinct patternid,cuntcode from UnitPlanPattern a inner join t1 on a.EstateName_c = t1.c_estate and isnull(a.PhaseName_c,'') = isnull(t1.c_phase,'') 
  and a.BuildingName_c = t1.c_property
  and isnumeric(replace(trim(t1.y_axis),'/F','')) = 1
  and case when y_axis not like '%G%' then cast(replace(trim(t1.y_axis),'/F','') as int) end >= a.startfloor 
  and case when y_axis not like '%G%' then cast(replace(trim(t1.y_axis),'/F','') as int) end <= a.endFloor
  and a.Flat_e = t1.x_axis
  where EstateName_c in (N'東環')
```

## Remap floorplanpatterncenbldg

```sql
  --==================================================REMAP CENBLDG=============================================================================--
select *
--delete b
from FloorPlanPattern a inner join FloorplanpatternCenbldg b on a.PatternID = b.PatternID where EstateName_c in (N'東環')

;with t1 as (select b.c_estate, b.c_phase, b.c_property, b.cblgcode
 from three..cenbldg b where c_estate in (N'東環'))
  --insert into floorplanpatterncenbldg(patternid,cblgcode)
  select distinct patternid,cblgcode from floorPlanPattern a 
  inner join t1 on a.EstateName_c = t1.c_estate 
  and isnull(a.PhaseName_c,'') = isnull(t1.c_phase,'') 
  and a.BuildingName_c = t1.c_property
  where EstateName_c in (N'東環')
```
