# Scheduler App
* A condition-based scheduling system for calendar events.

## Description
* This app allows planning events similar to a calendar, but has much more flexibility when planning events.
* Instead of specifying a start/end time for events, you specify conditions that restrict when the event can be scheduled.
* These restrictions tell the scheduler how to arrange events.
* For example, here is how you could schedule an after-school robotics meeting:
	```
	Name: Robotics Weekday Meeting
	Start condition: AFTER "school day" + 10 minutes
	Can schedule until: 10:00 PM
	Repeat condition: ON Tuesdays, Thursdays
	Repeat start: BEFORE "school year"
	Repeat end: BEFORE "robotics world championships"
	Interrupts: All
	```
	* This plans an event named "Robotics Weekday Meeting" on every Tuesday and Thursday between the start of the event "school year" and the start of the event "robotics world championships". The event starts 10 minutes after the event "school day" completes and continues until 10:00 PM, or until it is marked as completed by the user (whichever comes first).
* Should events be completed earlier than they are supposed to end, the scheduler will fill in the free time using other events scheduled at the same time.
* For example, you could have dinner scheduled later that day, as well as some time to complete homework:
	* Dinner:
	```
	Name: Dinner
	Can schedule from: AFTER 06:00 PM
	Can schedule until: 11:15 PM
	Minimum length: 20 minutes
	Maximum length: 1 hour
	Repeat condition: Daily
	Interrupts: All
	```
	* Notice 'Can schedule from' is used instead of 'Start condition'. This specifies that the event *can* start `AFTER "school day"`, but does not require it to, whereas 'Start condition' starts the event immediately when the condition is true.
	* Homework:
	```
	Name: Homework
	Can schedule from: AFTER "school day"
	Can schedule until: 11:15 PM
	Minimum length: 30 minutes
	Repeat condition: ON Weekdays
	Repeat start: BEFORE "school year"
	Repeat end: AFTER "school year"
	```
	* The 'Minimum Length' property ensures that this event runs for at least 30 minutes. For example, the scheduler would not schedule a homework event if there was only 3 minutes to spare in the schedule.
* Assuming "Robotics Weekday Meeting" continued until 10:00 PM, the scheduler would schedule "Dinner" between 10:00 PM and 11:00 PM, and leave free time after 11:00 PM, since there is only 15 minutes between 11:00 PM and 11:15 PM, which does not satisfy the 'Minimum length' constraint of "Homework".
* If "Robotics Weekday Meeting" completed one hour early, then the scheduler would instead schedule "Dinner" between 09:00 PM and 10:00 PM. This allows enough time for "Homework", so "Homework" would be scheduled beginning at 10:00 PM. Since it has no 'Maximum length' property, it will continue until the 'Can schedule until' time of 11:15 PM.
* In addition to scheduling the events, the scheduler keeps track of previously completed events, including whether or not they were interrupted, the time spent performing them, and the date/time they started and ended.

