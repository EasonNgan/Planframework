## Step of Imageid list change:

1. run stored proc (38.planframework.genFloorplanStat)
2. use sql to find estatename data and copy to Imgid list 
3. Phase select two column拉落填公式
4. Assignes imgID > 0 (not our work)

Imgid list :https://docs.google.com/spreadsheets/d/17kbYsAKmio_3x81Sk5uBLO2JiMBl8NRVVuMuwNy4ZYY/edit#gid=0

### Step two sql:

```sql
SELECT TOP (1000) [imgid]
      ,[region]
      ,[AplusScope]
      ,[hma_c]
      ,[EstateName_c]
	  ,''
      ,[PhaseName_c]
      ,[BuildingName_c]
      ,[FloorDesc_e]
      ,[unit_cnt]
  FROM [PlanFramework].[dbo].[FloorplanStat]
  where estatename_c = N'曉麗苑'
```

