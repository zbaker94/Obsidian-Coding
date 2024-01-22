### Data
- Employees - Basic data about an employee
	- Name
	- Skills
	- Availabilities
		- Array of days and times employee is available*
			- If we go with a shift-centric model we can just have employee availability be a list of which shifts on which days
			- possible weighting  based on employee preference
	- Type
		- arbitrary categories defined by user
- Rules - individual rules overrule type-wide rules which override global rules. rules can be weighted as well
	- One person must / must not work with certain other person
	- all/ Employee type / specific employee Must / Must not work X hours in a certain time span
	- all/ Employee type / specific employee Must / Must not work X consecutive days
	- all/ Employee type / specific employee may / may not work consecutive shifts

## Process
1. Figure out which days the desired span covers
2. Figure out the working hours for each day
3. Fill each day with every employee that is available for at least (AVAILABILITY THRESHOLD) of the day and rank their weighted desirability on that day (amount of day available, skills, rules, availability weight, etc.)
4. for the length of the list of days
	1. Choose a random day 
		1. if possible, detect invalid solution early
			1. if this is the first iteration 
				1. error out. No solution possible (could we detect this earlier?)
			2. else
				1. roll back or start over
		2. else
			1. choose highest ranked employee for day (random if tied)
			2. schedule employee for maximum possible time for day
			3. If the day is not fully covered by min employees at one time
				1. filter any employees whose rules would be violated or who are not available for the remainder of the day
				2. goto 1
	2. filter every other days list of employees to meet rules
	3. if possible, detect invalid solution early
		1. roll back or start over
	4. else if all days are fully covered
		1. end (success)
	5. else
		1. goto 1

*It is possible to do multiple iterations of this to generate different possible schedules. simply starting at a different random day could yield significantly different results. Other options could be to explore relaxing or ignoring certain rules if they result in a schedule that works. Maybe just store invalid schedules as possible options. Could also generate a schedule that only accounts for availability as a "starting point" for manual scheduling.*

*In practice this would shake out as several options for the user to work from. Ideally we could generate a schedule that requires zero modification from the user but should provide the ability to arbitrarily change any schedule as they see fit. A QL feature here would also be useful if we show the user invalid generated schedules: notify the user that a schedule violates one or more rules (but do not prevent them from using it).*