## Motivations
* Specifying events has elements similar to a programming language (identifiers, expressions, operators, literals, and data types), which I think will be interesting to implement. (I also wonder if I can get the language to do anything it isn't supposed to.)
* I can use the app once it is complete, I use my calendar often for scheduling events, so I could use this app instead, which would make it easier to schedule flexible events and adapt the schedule to any interruptions during the day.
* By syncing the scheduling app with a reminders app (e.g. Microsoft Todo), I can have the app suggest things for me to do during my free time, which could make me more productive.

## Incorporation of Class Topics
* An algorithm is used to schedule activities efficiently according to their properties.
* A database is used to keep track of completed/interrupted activities.

## Milestones
1. Language Interpreter completed & tested
	* Properties of an event can be stored as variables in an object.
	* Properties of an event can be **read from a file and stored** to an event object.
	* Properties of an event object can be printed out to the user.
2. Scheduling Algorithm completed & tested
	* Define the pseudocode for the scheduling algorithm.
	* Implement the scheduling algorithm in Dart. It should:
		* Accept an array of events as input
		* Output scheduled events in chronological order, providing their name, the time they start and the time they end.
		* Print any events that cannot be scheduled in a seperate list.
		* Set the appropriate `Interrupted By` property for interrupted events and display them to the user.
3. Working Event Log
	* The event log is capable of recording events once they are marked as completed or interrupted.
	* The event log records the data specified in `Additional Ideas` -> `Event Log`.
4. Sync schedule with other calendar apps (to provide notifications):
	* Schedules can be read from a calendar app
	* Schedules can be sent to a calendar app
	* (This will make it much easier to sync the schedule across devices and provide notifications.)
5. Cross-Platform UI App
	* Possible to perform all operations in `Scheduling Operations` in the app.
	* Possible to generate notifications according to the current schedule.
	* Syncs with other installations of the app on the same account. (Can be implemented by syncing app with other calendar apps.)

### Milestone Completion
* Week of June 11-June 17
	* Tuesday 13th: Define event object in Dart.
	* Wednesday 14th: Define pseudocode for event interpreter from file.
	* Thursday 15th: Finish event interpreter.
	* Friday 16th: Define pseudocode for scheduling algorithm.
	* Saturday 17th: Implement scheduling algorithm.
* Week of June 18-June 24
	* Sunday 18th: Implement event log database.
	* Monday 19th: Connect event log database to scheduler app.
	* Tuesday 20th: Read events from & send events to a calendar app.
	* Wednesday 21st: Define UI for cross-platform app.
	* Thursday 22nd: Implement UI for cross-platform app.
	* Friday 23rd: Period 2 Exam — Present Culminating Project

# Specifications

## Condition System
* Instead of using dates & times, the scheduler uses 'conditions' (an operator and a value). This allows events to be scheduled according to other events (e.g. "robotics meeting" start: AFTER "school day"), while still allowing time based conditions (e.g. "robotics meeting" start: AT 03:05pm).
* Operators have a type that determines where they can be used (e.g. AFTER can only be used to specify a *start* condition).

### Special Values
* Format: Value
	* Type: *condition types that value can be used with*
	* Meaning: Description of special value.
* All
	* Type: *event*
	* Meaning: Represents all events.
* Interruptible
	* Type: *event*
	* Meaning: Events where `Do not interrupt` is False.
* True
	* Type: *boolean*
* False
	* Type: *boolean*
* Hourly
	* Type: *repeat condition*
	* Meaning: True once for every hour.
* Daily
	* Type: *repeat condition*
	* Meaning: True once for every day.
* Weekly
	* Type: *repeat condition*
	* Meaning: True once for every week.
* Monthly
	* Type: *repeat condition*
	* Meaning: True once for every month.
* Yearly
	* Type: *repeat condition*
	* Meaning: True once for every year.
* Never
	* Type: *repeat end condition*
	* Meaning: Always true.
* Null
	* Type: *all*
	* Meaning: A lack of a value.
* Special Dates:
	* Weekdays
		* Alias for `Monday, Tuesday, Wednesday, Thursday, Friday`
	* Weekends
		* Alias for `Saturday, Sunday`
	* Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday
		* Matches respective day.
	* January, February, March, April, May, June, July, August, September, October, November, December
		* Matches respective month.

### Date & Time
* Time:
	* Format: `HH:MM:SS AM/PM`
	* Hours:Minutes:Seconds AM/PM
	* If `:MM` or `:SS` are not specified, they are assumed to be 0.
	* If `AM/PM` is not specified, time is assumed to be 24 hour time.
	* If `AM/PM` is specified, time is converted to 24 hour time (hours scaled between 01-12 if `AM`, 13-24 if `PM`).
* Amount of time:
	* Format: `# unit-specifier`
	* Example: 10 days
	* Unit specifiers: `years`, `months`, `weeks`, `days`, `hours`, `minutes`, `seconds`
* Dates:
	* Dates are specified using the following formats:
	* `DD/MM/YYYY`
		* Example: 09/06/2023
	* `Month DD YYYY`
		* Example: June 09 2023
* To specify a date and time, seperate the date and time with ` : `:
	* Example: Friday June 09 2023 : 05:04 PM
	* Example: 09/06/2023 : 17:04

### Operators
* Format: OPERATOR NAME \<parameter>
	* Type: *condition types that operator can be used with*
	* Description of operators.
* AFTER \<event>
	* Type: *start* 
	* True immediately after event is completed.
* BEFORE \<event>
	* Type: *end*
	* True immediately before event begins.
* AT \<date or time>
	* Type: *start*, *end*
	* True once date/time = current date/time
	* AT does not work with events to discourage the user from creating "start/end events" such as `AT "beginning of school year"`. The correct behaviour would be to use `BEFORE "school year"`.
* ON \<date or time>
	* Type: *start*, *end*
	* True once date/time = current date/time
	* Alias for `AT` (included for grammatical correctness)
* COMPLETED FOR \<completion time>
	* Type: *repeat end*, *end*
	* True once total time completed >= completion time
* Logical Operators: AND, OR, XOR, NOT, ==
* Mathematical Operators: +, -, *, /
	* Integer division is not allowed.
* "\<string>":
	* Type: *event*
	* Indicates an event name.

## Activity Properties
- `&` indicates related, but seperate properties.
- Format: `Property Name (type default: default-value)`
- Required properties for event creation:
	* Name
	* Type
	* Start condition OR Can schedule from
	* End condition OR Can schedule until OR Max length

* Name (string)
	* e.g. "Physics Class - P4"
	* Any unicode characters valid

* Type (string default: *Name*)
	* e.g. "Highschool Class"
	* Any unicode characters valid
	* If this is not specified, the value of `Name` will be used.

* Interrupted (all of the following are: event default: null)
	* Cancelled by
	* Delayed by
	* Spliced by
	* Stopped by

* Transparent (boolean default: False)
	* If true, event will never be interrupted, and never interrupt other events.
	* Event can run during other events.

* Interrupts (event-type default: Interruptible)
	* Interrupt events matching *event-type*.
	* Example:
		* `Name`: Dentist Appointment
		* `Start condition`: 02:05 PM
		* `Maximum length`: 1 hour
		* `Interrupts`: All
		* Interrupts all events starting at 14:05 PM on the day it is scheduled for 1 hour or until completed.

* Minimum length & Maximum length
	* Schedule event for at least `Minimum length` and at most `Maximum length`.
	* Example:
		* `Name`: Physics Homework
		* `Min length`: 30 minutes
		* `Max length`: 120 minutes
		* Event will be scheduled for at least 30 minutes and marked as completed after 120 minutes.
	* Min and max are compatible with ONE OF start and end condition only.
	* Min and max are not compatible with start and end condition used together.

* Start condition (*condition*) & End condition (*condition*)
	* Schedule start/end of event when condition is true.
	* Example:
		* `Name`: Physics P4
		* `Start condition`: AT 1:40 PM
		* `End condition`: AT 2:55 PM
	* When `Start condition` specified, cannot use:
		* Can schedule from
	* When `End condition` specified, cannot use:
		* Can schedule until
	* When both specified, cannot use:
		* Minimum/maximum length
		* Can schedule from/until

* Can schedule from (*condition*) & Can schedule until (*condition*)
	* Allow event to be scheduled starting at `Can schedule from` *condition* and ending at `Can schedule until` *condition*.
	* Example
		* `Name`: Robotics Meeting
		* `Start condition`: AT 03:05 PM
		* `Can schedule until`: AT 8:30 PM
		* Event will be scheduled starting at 3:05 PM and continue until completed or until 8:30 PM.
	* When `Can schedule from` specified, cannot use:
		* Start condition
	* When `Can schedule until` specified, cannot use:
		* End condition
	* When both specified, cannot use:
		* Start/end condition

* Repeat condition (*condition*)
	* Repeat event when *condition* true.
	* e.g. EVERY 7 days
	* e.g. ON Tuesday AND ON Thursday
	* e.g. Weekdays

* Repeat start condition & Repeat end condition (*condition*)
	* Restrict range of repeat to times where *condition* is true.
	* Example
		* `Name`: Robotics Saturday Meeting
		* `Repeat`: ON Saturday
		* `Repeat Start`: AFTER "robotics kickoff"
		* `Repeat End`: BEFORE "robotics world championships"
	* If start is not specified, the current date is assumed.
	* If end is not specified, 'Never' will be assumed.

## Activity Examples
* `Name`: Physics P4
	* `Start condition`: AT 1:40pm
	* `End condition`: AT 2:55pm
	* `Interrupts`: All
	* `Repeat condition`: ON Weekdays
	* `Repeat start`: BEFORE "grade 11 semester 2"
	* `Repeat end`: AFTER "grade 11 semester 2"
* `Name`: Robotics Weekday Meeting
	* `Start condition`: AFTER "school day" + 10 minutes
	* `Can schedule until`: 10:00 PM
	* `Minimum length`: 30 minutes
	* `Repeat condition`: ON Tuesday AND ON Thursday
	* `Repeat start`: BEFORE "school year"
	* `Repeat end`: BEFORE "robotics world championships"

## Scheduling Operations
The schedule is regenerated after each operation.

### User Invoked (Normal) Operations
* Add event
	* Add event with specified properties
* Modify non-scheduling details of event
	* e.g. correct spelling error or change name of event
	* Apply modifications to *all matching events*
* Modify scheduling details of event
	* e.g. increase `Minimum time` to 1 hour from 30 minutes
	* Apply modifications to *future events only*
* Remove event
	* Erase event from scheduler (does not remove from event completion database) and regenerate schedule

### User Invoked (Interrupting) Operations:
* If a user desires to interrupt an event, the program will generate an event which causes the desired interruption to occur.
* The user can specify an event to interrupt, or specify a start & end condition for the interrupt.
* The user can optionally specify a name for the interrupt.
* All interruptions: Create event:
	* Set Name: ((*provided name* NOT null) ? *provided name* : `Autogenerated`) + `-` + *interrupt type* + `:` + *event to interrupt*
	* Set `Interrupts`: *event to interrupt*
* Cancel event:
	* The user can specify an event to interrupt, or specify a start & end condition for the interrupt to interrupt multiple of the same event.
	* Set `Start condition`: (*selected event* NOT null) ? *start condition of selected event* : *provided start*
	* Set `End condition`: (*selected event* NOT null) ? *end condition of selected event* : *provided end*
* Delay event:
	* If (*start condition of selected event* NOT NULL) Set `Start condition`: *start condition of selected event*
	* If (*Can schedule from of selected event* NOT NULL) Set `Can schedule from`: *can schedule from of selected event*
	* Set `End condition`: AT *provided delay until condition*
* Splice event:
	* Set `Start condition`: AT *provided start condition*
	* Set `End condition`: AT *provided end condition*
* Stop event:
	* If (*end condition of selected event* NOT NULL) Set `End condition`: *end condition of selected event*
	* If (*Can schedule until of selected event* NOT NULL) Set `Can schedule until`: *Can schedule until of selected event*
	* Set `Start condition`: AT *provided stop condition*
* **Note that the more correct way to do this is to schedule a legitimate event occuring during this time instead of autogenerating one.**

### Scheduler Invoked (Interrupting) Operations
* Cancel event
	* Indicate an event cannot be scheduled.
	* Set `Cancelled`: True
	* Set `Cancelled by`: *cancelling event*
* Delay event
	* Delay the start of an event.
	* Set `Delayed`: True
	* Set `Delayed by`: *delaying event*
	* Set `Can schedule from`: Null
	* Set `Start condition`: AFTER *delaying event*
* Splice event
	* Schedule an event during another event.
	* Set `Spliced`: True
	* Set `Spliced by`: *splicing event*
	* Create new event from *event being spliced*:
		* Set `Can schedule from`: Null
		* Set `Start condition`: AFTER *splicing event*
	* Set `Can schedule until`: Null
	* Set `End condition`: BEFORE *splicing event*
* Stop event
	* Stop an event early.
	* Set `Stopped`: True
	* Set `Stopped by`: *stopping event*
	* Set `Can schedule until`: Null
	* Set `End condition`: BEFORE *stopping event*

## Event Log
* A database that records information about events (the following information is recorded whenever an event is completed):
	* Entry #
	* Event ID (foreign key reference to event names)
	* Event Type
	* Elapsed Time
	* Actual start time of event
	* Actual end time of event
	* Interrupted (type + reason)
* This allows the program to deduce information about an event (e.g. how long an event typically takes), and allows for duration-based conditions (e.g. '`Repeat end`: COMPLETED FOR 25 hours').