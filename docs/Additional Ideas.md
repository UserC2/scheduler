## Routines
* Routines act as a group of related events. They are treated as one event for scheduling purposes, but are made up of several shorter activities.
* This ensures the events are scheduled at the same time, and notifies the user for each event (whereas making one event to represent the group of events would only notify the user once).

## Deduced event length
* For events with minimum and maximum lengths, find the average amount of time the event takes using the *Event Log* and schedule the event with `Maximum length`: *calculated average*.
* Example: If dinner can take 20-50 minutes, but on average takes 35 minutes, schedule dinner with '`Maximum length`: 35 minutes'.

## Syncing with other programs
* When there is free time on the schedule, pull reminders from Apple Reminders or Microsoft Todo, and schedule them during the free time. If completed successfully, complete the reminder. Reminders are pulled from these apps according to a priority list (e.g. pull reminders from Programming first, School second, and Shopping never).
* If events could be added to a calendar account (such as a Microsoft account), the calendar app could handle storing, displaying, and notifying the user of events. This would also allow it to sync between devices.

## Notifications
* Notify the user when a task is scheduled. The user can add their own notification times (e.g. 1 h before event).
* Customizable notification preferences (e.g. silence notifications for start/end of different school periods).

## Miscellaneous
* Mark event as complete (stop it early and record to event log)
	* Events should be marked as complete automatically when `Can schedule until` or `End condition` is encountered.
* Event Priorities
	* Each event can be assigned a priority (number). Activities with a higher priority are prioritized over another event when a scheduling conflict occurs.
* Event Dependencies
	* Schedule events according to some external condition (e.g. when they are visiting a specific location).