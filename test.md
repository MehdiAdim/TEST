# GTFS Realtime feed : Assessment of the data.
  
   0- [Clone the project and install requirements](#0-clone-the-project-and-install-requirements).
   
   1- [Edit configuration (to run in your local)](#1-edit-configuration-to-run-in-your-local).
   
   2- [Process realtime feed for trip update](#2-process-realtime-feed-for-trip-update).
   
   3- [Arrival time consistency errors](#3-arrival-time-consistency-errors).
   
   4- [Interpolate missing arrival time data](#4-interpolate-missing-arrival-time-data).
   
   5- [Get time window of realtime trip update feeds from wood](#5-get-time-window-of-realtime-trip-update-feeds-from-wood).
   
   6- [Build distributions](#6-build-distributions).
   
   7- [Vehicle assessment](#7-vehicle-assessment).
              
---

### 0. Clone the project and install requirements.

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

### 1. Edit configuration (to run in your local). 
   
   - To run on local environment:
   
   First, you need to have GTFS Data (especially static data) 
   
   Second. on __src/config.py__ on line 6 : edit LOCAL_GTFS_DATA_PATH to your local path (folder containing static gtfs data.
   
   ![Image 1](imgs/config.png)
   
   - To Run on server use:
   
   on __src/config.py__ Uncomment the line 50 to use Production configuration instead
   
   ![Image 1](imgs/config_2.png)
   
   
    
---

### 2. Process realtime feed for trip update.
   
   - Goals:
   
        1-  Generate the last realtime predictions for all trips and their stops (csv file for each trip).
        ![Image 1](imgs/last_predictions.png)
          
        2-  Generate Lines and their stops sequences, and the assosiated trips.
        
        Example __lines.cvs__:
        
        ![Image 1](imgs/lines.png)
        
        Example  __trip_lines__.csv:
        
        ![Image 1](imgs/trip_lines.png)
        
        3-  Generate all stops with missing real-time passage information.
        
        ![Image 1](imgs/missing_stops.png)
        
        __MISSING_TYPE_0 = 0__ Missing stop on feed (no arrival time and no departure time)
        
        __MISSING_TYPE_1 = 1__ Missing arrival_time and doubtful departure_time with [schedule arrival == schedule departure]
        
        __MISSING_TYPE_2 = 2__ Doubtful arrival_time value (0 delay)
        
        4-  Frenquency of impacted trips by route (Considering all the trips of a route that pass by a the affected stop)
        
        ![Image 1](imgs/missing_stops_by_routes.png)
        
        5-  Frequency of impacted trips by line (Considering all the trips of a line that pass by a affected stop)
        
        ![Image 1](imgs/missing_by_lines.png)
        
   - Running process:
        
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
        
        ![Image 1](imgs/consistency_errors.png)
        
        2-  Generate consistency_errors_by_line.csv: Contains the ratio of impcated trips and connections by Line
        
        ![Image 1](imgs/cons_errors_by_line.png)
        
        3-  Generate feed_time_coherence.csv: All Connections with a consisstency error on feed time (feed time at the second stop earilier than feed time at the first)
        
        ![Image 1](imgs/feed_time_incoherence.png)
        
   - Running process:
        
        ```
           python src/processing/errors_detection.py arg1 arg2
        ```
        - arg1 : austin or netherlands
        - arg2 : Assessment directory (containing assessment results)
   
   - Example:    
        
        ```
           python src/processing/errors_detection.py austin /home/user/assessment_ouput
        ```
   - Generate:
   
          └── assessment_ouput  
              └── 20200615
                  ├── ...
                  ├── ...
                  ├── ...
                  ├── consistency_errors_by_line.csv      (Not generated if 0 errors)
                  ├── consistency_errors.csv              (Not generated if 0 errors)
                  └── feed_time_coherence.csv             (Not generated if 0 incoherence)
  
---


### 4. Interpolate missing arrival time data.
   
   - Goals:
   
        Generate __impute_missing_values__ : is a folder for all trips that have missing the estimated arrival time values have filled with interpolated estimations.
        
        For each trip there is a csv file generated, contains all stops information and the interpolated estimation about the missing arrival time.
        
        ![Image 1](imgs/impute_ms_values.png)
        
   - Running process:
        
        ```
           python src/processing/interpolate_missed_data.py arg1 arg2
        ```
        - arg1 : austin or netherlands
        - arg2 : Assessment directory (containing assessment results)
   
   - Example:    
        
        ```
           python src/processing/interpolate_missed_data.py austin /home/user/assessment_ouput
        ```
   - Generate:
   
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
### 5. Get time window of realtime trip update feeds from wood.
   
   - Goals:
   
        Fetch from server all trip update feeds (pb files) related to a defined time window.
        
        Example : from 11.am to 15.am for 5 days in juin 2020)  
        
        ![Image 1](imgs/script_fetching.png)
        
        You can modify this calender to fetch just desired number of hours and days.
         
   - Running process:
        
        ```
           chmod 755 ./src/get_from_wood.sh
           ./src/get_from_wood.sh arg1 arg2 arg3
        ```
        - arg1 : Naver-labs username (madim)
        - arg2 : Austin or Netherlands
        - arg3 : Folder path to save feeds (pb files) on.
   
   - Example:    
        
        ```
           ./src/get_from_wood.sh madim Austin /home/user/feeds
        ```
   - Generate:
   
          └── feeds  
              ├── trip_updates_2020_06_15_06_00_09.pb
              ├── trip_updates_2020_06_15_06_01_09.pb
              ├── ...
              └── ...

---                  
### 6. Build distributions.
   
   - Goals:
   
       1 - Extracting  arrival time distributions from a restricted number of days with identical (or similar) theoretical schedules.
         
        __distribution_by_trip__ folder : 
     
        contains for each trip and its stop_sequences the arrival time distribution over days.
        
        ![Image 1](imgs/arrival_time_distro.png)
        
        __describe_same_trips_and_stops_on_window_time__: 
     
        - contains statistic description about arrival_time over days for each stop sequence (mean, median, min, max).
        
        ![Image 1](imgs/arrival_time_stats.png)
        
        - contains statistic description about arrival_time_dealy over days for each stop sequence (mean, median, min, max, std, 25_percetille, 75_percentille)
        
        ![Image 1](imgs/delay_stats.png)
        
        __describe_same_trips_and_stops_on_window_time__with_changed_trips__:
     
        - Contains the same as folder precendent but just for trips that have a new Line on another day (stops sequence that haven't the stops id between one dayn and another)
     
       2 - travel_time_distribution: contains Travel time distribution over days for all Connections (travel duration for each successive stops)
          
        ![Image 1](imgs/travel_time_distro.png)
        
   - Running process:
        
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
   
   - Generated this structure of folders:
   
          └── distribution_building  
              └── distribution_by_trip
              ├── describe_same_trips_and_stops_on_window_time
              ├── describe_same_trips_and_stops_on_window_time__with_changed_trips
              ├── travel_time_distribution
     
---
    
### 7. Vehicle assessment.
   
   - Goals:
   
        1- Generate __vehicle history folder__, contains csv files, each one is related to the history of one vehicle.   
        Each row is representing a distinct vehicle update (it may contains trip information or not).
        There is bellow an example of two rows of certain vehicle history: 
          
        ![Image 1](imgs/vehicle_history.png)
        
        2- Generate __available_trip_info_by_vehicle.csv__ contains the ratio of trip/stop information availability on each vehicle history. 
        For the bellow example, all the updates of the vehicle 2806 contains trip information.
          
        ![Image 2](imgs/available_trip_info_by_vehcile.png)
        
        3- Generate __available_trip_and_stops_infos_by_trip_and_vehicle.csv__ contains per trip the ratio of stops available info in the related vehicle.
        
        ![Image 3](imgs/available_stops_by_trip_and_vehicle.png)
        
        4- Generate incoherence : Trip that have 2 vehicle (vehicle update feeds) 
        
        Example:
        
        ![Image 5](imgs/errors_vehcile.png)
        
        5- Generate __trip_infos_availability_by_line.csv__: contains (by Lines) Trips & Stops info availability in vehicle feed.
        
        Example: for the Line 0 88.33% of trips having infos in vehicle update, but just 25% of stops having info in vehicle feeds.
         
        ![Image 4](imgs/trip_vehcile_info_by_line.png)
        

  
   - Running process:
        
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
   
   - Generated this structure:
   
            └── vehicle_history
                  ├── 0801.csv
                  ├── 0802.csv
              ├── available_trip_and_stops_infos_by_trip_and_vehicle.csv
              ├── available_trip_info_by_vehicle.csv
              ├── errors_trip_duplication.csv
              ├── trip_infos_availability_by_line.csv
