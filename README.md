USE AT YOUR OWN RISK. this script comes as is.

referrence for OMSA CLI:  https://goo.gl/ZTi0pk ( had to shorten the Dell URL) 

referrence for MS Tasksch:  https://msdn.microsoft.com/en-us/library/windows/desktop/bb736357(v=vs.85).aspx 

must have OpenManage installed. 


:: color and windows size

@echo off

color F0

mode con:cols=50 lines=15


:: create temp if dirctory is not there.

IF NOT EXIST C:\temp mkdir C:\temp


:CC 

@echo off

color F0

mode con:cols=50 lines=15



:: open manage CLI to gather controller slot ID.

omreport storage controller > C:\temp\controller.txt

echo Available controllers

echo.


:: search for available controllers 

findstr /b ID C:\temp\controller.txt

echo.

SET input=

Set /p CN=please select Controller (Default 0)

echo.

:: open manage CLI to gather Virtual disk ID.

omreport storage Vdisk controller=%CN% > C:\temp\vd.txt

echo.

echo Available Virtual Disks

echo.

:: search for available virtual disk

findstr /b ID C:\temp\vd.txt

echo.

SET /P VD=please select VD (1,2,3,4..etc)

if [%CN%]==[] (Set CN=0)

echo.

:: set the rate of the consistancy check default is 30%

SET input=

Set /p SCC= set consistancy check rate (0-100): 

if [%SCC%]==[] (Set SCC=30)

echo.

::create a task for every month

schtasks /Create /tn "ConsistencyCheck virtual Disk %VD% Controller %CN%" /tr "omconfig storage vdisk action=checkconsistency controller=%CN% vdisk=%VD% && omconfig storage controller action=setcheckconsistencyrate controller=%CN% rate=%SCC% " /sc MONTHLY 

schtasks /Run /tn "ConsistencyCheck virtual Disk %VD% Controller %CN%"

mode con:cols=85 lines=30

schtasks /QUERY /tn "ConsistencyCheck virtual Disk %VD% Controller %CN%"



pause
goto CC
