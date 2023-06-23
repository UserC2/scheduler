# Event Interpreter
* See `Event Format.md` for information on how events are stored.

## Function
* Interpret events from a file.
* Interpret event conditions from a string.

## Operation - File Interpreter
### Example Event:
```
Name: Robotics Weekday Meeting
Start condition: AFTER "school day" + '10 minutes'
Can schedule until: '10:00 PM'
Minimum length: '30 minutes'
Repeat condition: ON Tuesday AND ON Thursday
Repeat start: BEFORE "school year"
Repeat end: BEFORE "robotics world championships"
```

### Interpreter
* Interpreter input is a file containing the schedule information.
* Repeat until End Of File is reached:
	* Get all input from the file until two newlines (`\n\n`) or End Of File.
		* Store into `textEvents` array (as string).
* For each `event` in `textEvents`:
	* Create `RawEvent` object named `rawEvent`.
		* Store into `rawEvents` array.
	* For each line in `event` string:
		* Get input until `:`, ignoring leading whitespace.
			* Store into `property` variable (as string).
		* Check `property` against enum of possible event properties as case-insensitive comparison.
		* If property does not exist:
			* Display "Invalid Property" error.
			* Continue to next event (skip remainder of current event).
		* If property exists:
			* Get input until end of line, ignoring leading whitespace.
				* Store into `property_value` string.
			* According to `property`, set the appropriate `property_value` for the current event.
				* For example: If `property` = "Name", set `rawEvent.name` = `property_value`.