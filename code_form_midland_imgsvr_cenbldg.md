## FROM midland floorplan:

```sql
  --==================================================floorplan=============================================================================--
--insert into [dbo].[temp_FloorplanPattern_eason]
select distinct c_estate
      ,c_phase
      ,c_property
      ,[Floor_Desc]
      ,case when isnumeric(Floor_from) = 0 then null else cast([Floor_From] as int) end
      ,case when isnumeric(Floor_to) = 0 then null else cast([Floor_to] as int) end
      ,[cblgcode]
      ,[Url]
      ,'' as [Final_Url] from [10.1.1.237].[midland_floorplan].[dbo].[building_floorplan] a 
inner join three..cenbldg b on estate_name = replace(replace(replace(c_estate, N'峰', N'峯'), N'園', N'苑'), N'臺', N'台')  collate database_default
and isnull(phase_name,'') = isnull(c_phase,'') collate database_default
and building_name = c_property collate database_default
--and trim(left(building_name,3)) = trim(replace(replace(right(c_property,4),')',''),'(','')) collate database_default
where estate_name collate database_default in (
select estatename_c from estatelist_centascore --change table
)
and create_date >= '2021-02-01'

  --==================================================unit_floorplan=============================================================================--

;with t1 as (select * from [10.1.1.237].[midland_floorplan].[dbo].[unit_floorplan]
where estate_name collate database_default in (
select estatename_c from estatelist_centascore --change table
))
--insert into unit_floorplan_with_centaunit_eason
select distinct c.[centabldg]
      ,[centaunit]
      ,[estate_id]
      ,[c_estate]
      ,[phase_id]
      ,[c_phase]
      ,[building_id]
      ,[c_property]
      ,[unit_id]
      ,[floor]
      ,[flat]
      ,[floorplan_url] from t1 a inner join three..cenbldg b on estate_name = replace(replace(replace(c_estate, N'峰', N'峯'), N'園', N'苑'), N'臺', N'台') collate database_default
and isnull(phase_name,'') = isnull(c_phase,'') collate database_default
and building_name = c_property collate database_default
--and trim(left(building_name,3)) = trim(replace(replace(right(c_property,4),')',''),'(','')) collate database_default
inner join three..cenunit c on c.cblgcode = b.cblgcode 
and replace(c.x_axis,'FLAT ','') = flat collate database_default
and floor = replace(c.y_axis,'/F','')  collate database_default
where datetime >= '2021-02-01'
```

## Check estatename or buildingname

```sql
  --==================================================237 midland=============================================================================--

select * from [midland_floorplan].[dbo].[building_floorplan]
where estate_name like N'%旭禾苑%' or building_name like '%旭禾苑%'

select * from [midland_floorplan].[dbo].[unit_floorplan]
where estate_name like N'%旭禾%' or building_name like '%旭禾%'

  --==================================================38 cenbldg and cenunit=============================================================================--

select * from three..cenbldg where centabldg in (N'6856704001')
select * from three..cenbldg where c_estate like (N'%12345%')
order by c_phase, c_property

select * from three..cenunit where cblgcode in ('OCJRUROARS')
select * from three..cenunit where centaunit in ('146724830429')
```

## Find estatename in imgsvr > imgmaster

```sql
select namec1, c_estate,c_phase,c_property,trim(replace(replace(replace(namec1,c_estate,''), '2期 GRANDE', ''), c_property, '')) as floordesc, 0 as floor_from , 0 as floor_to,cblgcode,
  'https://centadata.com/ViewImage.aspx?url=https://hkfloorplan.centanet.com/img/img.aspx!dir='+ trim(imgdir) + '$name='+ trim(imgalias) +'$org=Y' as url,
  null as final_url
  from [imgsvr].[dbo].[imgmaster] a inner join imgsvr..imglink b on a.imgid = b.imgid inner join three..cenbldg c on c.cblgcode = b.pcodee
  where namec1 like '%MONACO%' and imgtype = 'FP' and subsidiary = 'cpal' and namec1 not like N'%平面%' and c_phase != N'1期'
  --and namec1 like N'%地下, 地下高層%'
  order by c_phase, c_property
```



## FROM IMGSVR  FINAL CODE: (No unit)

