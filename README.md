# floorplan

there are several branches in this git for floorplan:

1. [Gen_patten_for_yannis_check](https://github.com/EasonNgan/floorplan/tree/Gen_patten_for_yannis_check)
2. [Change_floor&unit_planpattern](https://github.com/EasonNgan/floorplan/tree/Change_floor%26unit_planpattern)
3. [Raw_pattern_insert&checking](https://github.com/EasonNgan/floorplan/tree/Raw_pattern_insert&checking)

### Change_floor&unit_planpattern

- Change_planpattern_7_tables

  some sql for you to change the floorplanpatten, unitplanpattern and planimage

- Imageid_list_change_for_floorplan

  steps for changing google drive imageid list and generate imgid sql

- Restore_specific_patternid_floorplan

  example sql for restoring specific number of patternid

### Gen_patten_for_yannis_check

- code_form_midland_imgsvr_cenbldg

  reference sql to generate tempfloorplan and unitplan from **midland floorplan**, **imgsvr** or **cenbldg**

- 3_places_to_change

  3 places to change if you done generated temp_floorplanpattern or temp_unitplanpattern

### Raw_pattern insert and checking

- Pattern_checking
  
  script for checking and changing raw_floorplan or raw_unitpattern
  
- Unit_explosion_from_rawfloorplan_unhandled

  insert into temp_unitplanpattern from 10.1.1.237 and raw_floorplan_unhandled
