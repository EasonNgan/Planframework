## restore record set identity ON

```sql
/****** Script for SelectTopNRows command from SSMS  ******/
set identity_insert [dbo].[FloorPlanPattern] ON
set identity_insert [dbo].[FloorPlanPattern] OFF

insert into floorplanpattern([PatternID]
      ,[EstateName_c]
      ,[PhaseName_c]
      ,[BuildingName_c]
      ,[FloorDesc_c]
      ,[EstateName_s]
      ,[PhaseName_s]
      ,[BuildingName_s]
      ,[FloorDesc_s]
      ,[EstateName_e]
      ,[PhaseName_e]
      ,[BuildingName_e]
      ,[FloorDesc_e]
      ,[startFloor]
      ,[endFloor]
      ,[ord]
      ,[createdate]
      ,[flagdeleted])
  select  1373,
      [EstateName_c]
      ,[PhaseName_c]
      ,N'O座'
      ,[FloorDesc_c]
      ,[EstateName_s]
      ,[PhaseName_s]
      ,N'O座'
      ,[FloorDesc_s]
      ,[EstateName_e]
      ,[PhaseName_e]
      ,'BLOCK O'
      ,[FloorDesc_e]
      ,[startFloor]
      ,[endFloor]
      ,[ord]
      ,getdate()
      ,[flagdeleted]FROM [PlanFramework].[dbo].[FloorPlanPattern]  where patternid = 1355
```