```sql
-- insert into [PlanFramework].[dbo].[temp_floorplanpattern_Eason]
select c_estate as estate_name
	,c_phase as phase_name
	,c_property as building_name
	
	,case when replace(replace(replace(namec1,c_estate,''), c_phase, ''), c_property, '') like '%,%' then 
		  substring(trim(replace(replace(replace(replace(namec1,c_estate,''), c_phase, ''), c_property, ''), '-', '樓至')), 1, charindex('至', trim(replace(replace(replace(replace(namec1,c_estate,''), c_phase, ''), c_property, ''), '-', '樓至')))-1)
		  + substring(trim(replace(replace(replace(replace(namec1,c_estate,''), c_phase, ''), c_property, ''), '-', '樓至')),charindex('至', trim(replace(replace(replace(replace(namec1,c_estate,''), c_phase, ''), c_property, ''), '-', '樓至')), 17), 4)
		  when replace(replace(replace(namec1,c_estate,''), c_phase, ''), c_property, '') like '%-%' then trim(replace(replace(replace(replace(namec1,c_estate,''), c_phase, ''), c_property, ''), '-', '樓至'))
		  else trim(replace(replace(replace(namec1,c_estate,''), c_phase, ''), c_property, '')) end as floor_desc

	-- ,trim(replace(replace(replace(namec1,c_estate,''), c_phase, ''), c_property, '')) as floordesc
	 ,case when replace(replace(replace(namec1,c_estate,''), c_phase, ''), c_property, '') like '%,%' or replace(replace(replace(namec1,c_estate,''), c_phase, ''), c_property, '') like '%-%' then
	 substring (trim(replace(replace(replace(namec1,c_estate,''), c_phase, ''), c_property, '')), 1, charindex('至', trim(replace(replace(replace(replace(namec1,c_estate,''), c_phase, ''), c_property, ''), '-', '樓至')))-2)
			when replace(replace(replace(namec1,c_estate,''), c_phase, ''), c_property, '') like '%樓%' then
			substring (trim(replace(replace(replace(namec1,c_estate,''), c_phase, ''), c_property, '')), 1, charindex('樓', trim(replace(replace(replace(replace(namec1,c_estate,''), c_phase, ''), c_property, ''), '-', '樓至')))-1)
	 else null end as floorfrom

	,case when replace(replace(replace(namec1,c_estate,''), c_phase, ''), c_property, '') like '%,%' then
		substring(trim(replace(replace(replace(replace(namec1,c_estate,''), c_phase, ''), c_property, ''), '-', '樓至')), charindex('至', trim(replace(replace(replace(replace(namec1,c_estate,''), c_phase, ''), c_property, ''), '-', '樓至')), 17)+1
		,len(trim(replace(replace(replace(replace(namec1,c_estate,''), c_phase, ''), c_property, ''), '-', '樓至')))-(charindex('至', trim(replace(replace(replace(replace(namec1,c_estate,''), c_phase, ''), c_property, ''), '-', '樓至')), 17)+1))

		when replace(replace(replace(namec1,c_estate,''), c_phase, ''), c_property, '') like '%-%' then
		substring(trim(replace(replace(replace(namec1,c_estate,''), c_phase, ''), c_property, '')), charindex('-', trim(replace(replace(replace(namec1,c_estate,''), c_phase, ''), c_property, '')))+1
		,len(trim(replace(replace(replace(namec1,c_estate,''), c_phase, ''), c_property, '')))-(charindex('-', trim(replace(replace(replace(namec1,c_estate,''), c_phase, ''), c_property, '')))+1))

		when replace(replace(replace(namec1,c_estate,''), c_phase, ''), c_property, '') like '%樓%' then
		substring(trim(replace(replace(replace(namec1,c_estate,''), c_phase, ''), c_property, '')), 1,len(trim(replace(replace(replace(namec1,c_estate,''), c_phase, ''), c_property, '')))-1)
		else null end as floorto  
	,cblgcode
	,'https://centadata.com/ViewImage.aspx?url=https://hkfloorplan.centanet.com/img/img.aspx!dir='+ trim(imgdir) + '$name='+ trim(imgalias) +'$org=Y' as url,
    null as final_url
  from [imgsvr].[dbo].[imgmaster] a 
  inner join imgsvr..imglink b on a.imgid = b.imgid
  inner join three..cenbldg c on c.cblgcode = b.pcodee
  where namec1 like '%MONACO%' and imgtype = 'FP' and subsidiary = 'cpal' and namec1 not like N'%平面%'
  --and namec1 not like N'%地下, 地下高層%' and namec1 not like N'%地下高層, 1樓%' and namec1 not like N'%地庫, 地下-1樓 - 天台%'
```



## FROM  CENBLDG: (No unit)

```sql
 --insert into [PlanFramework].[dbo].[temp_floorplanpattern_Eason]
 select c_estate as estate_name
 ,c_phase as phase_name
 ,c_property as building_name
 ,cast(cast(min(cast(replace(b.y_axis,'/F','') as int))as nvarchar) + '樓至' + cast(max(cast(replace(b.y_axis,'/F','') as int))as nvarchar) + '樓' as nvarchar) as floor_desc
 ,min(cast(replace(b.y_axis,'/F','') as int)) as floor_from
 ,max(cast(replace(b.y_axis,'/F','') as int)) as floor_to
 /*
  ,'NO. 1' as floor_desc
 ,null as floor_from
 ,null as floor_to
 */
 ,a.cblgcode
 ,null as url
 ,null as final_url
 from three..cenbldg a
 inner join three..cenunit b on a.cblgcode = b.cblgcode 
 where c_estate like N'%靜修里11號%' -- and c_property not in (N'1座', N'2座')
 group by c_estate, c_phase, c_property, a.cblgcode
```



## Note for specific case of phasename or buildingname:

```sql
--trim(substring(replace(replace(c_property, '(', ''), ')', ''), 4, 3))

--right(replace(replace(replace(building_name, estate_name, ''), '(', ''), ')', ''), 2) = left(replace(replace(replace(c_property, c_estate, ''), '(', ''), ')', ''), 2)

--and left(replace(replace(replace(replace(replace(replace(replace(replace(building_name, estate_name, ''), '第', ''), '(', ''), ')', ''), '1', '一'), '2', '二'), '3', '三'), '4', '四'), 2) =
--right(replace(replace(replace(replace(replace(replace(replace(c_property, c_estate, ''), '(', ''), ')', ''), '1', '一'), '2', '二'), '3', '三'), '4', '四'), 2) collate database_default

-- , '1 ', '1'), '2 ', '2'), '3 ', '3'), '4 ', '4'), '5 ', '5'), '6 ', '6'), '7 ', '7')

--and trim(left(building_name,3)) = trim(replace(replace(right(c_property,4),')',''),'(','')) collate database_default

--keep
--and substring(replace(replace(replace(replace(replace(replace(replace(replace(building_name, estate_name, ''), N'第', ''), '(', ''), ')', ''), '1', '一'), '2', '二'), '3', '三'), '4', '四'), 10, 1) =
--substring(replace(replace(replace(replace(replace(replace(replace(c_property, c_estate, ''), ' (', ''), ')', ''), '1', '一'), '2', '二'), '3', '三'), '4', '四'), 4, 1) collate database_default
```

