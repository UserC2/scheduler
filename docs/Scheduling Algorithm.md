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
Start condition: '01/09/2022'
End condition: '30/06/2023'

Name: Grade 11 S2 School Day
Transparent: True
Repeat condition: ON Weekdays
Repeat start: BEFORE "Grade 11 School Year"
Repeat end: AFTER "Grade 11 School Year

Name: Physics P4
Start condition: AT '01:40 PM'
End condition: AT '02:55 PM'
Repeat condition: ON "Grade 11 S2 School Day"

Name: Robotics Weekday Meeting
Start condition: AFTER "School Day"
Can schedule until: '10:00 PM'
Minimum length: '30 minutes'
Repeat condition: ON Tuesdays AND ON Thursdays
Repeat start: BEFORE "Grade 11 School Year"
Repeat end: BEFORE "Robotics World Championships"

Name: Robotics World Championships
Start condition: '01/05/2023'
End condition: '07/05/2023'

Name: Video Games
// true on either event, operator on left will take priority.
Can schedule from: AFTER "Robotics Meeting" OR AFTER "Grade 11 S2 School Day" + 25 minutes
Can schedule until: '10:00 PM'
Minimum length: '20 minutes'
Maximum length: '1 hour'
Repeat condition: Weekdays

Name: Self Reference
Start condition: BEFORE "Self Reference"
End condition: AFTER "Self Reference"

Name: Circular
Start condition: BEFORE "Reference"
Maximum length: '30 minutes'

Name: Reference
Start condition: BEFORE "Circular"
Maximum length: '30 minutes'
```

### Schedule All Events
* Provided with `staticEvents` and `dependentEvents` arrays (both an array of `InterpretedEvent`).
* For each `InterpretedEvent` in `staticEvents`:
	* Evaluate each condition for the event and convert them to iCalendar dates.
		* If a range is specified, schedule the event at the start of the range for `Maximum length` or until `End condition`.
* Repeat until all events in `dependentEvents` are scheduled or no events in `dependentEvents` refer to scheduled events:
	* For each `InterpretedEvent` in `dependentEvents`:
		* if the event being referred to by the dependent property is scheduled, convert the property into a time then schedule it as a static event.
* If any events remain unscheduled in `dependentEvents`, display an "Invalid Dependency" error and specify the name of each remaining event, then continue.
	* If any events remain unscheduled in `dependentEvents` and do not refer to scheduled events, then any remaining events must refer to nonexistent events or refer to each other, which makes them impossible to schedule.

### Example
* `staticEvents`:
	* "Grade 11 School Year" scheduled from '01/09/2022' to '30/06/2023' as transparent event.
	* "Robotics World Championships" scheduled from '01/05/2023' to '07/05/2023.
* `dependentEvents` Loop 1:
	* "Grade 11 S2 School Day" depends on existing event "Grade 11 School Year".
		* Schedule it from "Grade 11 School Year".start to "Grade 11 School Year.end repeating on weekdays.
* `dependentEvents` Loop 2:
	* "Physics P4" depends on existing event "Grade 11 S2 School Day".
		* Schedule it on each "Grade 11 S2 School Day" between '01:40 PM' and '02:55 PM'.
	* "Robotics Weekday Meeting" depends on existing events "Grade 11 S2 School Day", "Grade 11 School Year" and "Robotics World Championships".
		* Note that *all* events referenced must be scheduled in order to schedule a dependent event, not just some of them.
		* Schedule it from "Grade 11 S2 School Day".end until '10:00 PM' each Tuesday and Thursday between '01/09/2022' to '07/05/2023'
* `dependentEvents` Loop 3:
	* "Video Games" depends on existing events "Robotics Weekday Meeting" and "Grade 11 S2 School Day".
		* Will be scheduled on Mondays, Wednesdays, and Fridays from '3:20 PM' to '4:20 PM' between '01/09/2022' to '30/06/2023'.
		* Will not be scheduled on Tuesdays and Thursdays after "Robotics Weekday Meeting" as that violates the property `canScheduleUntil`.
* `dependentEvents` Loop 4:
	* "Self Reference", "Circular", and "Reference" all with no references to existing events.
	* An "Invalid Dependency" error is displayed with the names of these events, and they are not scheduled.
