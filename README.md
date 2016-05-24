Set of qvd files containing time intervals. The files are containgin only one column. For example the 10m qvd:

````
Range_600
00:00:00
00:10:00
00:20:00
00:30:00
...
````

The qvds can be joined to date table:

````
Load 
  Date
Resident
  MyCalendar
;

join

Load
  Range_600
From
  Range_600.qvd (qvd)
;;

````

The script to produce the qvd files:

````
Times:
Load * Inline [
	Descr  , Seconds
    1s     , 1
    5s     , 5	
    10s    , 10
    15s    , 15
    30s    , 30
    1m     , 60
    5m     , 30	
    10m    , 600
    30m    , 1800
    1h     , 3600
];

for i = 1 to FieldValueCount('Seconds')
	let sSeconds = FieldValue('Seconds', $(i));
    let sDescr = FieldValue('Descr', $(i));

    for a = 0 to 86400 step $(sSeconds)
    
    	Range_$(sSeconds):
        Load
            Time( $(a) / 86400, 'hh:mm:ss') as Range_$(sSeconds)
        AutoGenerate(1)
        ;
    next   
    
    Store Range_$(sSeconds) into [lib://Personal/Range_$(sDescr).qvd];
    Drop Table Range_$(sSeconds);

next

Drop Table Times;
````
