# Scheduling Algorithm

## Function
* Provided with a list of static events (reference only dates & times in their properties) and dependent events (reference other events in their properties).
* Schedule the events according to their properties.
* Output a list of scheduled events in chronological order, and a list of any events that could not be scheduled.
	* Any events that are interrupted are indicated using their `Interrupted` property.

## Operation
### Example Events
* These dates are not intended to be accurate.
```
Name: Grade 11 School Year
Transparent: True
Start condition: 01/09/2022
End condition: 30/06/2023

Name: Grade 11 S2 School Day
Transparent: True
Repeat condition: ON Weekdays
Repeat start: BEFORE "Grade 11 School Year"
Repeat end: AFTER "Grade 11 School Year

Name: Physics P4
Start condition: AT 01:40 PM
End condition: AT 02:55 PM
Repeat condition: ON "Grade 11 S2 School Day"

Name: Robotics Weekday Meeting
Start condition: AFTER "School Day"
Can schedule until: 10:00 PM
Minimum length: 30 minutes
Repeat condition: ON Tuesdays AND ON Thursdays
Repeat start: BEFORE "Grade 11 School Year"
Repeat end: BEFORE "Robotics World Championships"

Name: Robotics World Championships
Start condition: 01/05/2023
End condition: 07/05/2023

Name: Video Games
Can schedule from: AFTER "Robotics Meeting" // this causes shenanigans to occur...
Can schedule until: 10:00 PM
Minimum length: 20 minutes
Maximum length: 1 hour
Repeat condition: Weekdays // that doesn't work

Name: Self Reference
Start condition: BEFORE "Self Reference"
End condition: AFTER "Self Reference"

Name: Circular
Start condition: BEFORE "Reference"
Maximum length: 30 minutes

Name: Reference
Start condition: BEFORE "Circular"
Maximum length: 30 minutes
```
