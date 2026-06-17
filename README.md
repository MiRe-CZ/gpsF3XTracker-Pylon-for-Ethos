Do you fly also slope races (F3F)? Then try the GPS F3X Tracker for Ethos! See https://github.com/MiRe-CZ/gpsF3XTracker-for-Ethos

# GPS F3X Tracker Pylon-racing for Ethos Version 1.1

### Installation guide and user manual

## Contents
1. [General Description](#GeneralDescription)
2. [Requirements](#Requirements)
3. [Known limitations](#Knownlimitations)
4. [Installation](#Installation)
5. [Configuration](#Configuration)
6. [Locations.lua](#Locations.lua)
7. [Event modes](#Eventmodes)
8. [Usage on a site](#Usageonasite)
9. [Logging](#Logging)
10. [Flight position correction](#Flight_position_correction)
11. [Management of course direction difference](#Management_of_course_direction_difference)
12. [Change log](#Changelog)
13. [Development plan](#Developmentplan)
14. [License](#License)

<a name="GeneralDescription"></a>
## 1. General Description
GPS F3X Tracker Pylon-racing for Ethos is the LUA application for FrSky transmitters with Ethos operating system. It supports training of pylon racing events (F3D, F3E, F3R). Whole competition event is supported, beginning with start initiated by the Start race switch and counting and time measuring of preconfigured number of legs. Via a GPS sensor the turn lines are identified and an acoustic signal is given while crossing them. A supported GPS sensor must be placed in the model and connected and configured to telemetry subsystem.

The application is based on the GPS F3X(F3F/F3B) Tracker for Ethos. Its code is published under the MIT License.

This manual describes how to install, configure and use the GPS F3X Tracker Pylon-racing.

<a name="Requirements"></a>
## 2. Requirements
- GPS sensor: RCGPS-F3x/RCGPS-F3x-Slim (https://www.cassini.vision/), SM-Modelbau GPS-Logger 3 (https://www.sm-modellbau.de/GPS-Logger-3), FrSky GPS ADV and generally any other GPS with data rate 10Hz are supported. We recommend RCGPS-F3x as it was designed for F3F, fully fits to the GPS F3X Tracker and its satellite positioning and navigation information remains within an acceptable accuracy range when more than 20 satellite signals remain locked during short period of high acceleration in curves
- Ethos: versions 1.6.2 and newer are supported (previous versions have various issues in areas used by the application). Ethos versions 1.6.4 and 26.x fix some other issues, which are but not critical for operation
- Transmitter: The application supports units with touchscreen resolution 800*480

<a name="Knownlimitations"></a>
## 3. Known limitations
- Due to accuracy of GPS sensors (e.g. FrSky GPS ADV has horizontal accuracy approx 2.5m CEP, RCGPS-F3x 1.5 m CEP with SBAS) and telemetry latency the turn signals are not 100% precise, but still give a good F3X pylon-racing training experience. Moreover due to extreme acceleration in curves, which can achieve much above 4G assured for reliable GPS sensor operation, sensors can experience temporary satellite signal lock loss
- Sometimes there is a GPS-drift of given start point. In this case the whole course might drift to left or right some meters, because the turn positions are calculated in relation to the start point
- Max 14 fully defined and one "live" event sites are supported
- Application texts and menus are only in English. Speech announcements are given in language configured in the transmitter
- Application is resource demanding. There should not be many other widgets/system tools/tasks/sources running on transmitter otherwise accuracy of application can be compromised. It is valid also vice versa, so other applications can be affected by the GPS F3X Tracker
- Currently the Tracker supports only courses with 2 pylons

<a name="Installation"></a>
## 4. Installation
Unzip the installation package gpstracko.X.x.zip, downloaded from the repository (Releases gpsF3XTracker-Pylon-for-Ethos), and place all files into directory /SCRIPTS on your transmitter. Folders gpstraca (keeping setup part) and gpstrack (keeping operation part) should not be changed. Please note all modules, excluding locations.lua, are in the compiled form (*.luac).
Start the transmitter and configure two widgets "GPS Pylon Tracker Setup" and "GPS Pylon Tracker" when a target model is selected. The application is capable to partly modify size of text to size of widget windows, however, for accommodation of all information properly it is recommended to use at least half height & full wide layout for both setup and main widget (in such case widget titles should be switched off), or full height & half wide layout:

<img width="434" height="206" alt="image" src="https://github.com/user-attachments/assets/c9a4b177-bd80-41db-91c7-f67cbe5cda34" />

<img width="435" height="187" alt="image" src="https://github.com/user-attachments/assets/33fb4113-139d-4eea-b85e-43da16280ef6" />

Note: upgrade from a previous program version can be done simply by replacing of all program modules by new ones. It is strongly suggested to delete both widgets first and create them after replacement, checking and setting back all configuration items.

<a name="Configuration"></a>
## 5. Configuration
- GPS sensor: set data rate to 10Hz = 0.1s (or higher if possible without lost of accuracy). Crosscheck names of available GPS sensors and rename, if needed. The application expects sensors coordinates, speed and satellites, if supported, with any of following name:
	- Coord_names = {"GPS", "gps"}
	- Speed_names = {"GPS Speed", "GPS speed", "gps speed", "GSpd", "gspd"}
	- Sats_names = {"GPS Sats", "GPS sats", "gps sats", "GSats", "gsats", "GPS Satellites"}

- For individual telemetry units:
	- RCGPS-F3x: use firmware 0.0.3d or newer. Create a DIY sensor "GPS Sats" with Application ID is 0x5111. Sensors coordinates, speed and satellites are supported
	- SM-Modelbau GPS-Logger 3: be careful – due to error in the Logger firmware v1.31 will Ethos wrongly recognize a sensor with Application ID 0x0860 and with name "GPS Satellites". It is needed to delete such sensor and create a new DIY sensor with Application ID 0x0870! It is probably corrected in the firmware v1.32, published on 23.01.2026. Sensors coordinates, speed and satellites are supported
	- FrSky GPS ADV: Sensors coordinates and speed are supported
	- Other GPS: Sensors coordinates and speed are supported

Note: Speed sensors must be configured in Ethos to give m/s!
Note: if the GPS sensor was bind (discovered) in the Ethos system version 1.6.1 or earlier, it is strongly recommended to delete it and discover again. It should fix various issues affecting Ethos before version 1.6.2
Note: not needed other telemetry values should be disabled in Ethos to speed up the telemetry transfer to transmitter. Also if possible, other telemetry sensors should not be used. A receiver polls 28 potential physical sensor units periodically, in an approximately 12 ms cycles (Physical IDs 0 - 27). One sensor unit can have multiple sensors types (Application IDs). Polling is dynamic, where active sensors are polled more often, without waiting for all 	the inactive ones, improving the data refresh rate. This means, if there is one active sensor it will be polled every 24ms, when there are two active sensors they will be polled every 36ms, etc. Lot of active sensors means problem for delivery of GPS coordinates as polling interval for individual sensor (= individual Physical and Application IDs) can be hundreds of milliseconds

- "GPS Pylon Tracker Setup" widget configuration:
	- Event place: any item from list of places in locations.lua file
	- Course direction:

    a) 2-pylon: course bearing from the pylon 2 to the pylon 1 in degrees. Take direction by a compass from the pylon 2 to the pylon 1, e.g. 90° if the course direction is exactly East → West (*)
    <img width="340" height="116" alt="image" src="https://github.com/user-attachments/assets/c065f85b-59c1-4ee6-937e-6530693943f9" />
 
    b) 3-pylon: course bearing from center of line between pylons 2 and 3 and pylon 1
     <img width="270" height="175" alt="image" src="https://github.com/user-attachments/assets/6e36aa5a-1ab6-4dda-8df2-bb221a8da4b6" />

    Standard distances: B=40m, C=30m, D=20m, E=180m
     
	- Course difference: change of standard 180m course length <-100, +100> (*)
	- Competition type: any type from supported types (2-pylon_training, 3-pylon_training, 2-pylon_debug) (*)
 	- GPS sensor: any item from list of supported units
	- Lock GPS Home position switch: any 2-position switch, physical or logical, or any other source with range <-100, +100>, mandatory (**)
 	- Course direction difference management: source for real-time change of course direction, not mandatory, see chapter 11

	(*) These items are available only for "Live Position & Direction event", otherwise are locked as they are determined by event information from locations.lua file

	(**) Switch has to keep “high” value for permanent locking the GPS Home position, a momentary switch is not suitable. If a functional switch should be used, it must be	configured as status switch

<img width="434" height="218" alt="image" src="https://github.com/user-attachments/assets/d955a2eb-6356-472e-b6cd-09184e002859" />
<img width="437" height="95" alt="image" src="https://github.com/user-attachments/assets/92e80922-43e8-491b-b2b9-f40d761e6af3" />

- "GPS Pylon Tracker" widget configuration:
	- Start race switch: any 2-position switch, physical or logical, or any other source with range <-100, +100>, mandatory (*)
	- Logging: controls logging of event information
 	- Flight correction factor management: Source for setting of the Flight correction factor during flight, not mandatory, see chapter 10
 	- Flight correction factor: defines value for correction of flight position, 0 = no correction
	- Input debug GPS latitude and longitude: used for emulation of GPS input in debug mode  (suggested analog sources elevator and rudder), not mandatory

  (*) Switch can be state or momentary
  
<img width="392" height="197" alt="image" src="https://github.com/user-attachments/assets/97e3e55b-4090-4172-bcd6-1db49781221b" />

<a name="Locations.lua"></a>
## 6. Locations.lua
File Locations.lua, located in "/scripts/gpstrack/gpstrack" folder, keeps event location items in the format "Name of event site, home latitude, home longitude, course direction, course length difference, event type". Event types are:
- type 1: 2-pylon training 
- type 2: 3-pylon competition
- type 3: 2-pylon debug

Default Location.lua looks like below. You can edit it as per your needs, but please do not delete the first row, which defines "live" event location. Default event type in such case is f3f training (type 1), however you can change it during configuration. Home position for “live” event location is defined from current GPS information. Please do not remove the last entry either (create new sites before that item) and keep its name "Last Entry". Max 15 event sites are supported:

    {name = "Live Position & Direction", lat = 0.0, lon = 0.0, dir = 0.0, dif = 0, runs = 0, comp = 1}
    {name = "Debug", lat = 53.550707, lon = 9.923472,dir = 9.0, dif = 0, runs = 5, comp = 3},
    {name = "Test 2-pylon", lat = 31.212000, lon = 121.400000, dir =   0.2, dif = -50, runs = 10, comp = 1},
    {name = "Test 3-pylon", lat = 47.701974, lon = 8.3558498, dir = 152.0, dif = 0, runs = 10, comp = 2},
    {name = "Last Entry", lat = 0.0, lon = 0.0, dir = 0.0, dif = 0, runs = 10, comp = 1}

Notes:
- home latitude and longitude is for 2-pylon events center of the course (on start/finish line)
- home latitude and longitude is for 3-pylon events position of center between pylon 2 and 3 (TBD)
- course length of competition event types is defined as per F3X rules - 180m. You can change this default course length for a particular event site via item “dif” if needed – course is longer when value is positive and course is shorter when value is negative. It is not possible to change distance between pylons 2 and 3 for 3-pylon events. 2-pylon debug has its course length set internally to 30m
- purpose of the "Live Position & Direction" event place is generally to test&try a new site and tune course parameters. If the site looks suitable and you expect to fly there more, it is necessary to create for it a new record in the Locations.lua (via Edit function) - all course parameters are then recorded. Otherwise the Tracker takes course direction, course length difference and competition type from the “Live Position & Direction” record in the Locations.lua, where by default is 'dir = 0.0, dif = 0, comp = 1' !

You can edit the Location.lua file on a PC or edit individual records via embedded editor. Use button “Edit event place” on the site configuration screen to enter the editor. The original screen will be replaced by a new screen allowing editing of all parameters:

<img width="416" height="211" alt="image" src="https://github.com/user-attachments/assets/5211d30a-0986-4a17-88e9-308034f06ed3" />
<img width="416" height="32" alt="image" src="https://github.com/user-attachments/assets/85bbf40b-dd78-4d19-9d57-7446b08cc61a" />

Button “Save” saves modified site as below:
    1. Creation of a new site from the “Live Position & Direction” event place – if list of sites is not full, new line with provided information will be created in the Locations table, just before the "Last Entry" line. Do not forget to change name of new event place. If list of sites is full, save operation will be refused and indicated by message "List of sites is full!"
    2. Editing of an existing site – changed parameter(s) will be written into relevant site line in the Locations table 

Editor can be closed by standard “back” widget Ethos button

Note: deletion of site lines in the Locations table and edition of parameters for “Live Position & Direction” event place is possible only via PC

<a name="Eventmodes"></a>
## 7. Event modes
The application supports 2-pylon_training, 3-pylon_training, 2-pylon_debug event types. They behave differently as below:
- 2-pylon_training: it follows F3D rules, however only with 2 pylons. The run timer is started by a pilot via the Start race switch. Time is then measured for individual concluded laps and for whole training event
- 3-pylon_training: it follows F3D rules fully. Time is then measured for individual concluded laps and for whole training event
- 2-pylon_debug: similar to 2-pylon_training, however it uses emulation of GPS input (via configured sources “Input debug GPS latitude and longitude”) for simulation of flight

The actual status is indicated by individual rows in the "GPS Pylon Tracker" widget screen:
- Comp factor:  defines value for correction of flight position, 0 = no correction
- Comp: “waiting for start…”, "started…" - just after switching the “Start race switch” on, "canceled…" - cancellation can be done by switching off/on/off of the “Start race switch”; information about concluded laps
- Runtime: time used for individual event
- Course: "center", "leftOutside", "leftInside", "rightOutside", "rightInside" - distance from the center is provided
- Spd, Dst: speed, distance from the center
- GPS: actual GPS position
- Runs: list of last events of the same type with their runtime

<img width="241" height="113" alt="image" src="https://github.com/user-attachments/assets/faa42b08-e384-4e72-92f7-ff7e5c7b88ed" />


Announcements and sounds: 
- Beep after switching the "Start race switch" on (600 Hz)
- Beep when crossing pylons plane from inside to outside (1000 Hz)
- Beep when crossing the Start/Finish line from left to right in the last lap (1400 Hz)
- Lap number announcements
- Overall runtime at event end for F3F-x event types

<a name="Usageonasite"></a>
## 8. Usage on a site
- Switch on RC system and give the GPS sensor enough time for initiation and satellite detection - it can take 60+ seconds! For example for GPS-Logger 3 please wait till the orange LED is off and the green LED glows permanently or flashes. Open “GPS Pylon Tracker Setup” widget and select “Live Position & Direction” event place or any pre-configured place.
- For "Live Position & Direction event place":
	- Set "Competition type" configuration items if needed
	- Go with your model to the home of the course (for 2-pylon events it is center of the course (on start/finish line),  for 3-pylon events it is center of line between pylons 2 and 3)
	- Wait for stable information in the "GPS" row in the "GPS Pylon Tracker Setup" widget screen
 	- Take direction by a compass (2-pylon: course direction from the pylon 2 to the pylon 1 in degrees, 3-pylon: course direction from center of line between pylons 2 and 3 and pylon 1) and set it to the “Course direction” item (e.g. 90° if the course direction is exactly East → West)
	- Lock the position with the "Lock GPS Home position switch" - such status will be indicated by change of item name to "GPS Home lck" 
	- Now your flight configuration is ready, you can go to start

- For other event places (pre-configured in the Location.lua file):
	-Your flight configuration is ready - all parameters are set in the Location.lua file

- Go to the "GPS Pylon Tracker" widget screen
	- The initial status is indicated by statement "waiting for start..." 

<img width="216" height="110" alt="image" src="https://github.com/user-attachments/assets/998782e4-41fe-4304-9ca7-b39dff16a9aa" />

- Start new event with the "Start race switch"

<a name="Logging"></a>
## 9. Logging
Logging of flight event information is supported. It should be used only if really necessary as it can affect performance of the application. Logging is disabled by default and has to be enabled in the "GPS Pylon Tracker" widget configuration. Flight events are written into log files “YYYYMMDD-Log” located in the folder /scripts/gpstrackp/gpstrack.

Recording begins when a flight is started by the “Start race switch” and ends when an event is concluded. An initial log row looks e.g. like:

_Start 16:06 Course direction:12.5°, Course difference:0m, luaRamAvailable:1758840B, Correction factor:0.0_

Next rows have format as below, where:

_comp.state, GPS:  latitude, longitude, Dist2home, Dir2home, Course distance, Speed, Sats_

- comp.state: provides information about current flight stage (20 – start competition timer, 25 – waiting for plane crossing right base from inside, 27 – waiting for plane crossing left base from inside, 28 – waiting for plane crossing start/finish line from left to right, 30 – end of event)
- GPS: gives current position of the plane
- Dist2home: gives current distance between the plane and home point (center of the course) in meters (negative value means the plane is on the left from the home point, positive value means the plane is on the right)
- Dir2home: gives current angle between the plane and home point in rads
- Course distance: gives current distance between the plane and home point in meters, projected into the event geometry. It gives information about position of the plane in relation to bases A and B
- Speed: speed of flight in m/s
- Sats: number of visible and used GPS satellites (available only for sensors providing that information)

<a name="Flight_position_correction"></a>
## 10. Flight position correction
Due to latency of GPS sensor, telemetry latency and processing latency the turn signals are not 100% precise. The application has implemented basic mechanism aiming to compensate this issue.  The function is controlled via value of item “Flight correction factor” in the "GPS Pylon Tracker" widget configuration:
- 0: correction is disabled
- 0.01 – 0.50: correction is enabled

Corrected position depends on a current fight speed and it is calculated as per formula below:
CorrectedDistance =  ReportedDistance + FlightcorrectionFactor * groundspeed

The best value of the Flight correction factor must be found as it depends on hardware and software conditions. For example the Flight correction factor = 0.1, compensates the overall position inaccuracy related to one delayed report from a GPS sensor set to 10 Hz, so ideally reporting each 0.1s. In a case of speed 100 km/h, compensation is 2.8m, with speed 150 km/s it is 4.2m. The Flight correction factor = 0.5 compensates 5 delayed reports and compensation is 14m respectively 21m for the same speeds.

You can set the Flight correction factor manually or you can set its best value during test flights:

- There is new configuration item of type Source "Flight correction factor management". If it isn’t configured, it is possible to change the configuration item "Flight correction factor" manually, otherwise the item "Flight correction factor" is managed by the configured source
- Value of the item "Flight correction factor management" must be in range 0-50%, I advise to create a Var, e.g.:

	<img width="393" alt="image" src="https://github.com/user-attachments/assets/5b9366b1-de47-44a1-8c0d-a8e72fdd5364" />

- Change of value is in the Tracker accepted only during status "waiting for start...". However value of the source can be changed anytime and the Tracker will accept it when goes again into the status "waiting for start..."!
- Change of value is announced by voice, be patient as Ethos chains voice messages
- Last value is stored in the item "Flight correction factor", you can see it in the operation widget
- After landing configure the item "Flight correction factor management" as empty – in this way you will fix the found best value of the correction factor.   It is expected to use this function only when a new model is configured or its hardware (sensors, receiver) has changed, so the "Flight correction factor management" configuration item is not mandatory and should stay generally empty to avoid unexpected change of the correction factor!

<a name="Management_of_course_direction_difference"></a>
## 11. Management of course direction difference
Accuracy of setting the course direction is crucial for correlation between actual position and the course frame (vertical planes at base A and B). Any mistake in setting of the course direction means wrong indication in crossing of bases. Depending on how far you fly in front from the center point of the course, inaccuracy in indication can be couple of meters even for relative small error in course direction (error 5° means 0.4m inaccuracy 5 meters from the center point, but 2.6m when you fly 30 meters from it). Wrong course direction can be identified by a permanent “move” of both bases to the left or right. When crossing of both bases is reported to the right of bases, try to decrease the course direction and vice versa.

Standard course direction for individual event can be permanently changed via configuration and reflected in the related event location item in the Locations.lua. For the “Live Position & Direction” event place there is possibility to change its course direction in the range <-10, +10> degrees during flight without landing.

For the feature to run you need to set a source for the “Course direction difference management” configuration item. It is suggested to use e.g. a free trim configured with Easy mode and Fine step – in such case each trim move will change the direction by one degree in positive or negative manner.  A Var with range -20%-20% with any suitable source is also very good and universal solution.

Change of the direction is possible only when the position is locked with the “Lock GPS Home position switch” – change is confirmed by a voice announcement and indicated on the “Course Direction” row of the “GPS Pylon Tracker Setup” widget.

How to use this function: Fly near axis of the course, observe where the detection occurs, and then fly in the area far from the axis. Observe the difference and try to correct if necessary, until you feel you have found the correct course direction.

Notes:
- do not use this function without tuning of the Flight correction factor first! Otherwise you can face inaccurate position information!
- course direction made by this feature is not considered as permanent and it isn’t recorded. If the change in the course direction should be set permanently, configure it after landing in the Locations.lua
- if the source configured for the “Course direction difference management” item isn’t zero/neutral at a moment of locking with the “Lock GPS Home position switch”, its value is considered and reflected as course direction difference
- if the “Course direction difference management” source changes its value after start of a “Live Position & Direction” event, the event is canceled and it goes into "waiting for start…" status
- it is expected management of course direction will be used only rarely for previously unknown sites or for testing, so the “Course direction difference management” configuration item is not mandatory and should stay generally empty to avoid unexpected change of the course direction!

<a name="Changelog"></a>
## 12. Change log
V1.1:
- only lap number without lap time is announced to mitigate delay in indication of reaching pylon 1

<a name="Developmentplan"></a>
## 13. Development plan
At this moment we plan:
    a) Implement support for 3-pylon scenario

It is probable there will be necessary to change or enhance some parts. Do not hesitate to comment and come with ideas, preferably via an Issue in the GitHub repository (New issue gpsF3XTracker-Pylon-for-Ethos)

<a name="License"></a>
## 14. License
Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

Copyright © 2026 Milan Repik


