# GTFS Realtime feed : Assessment of the data.
  
   1- [Clone the project and install requirements](#1-clone-the-project-and-install-requirements).
   
   2- [Process realtime feed for trip update](#2-process-realtime-feed-for-trip-update).
   
   3- [Arrival time consistency errors](#2-arrival-time-consistency-errors).
              
---

### 1. Clone the project and install requirements.

  - Requirements : Python 3.6.x or newer. 
  - VPN connection & membership to https://oss.navercorp.com/am organization are required:
  - Clone the project: 
      ```
      git clone https://oss.navercorp.com/am/GTFS_RT.git
      cd GTFS_RT 
      ```
  - Install requirements:
      ```
      pip3 install --user -r requirements.txt
      ```
---

### 2. Process realtime feed for trip update.
   
   - Goals:
   
          1-  Generate the last realtime predictions for all trips and their stops (csv file for each trip).
        
          2-  Generate Lines and their stops sequences, and the assosiated trips.
        
          3-  Generate all stops with missing real-time passage information.
        
          4-  Frenquency of impacted trips by route (Considering all the trips of a route that pass by a the affected stop)
        
          5-  Frequency of impacted trips by line (Considering all the trips of a line that pass by a affected stop)
    
   - Running assessment:
        
        ```
           python src/processing/trip_assessment.py arg1 arg2 arg3
        ```
        - arg1 : austin or netherlands
        - arg2 : Folder containing feeds (trip update Protocol Buffers files)
        - arg3 : Desired ouput directory (containing assessment results)
   
   - Example:    
        
        ```
           python src/processing/trip_assessment.py austin /home/user/feeds /home/user/assessment_ouput
        ```
   - Generated tree files (structure of assessment results):
   
          └── assessment_ouput  
              └── 20200615
                  ├── last_predictions_for_all_trips_on_day_service
                  │   ├── 2392169_MRG_1.csv
                  │   ├── 2392174_MRG_1.csv
                  │   └── 2392177_MRG_1.csv
                  ├── lines.csv
                  ├── missing_stops_arrival_time.csv
                  ├── ratio_missing_stops_by_line.csv
                  ├── ratio_missing_stops_by_route.csv
                  └── trips_lines.csv


  - last_predictions_for_all_trips_on_day_service is the folder related to goal (1).
  - lines.csv and trips_lines.csv are csv files related to goal (2).
  - missing_stops_arrival_time.csv is the csv file related to goal (3).
  - ratio_missing_stops_by_route.csv is the csv file related to goal (4).
  - ratio_missing_stops_by_line.csv is the csv file related to goal (5).
  
---

### 3. Arrival time consistency errors.
        
        To be continued
        
