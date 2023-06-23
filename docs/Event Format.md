# Event Format
* This file describes how events are stored within the Scheduler app.

## Text Files
* Events are stored in text files.
```
Property1: E1-Property1-Value
Property2: E1-Property2-Value

Property1: E2-Property1-Value
Property2: E2-Property2-Value
```
* `\n\n` seperates events (each event is seperated by a blank line).
* `\n` seperates properties (each property is on a different line).
* The property name is specified between `\n` (the start of the line) and `:`.
* The property value is specified between `:` and `\n` (the end of the line).

## During Interpretation
* Events are stored in a Dart class:
```dart
class RawEvent {
	String? name;
	String? type;
	bool? isTransparent;
	String? interrupts;
	String? minLength;
	String? maxLength;
	String? startCondition;
	String? endCondition;
	String? canScheduleFrom;
	String? canScheduleUntil;
	String? repeatCondition;
	String? repeatStartCondition;
	String? repeatEndCondition;

	bool isValidEvent() {
		return (name != null) && (type != null)
			&& ((startCondition != null) || (canScheduleFrom != null))
			&& ((endCondition != null) || (canScheduleUntil != null) || (maxLength != null));
	}
}
```