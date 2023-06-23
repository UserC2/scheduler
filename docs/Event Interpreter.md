# Event Interpreter
* See `Event Format.md` for information on how events are stored.

## Function
* Interpret events from a file.
* Interpret event conditions from a string.
* Output a list of machine-readable events.

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

## Operation - Condition Interpreter
### Example `RawEvent`:
```dart
RawEvent(
	name: 'Robotics Weekday Meeting',
	startCondition: 'AFTER "school day" + \'10 minutes\'',
	canScheduleUntil: '\'10:00 PM\'',
	minimumLength: '\'30 minutes\'',
	repeatCondition: 'ON Tuesday AND ON Thursday',
	repeatStart: 'BEFORE "school year"',
	repeatEnd: 'BEFORE "robotics world championships"',
)
```

### Interpreter
* **See `App Proposal.md` for Special Values, Operators, and Date & Time formatting.**
* Returns three arrays:
	* `invalidEvents`, array of `rawEvent`
	* `staticEvents`, array of `InterpretedEvent`
	* `dependentEvents`, array of `InterpretedEvent`
* For each `rawEvent` in `rawEvents`:
	* Create a boolean variable `isDependent` = false.
	* Call `rawEvent.isValidEvent()`.
	* If `rawEvent.isValidEvent()` returns False:
		* Continue to next `rawEvent`.
	* Create an `InterpretedEvent` with the `rawEvent` using `InterpretedEvent`'s constructor.
	* For each property in `rawEvent` other than `name`, `type`, and `isTransparent`:
		* Run the Property Divider on the value of the property.
		* If the returned `propertyWordTypes` contains one or more `PropertyWordType.event`:
			* Set `isDependent` = true.
			* **The `propertyWordTypes` and `propertyWords` arrays should be stored to avoid re-computing them later.**
			* Move onto the next property.
* If a `InvalidPropertyValue` exception was raised, save the invalid `rawEvent` to the `invalidEvents` array.
* If `isDependent` is false, save the `InterpretedEvent` to the `staticEvents` array.
* If `isDependent` is true, save the `InterpretedEvent` to the `dependentEvents` array.

### Property Divider
* This enum describes the different types that an operator/operand can be within a property:
```dart
enum PropertyWordType {
	boolean, repeatCondition, specialEvent, specialDate, datetime, event, logicalOperator, mathematicalOperator, schedulingOperator
}
```
* Provided with a string `property_value`.
* Returns an array `propertyWordTypes` of PropertyWordType representing the sequence of operators/operands in the provided `property_value`, and a second array `propertyWords` of String containing the divided property_value. Elements in the second array are arranged according to the first array (for example, if `propertyWordTypes[0]` is `event`, then `propertyWords[0]` will be the name of an event).

* Repeat until end of property_value is reached:
	* Get text until ` ` or `"` or `'` or end of `property_value` is reached.
	* If a `"` is encountered:
		* Get text until `"` or end of `property_value`.
			* If the end of `property_value` is encountered, raise an `InvalidPropertyValue` exception.
			* If a `"` is encountered, push back `event` to `propertyWordTypes` and push back the string between both `"` to `propertyWords`.
	* If a `'` is encountered:
		* Get text until `'` or end of `property_value`.
			* If the end of `property_value` is encountered, raise an `InvalidPropertyValue` exception.
			* If a `'` is encountered:
				* Push back `datetime` to `propertyWordTypes`.
				* Push back the string between each `'` to `propertyWords`.
	* If text matches an operator:
		* Push back either `logicalOperator`, `mathematicalOperator`, or `schedulingOperator` (depending on the type of operator) to `propertyWordTypes`.
		* Push back the element of the enum that the text was matched with to `propertyWords`.
	* If text matches a special value:
		* Push back either `boolean`, `repeatCondition`, `specialEvent`, or `specialDate` (depending on the type of special value) to `propertyWordTypes`.
		* Push back the element of the enum that the text was matched with to `propertyWords`.
	* Otherwise, raise an `InvalidPropertyValue` exception.

### Property Evaluator
* Given the `propertyWordTypes` and `propertyWords` arrays from Property Divider.
* Assert that there are no elements equal to `event` in `propertyWordTypes`.
	* This should not occur, as the caller of this function should have converted each reference to an event into a date. (For example, if `BEFORE "Father's Day 2023"` was used as a condition, it should have been converted into `18/06/2023`.) 
* Apply each operator in `propertyWordTypes`.
* Return the resulting date/time.

## Operation - Overall
* Start file interpreter.
	* Give `rawEvents` array to condition interpreter.
* Start condition interpreter.
	* Display `invalidEvents` array to the user.
	* Give the `staticEvents` and `dependentEvents` arrays to the scheduling algorithm.