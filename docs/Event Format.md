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
* Note that the `?` after a type name indicates the value *can be* null, and will be initialized to `null` if an initializer is not specified. Any variables not marked with `?` are not allowed to be `null`.
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

class InterpretedEvent {
	String name;
	String type;
	bool isTransparent;
	String interrupts;
	String? minLength;
	String? maxLength;
	String? startCondition;
	String? endCondition;
	String? canScheduleFrom;
	String? canScheduleUntil;
	String? repeatCondition;
	String repeatStartCondition;
	String repeatEndCondition;

	// assign default values to properties if null
	InterpretedEvent(RawEvent rawEvent)
		: name = rawEvent.name ?? ''
		, type = rawEvent.type ?? name
		, isTransparent = checkTransparency(rawEvent.isTransparent)
		, interrupts = 'Interruptible'
		, repeatStartCondition = "${current_date_time}"
		, repeatEndCondition = false; // forever
	
	// match either true or false to string, returning false if invalid or null.
	static bool checkTransparency(String? isTransparent) {
		isTransparent
			?..trim()
			..toLowerCase();
		switch (isTransparent) {
			case 'true':
				return true;
			case 'false':
				return false;
			default:
				return false;
		}
	}
}
```