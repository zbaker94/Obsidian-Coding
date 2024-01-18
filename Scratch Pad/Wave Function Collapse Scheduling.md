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
		
- Shifts
	- Arbitrary time span on a specific day
	- optional weighted list of skills needed for a specific shift
		- skills could have relationships (if an employee is performing x skill, they cannot perform y skill on the same shift)
	- Number of employees needed for shift

## Process
1. Fill each shift with every employee that is available for the shift and rank their desirability on that shift (skills, rules, availability weight, etc.)
2. for the length of the list of shifts
	1. Choose a random shift 
		1. if list of possible employees is < needed for the shift 
			1. roll back or start over
		2. else
			1. choose highest ranked employee for shift
			2. If the shift is not full
				1. filter any employees whose rules would be violated
				2. goto 1
	2. update every other shifts list of employees to meet rules
	3. if any shifts list of possible employees is < needed for the shift 
		1. roll back or start over
	4. else if all shifts are full
		1. end
	5. else
		1. goto 1

*It is possible to do multiple iterations of this to generate possible schedules. simply starting at a different random shift could yield significantly different results. Other options could be to explore relaxing or ignoring certain rules if they result in a schedule that works. Maybe just store invalid schedules as possible options. Could also generate a schedule that only accounts for availability and skills as a "starting point"*

*In practice this would shake out as several options for the user to work from. Ideally we could generate a schedule that requires zero modification from the user but will provide the ability to arbitrarily change any schedule as they see fit. A QL feature here would be useful if we show the user invalid generated schedules as well: notify the user that a schedule violates one or more rules (but do not prevent them from using it).*