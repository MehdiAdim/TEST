# GTFS Realtime feed : Assessment of the data.
  
   1- [Clone the project and install requirements](#1-clone-the-project-and-install-requirements).
   
   2- [Process realtime feed for trip update](#2-process-realtime-feed-for-trip-update).
   
   3- [Arrival time consistency errors](#3-arrival-time-consistency-errors).
   
   4- [Interpolate missing arrival time data](4-interpolate-missing-arrival-time-data).
   
   5- [Build ditributions](5-build-ditributions).
   
   6- [Vehicle assessment](6-vehicle-assessment).
              
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
   
   - Goals:
   
          1-  Generate consistency_errors.csv: Containing all Connections with a consisstency error (arrival_time at the second stop earilier than passage time at the first)
        
          2-  Generate consistency_errors_by_line.csv: Contains the ratio of impcated trips and connections by Line
        
          3-  Generate feed_time_coherence.csv: All Connections with a consisstency error on feed time (feed time at the second stop earilier than feed time at the first)
        
    
   - Running assessment:
        
        ```
           python src/processing/errors_detection.py arg1 arg2
        ```
        - arg1 : austin or netherlands
        - arg2 : Assessment directory (containing assessment results)
   
   - Example:    
        
        ```
           python src/processing/errors_detection.py austin /home/user/assessment_ouput
        ```
   - Generated tree files (structure of assessment results):
   
          └── assessment_ouput  
              └── 20200615
                  ├── ...
                  ├── ...
                  ├── ...
                  ├── consistency_errors_by_line.csv
                  ├── consistency_errors.csv
                  └── feed_time_coherence.csv
  
---

### 4. Interpolate missing arrival time data.
   
   - Goals:
   
          - Generate a folder for all trips that have missing arrival time values have filled with interpolated estimations.
        
  
   - Running assessment:
        
        ```
           python src/processing/interpolate_missed_data.py arg1 arg2
        ```
        - arg1 : austin or netherlands
        - arg2 : Assessment directory (containing assessment results)
   
   - Example:    
        
        ```
           python src/processing/interpolate_missed_data.py.py austin /home/user/assessment_ouput
        ```
   - Generated tree files (structure of assessment results):
   
          └── assessment_ouput  
              └── 20200615
                  ├── ...
                  ├── ...
                  ├── ...
                  ├── impute_missing_values
                  │   ├── 2392169_MRG_1.csv
                  │   ├── 2392174_MRG_1.csv
                  └── └── 2392177_MRG_1.csv
                  
---

### 5. Build ditributions.
   
   - Goals:
   
          - Extracting  arrival time distributions from a restricted number of days with identical (or similar) theoretical schedules.

  
   - Running assessment:
        
        ```
           python src/processing/distribution_building.py arg1 arg2 arg3
        ```
        - arg1 : austin or netherlands
        - arg2 : Assessment directory (containing assessment results)
        - arg3 : Distribution build building output folder
   
   - Example:
   
     We have the assessment folder (discussed above) for each day of one week of GEFTS_RT feed :
     
          └── one_week_assessment  
              └── 20200615
              ├── 20200616
              ├── 20200617
              ├── 20200618
              └── 20200619
     
     Running the distribution building:
     
        ```
           python src/processing/distribution_building.py austin /home/user/one_week_assessment /home/user/distribution_building
        ```
   
   - Generated tree folders (structure of assessment results):
   
          └── distribution_building  
              └── distribution_by_trip
              ├── describe_same_trips_and_stops_on_window_time
              ├── describe_same_trips_and_stops_on_window_time__with_changed_trips
     
     distribution_by_trip : 
     
        - contains for each trip and its stop_sequences the arrival time distribution over days.
     
     describe_same_trips_and_stops_on_window_time: 
     
        - contains statistic description about arrival_time over days for each stop sequence (mean, median, min, max).
        
        - contains statistic description about arrival_time_dealy over days for each stop sequence (mean, median, min, max, std, 25_percetille, 75_percentille)
     
     describe_same_trips_and_stops_on_window_time__with_changed_trips:
     
        - Contains the same as folder precendent but just for trips that have a new Line on another day (stops sequence that haven't the stops id between one dayn and another)
      

---
    
### 5. Vehicle assessment.
   
   - Goals:
   
          - ll

  
   - Running assessment:
        
        ```
           python src/processing/vehicle_assessment.py arg1 arg2 arg3
        ```
        - arg1 : austin or netherlands
        - arg2 : Feed directory (containing pb files related to vehcileUpdates)
        - arg3 : Assessment path 
   
   - Example:
     
        ```
           python src/processing/vehicle_assessment.py austin /home/user/vehicle_feeds /home/user/assessment_ouput
        ```
   
   - Generated tree folders (structure of assessment results):
   
          ...
     

