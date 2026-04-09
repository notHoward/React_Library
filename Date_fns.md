# date-fns - Complete Beginner's Guide

A comprehensive from-scratch guide to date-fns date manipulation library.

## Table of Contents

1. [What is date-fns](#what-is-date-fns)
2. [Installation](#installation)
3. [Basic Setup](#basic-setup)
4. [Formatting Dates](#formatting-dates)
5. [Parsing Dates](#parsing-dates)
6. [Adding and Subtracting Time](#adding-and-subtracting-time)
7. [Comparing Dates](#comparing-dates)
8. [Getting Date Parts](#getting-date-parts)
9. [Difference Between Dates](#difference-between-dates)
10. [Date Validation](#date-validation)
11. [Manipulating Dates](#manipulating-dates)
12. [Time Zones](#time-zones)
13. [Relative Time](#relative-time)
14. [Locale Support](#locale-support)
15. [Complete Examples](#complete-examples)
16. [API Reference](#api-reference)

---

## What is date-fns

date-fns is a modern JavaScript date utility library that provides the most comprehensive, yet simple and consistent toolset for manipulating JavaScript dates.

**Why date-fns instead of Moment.js:**

| Feature             | date-fns                          | Moment.js              |
| ------------------- | --------------------------------- | ---------------------- |
| Bundle size         | Tiny (modular imports)            | Large (full library)   |
| Immutability        | Pure functions (returns new date) | Mutates original       |
| Tree shaking        | Yes                               | No                     |
| Native Date objects | Uses standard Date                | Wraps in Moment object |

**Key Benefits:**

- **Modular** - Import only what you need
- **Pure functions** - Never mutates original dates
- **TypeScript support** - Full type definitions
- **Locale aware** - Supports 100+ languages

---

## Installation

### Basic Installation

```bash
npm install date-fns
```

### For TypeScript Projects

```bash
npm install date-fns
# Types are included in the main package
```

### Import Styles

```javascript
// Method 1: Import specific functions (RECOMMENDED - smallest bundle)
import { format, addDays, subDays } from "date-fns";

// Method 2: Import from index (imports everything - larger bundle)
import * as dateFns from "date-fns";

// Method 3: Import from specific paths (most explicit)
import format from "date-fns/format";
import addDays from "date-fns/addDays";
```

---

## Basic Setup

### Working with JavaScript Dates

```javascript
import { format, addDays, differenceInDays } from "date-fns";

// Create dates
const now = new Date(); // Current date/time
const specificDate = new Date(2024, 0, 15); // Jan 15, 2024
const fromString = new Date("2024-01-15");

// Format a date
const formatted = format(now, "yyyy-MM-dd");
console.log(formatted); // "2024-01-15"

// Add days to a date
const tomorrow = addDays(now, 1);

// Calculate difference
const daysBetween = differenceInDays(date1, date2);
```

---

## Formatting Dates

### Basic Formatting

```javascript
import { format } from "date-fns";

const date = new Date(2024, 0, 15, 14, 30, 45); // Jan 15, 2024 14:30:45

// Year formats
format(date, "yyyy"); // "2024" (4-digit year)
format(date, "yy"); // "24" (2-digit year)

// Month formats
format(date, "MMMM"); // "January" (full month name)
format(date, "MMM"); // "Jan" (abbreviated month)
format(date, "MM"); // "01" (2-digit month)
format(date, "M"); // "1" (1-digit month)

// Day formats
format(date, "EEEE"); // "Monday" (full day name)
format(date, "EEE"); // "Mon" (abbreviated day)
format(date, "dd"); // "15" (2-digit day)
format(date, "d"); // "15" (1-digit day)

// Hour formats
format(date, "HH"); // "14" (24-hour, 2-digit)
format(date, "H"); // "14" (24-hour, 1-digit)
format(date, "hh"); // "02" (12-hour, 2-digit)
format(date, "h"); // "2" (12-hour, 1-digit)

// Minute and Second formats
format(date, "mm"); // "30" (2-digit minute)
format(date, "ss"); // "45" (2-digit second)

// AM/PM
format(date, "aa"); // "PM" (am/pm)
format(date, "aaa"); // "pm" (lowercase)
format(date, "a"); // "p" (abbreviated)

// Timezone offset
format(date, "xx"); // "+0530" (offset)
format(date, "xxx"); // "+05:30" (offset with colon)

// Literal characters (escape with brackets)
format(date, "'Today is' EEEE"); // "Today is Monday"
```

### Common Date Formats

```javascript
import { format } from "date-fns";

const date = new Date();

// Common US format
format(date, "MM/dd/yyyy"); // "01/15/2024"
format(date, "MMMM dd, yyyy"); // "January 15, 2024"
format(date, "MMM dd, yyyy"); // "Jan 15, 2024"

// Common European format
format(date, "dd/MM/yyyy"); // "15/01/2024"
format(date, "dd.MM.yyyy"); // "15.01.2024"

// ISO format
format(date, "yyyy-MM-dd'T'HH:mm:ss.SSSxxx"); // "2024-01-15T14:30:45.123+05:30"

// Time only
format(date, "HH:mm:ss"); // "14:30:45"
format(date, "hh:mm:ss a"); // "02:30:45 PM"

// Date and time
format(date, "yyyy-MM-dd HH:mm:ss"); // "2024-01-15 14:30:45"
format(date, "MMMM do, yyyy h:mm a"); // "January 15th, 2024 2:30 PM"

// With ordinal
format(date, "do"); // "15th" (ordinal day of month)
```

### Formatting with Multiple Languages

```javascript
import { format } from "date-fns";
import { es, fr, de, zhCN } from "date-fns/locale";

const date = new Date(2024, 0, 15);

// English (default)
format(date, "EEEE", { locale: null }); // "Monday"

// Spanish
format(date, "EEEE", { locale: es }); // "lunes"

// French
format(date, "EEEE", { locale: fr }); // "lundi"

// German
format(date, "EEEE", { locale: de }); // "Montag"

// Chinese
format(date, "EEEE", { locale: zhCN }); // "星期一"
```

---

## Parsing Dates

### Parsing Strings to Dates

```javascript
import { parse } from "date-fns";

// Parse with specific format
const date1 = parse("15/01/2024", "dd/MM/yyyy", new Date());
// Result: Jan 15, 2024

const date2 = parse("January 15, 2024", "MMMM dd, yyyy", new Date());

const date3 = parse("2024-01-15 14:30:45", "yyyy-MM-dd HH:mm:ss", new Date());

// With timezone
const date4 = parse(
  "2024-01-15T14:30:45+05:30",
  "yyyy-MM-dd'T'HH:mm:ssxxx",
  new Date(),
);
```

### Parsing ISO Strings

```javascript
import { parseISO } from "date-fns";

// ISO 8601 format
const date1 = parseISO("2024-01-15");
const date2 = parseISO("2024-01-15T14:30:45");
const date3 = parseISO("2024-01-15T14:30:45+05:30");

// Invalid returns Invalid Date
const invalid = parseISO("not a date"); // Returns Invalid Date
```

### Safe Parsing

```javascript
import { parse, isValid } from "date-fns";

function safeParse(dateString, formatString) {
  const parsed = parse(dateString, formatString, new Date());
  return isValid(parsed) ? parsed : null;
}

const validDate = safeParse("15/01/2024", "dd/MM/yyyy"); // Returns Date object
const invalidDate = safeParse("invalid", "dd/MM/yyyy"); // Returns null
```

---

## Adding and Subtracting Time

### Adding Time

```javascript
import {
  addDays,
  addWeeks,
  addMonths,
  addYears,
  addHours,
  addMinutes,
  addSeconds,
  addMilliseconds,
} from "date-fns";

const date = new Date(2024, 0, 15); // Jan 15, 2024

// Date units
addDays(date, 5); // Jan 20, 2024
addWeeks(date, 2); // Jan 29, 2024
addMonths(date, 1); // Feb 15, 2024
addYears(date, 1); // Jan 15, 2025

// Time units
const timeDate = new Date(2024, 0, 15, 10, 0, 0);
addHours(timeDate, 3); // 1:00 PM
addMinutes(timeDate, 30); // 10:30 AM
addSeconds(timeDate, 45); // 10:00:45 AM
addMilliseconds(timeDate, 500); // 10:00:00.500 AM

// Multiple units
const newDate = addDays(addMonths(date, 1), 5);
```

### Subtracting Time

```javascript
import {
  subDays,
  subWeeks,
  subMonths,
  subYears,
  subHours,
  subMinutes,
  subSeconds,
} from "date-fns";

const date = new Date(2024, 0, 20); // Jan 20, 2024

// Date units
subDays(date, 5); // Jan 15, 2024
subWeeks(date, 2); // Jan 6, 2024
subMonths(date, 1); // Dec 20, 2023
subYears(date, 1); // Jan 20, 2023

// Time units
const timeDate = new Date(2024, 0, 15, 10, 30, 0);
subHours(timeDate, 3); // 7:30 AM
subMinutes(timeDate, 30); // 10:00 AM
subSeconds(timeDate, 30); // 10:29:30 AM
```

---

## Comparing Dates

### Basic Comparisons

```javascript
import {
  isBefore,
  isAfter,
  isEqual,
  isSameDay,
  isSameMonth,
  isSameYear,
  isWithinInterval,
  isWeekend,
  isWeekday,
} from "date-fns";

const date1 = new Date(2024, 0, 15);
const date2 = new Date(2024, 0, 20);

// Comparison
isBefore(date1, date2); // true (Jan 15 is before Jan 20)
isAfter(date1, date2); // false
isEqual(date1, date1); // true

// Same unit checks
isSameDay(date1, date2); // false (different days)
isSameMonth(date1, date2); // true (both January)
isSameYear(date1, date2); // true (both 2024)

// Date ranges
isWithinInterval(date1, {
  start: new Date(2024, 0, 10),
  end: new Date(2024, 0, 20),
}); // true

// Day checks
const weekend = new Date(2024, 0, 20); // Saturday
isWeekend(weekend); // true
isWeekday(weekend); // false
```

### Advanced Comparisons

```javascript
import {
  isToday,
  isYesterday,
  isTomorrow,
  isPast,
  isFuture,
  isThisYear,
  isThisMonth,
  isFirstDayOfMonth,
  isLastDayOfMonth,
} from "date-fns";

const today = new Date();
const yesterday = new Date(today);
yesterday.setDate(yesterday.getDate() - 1);

// Day relative
isToday(today); // true
isYesterday(yesterday); // true
isTomorrow(today); // false

// Past/Future
isPast(yesterday); // true
isFuture(tomorrow); // true

// Year/Month checks
isThisYear(today); // true
isThisMonth(today); // true

// Month boundaries
const firstDay = new Date(2024, 0, 1);
const lastDay = new Date(2024, 0, 31);

isFirstDayOfMonth(firstDay); // true
isLastDayOfMonth(lastDay); // true
```

---

## Getting Date Parts

### Basic Date Parts

```javascript
import {
  getYear,
  getMonth,
  getDate,
  getDay,
  getHours,
  getMinutes,
  getSeconds,
  getMilliseconds,
} from "date-fns";

const date = new Date(2024, 0, 15, 14, 30, 45, 123);

// Date parts
getYear(date); // 2024
getMonth(date); // 0 (January - zero indexed!)
getDate(date); // 15 (day of month)
getDay(date); // 1 (Monday - 0 = Sunday, 1 = Monday, etc.)

// Time parts
getHours(date); // 14
getMinutes(date); // 30
getSeconds(date); // 45
getMilliseconds(date); // 123

// Note: getMonth() returns 0-11, so add 1 for human-readable
const humanMonth = getMonth(date) + 1; // 1
```

### Week Information

```javascript
import {
  getWeek,
  getISOWeek,
  getWeekYear,
  getDayOfYear,
  getDaysInMonth,
  getDaysInYear,
} from "date-fns";

const date = new Date(2024, 0, 15);

// Week numbers
getWeek(date); // Week number (locale aware)
getISOWeek(date); // ISO week number (Monday-Sunday)
getWeekYear(date); // Week-numbering year

// Day of year
getDayOfYear(date); // 15 (Jan 15 is 15th day of year)

// Days in month/year
getDaysInMonth(date); // 31 (days in January)
getDaysInYear(date); // 366 (2024 is leap year)
```

### Quarter and Decade

```javascript
import { getQuarter, getDecade } from "date-fns";

const date = new Date(2024, 5, 15); // June 15, 2024

getQuarter(date); // 2 (April-June is Q2)
getDecade(date); // 2020 (start of decade)
```

---

## Difference Between Dates

### Basic Differences

```javascript
import {
  differenceInDays,
  differenceInWeeks,
  differenceInMonths,
  differenceInYears,
  differenceInHours,
  differenceInMinutes,
  differenceInSeconds,
  differenceInMilliseconds,
} from "date-fns";

const start = new Date(2024, 0, 1);
const end = new Date(2024, 0, 15);

// Date differences
differenceInDays(end, start); // 14
differenceInWeeks(end, start); // 2
differenceInMonths(end, start); // 0
differenceInYears(end, start); // 0

// Time differences
const timeStart = new Date(2024, 0, 1, 10, 0, 0);
const timeEnd = new Date(2024, 0, 1, 14, 30, 30);

differenceInHours(timeEnd, timeStart); // 4
differenceInMinutes(timeEnd, timeStart); // 270
differenceInSeconds(timeEnd, timeStart); // 16230
differenceInMilliseconds(timeEnd, timeStart); // 16230000
```

### Advanced Differences

```javascript
import {
  differenceInCalendarDays,
  differenceInCalendarMonths,
  differenceInCalendarWeeks,
  differenceInCalendarYears,
  differenceInBusinessDays,
} from "date-fns";

const start = new Date(2024, 0, 1);
const end = new Date(2024, 0, 15);

// Calendar differences (ignores time of day)
differenceInCalendarDays(end, start); // 14
differenceInCalendarWeeks(end, start); // 2
differenceInCalendarMonths(end, start); // 0
differenceInCalendarYears(end, start); // 0

// Business days (Monday-Friday only)
const startWeek = new Date(2024, 0, 1); // Monday
const endWeek = new Date(2024, 0, 5); // Friday
differenceInBusinessDays(endWeek, startWeek); // 4 (skips weekend)
```

### Calculating Age

```javascript
import {
  differenceInYears,
  differenceInMonths,
  differenceInDays,
} from "date-fns";

function calculateAge(birthDate) {
  const today = new Date();
  const years = differenceInYears(today, birthDate);
  const months = differenceInMonths(today, birthDate) % 12;
  const days = differenceInDays(today, birthDate) % 30;

  return { years, months, days };
}

const birthDate = new Date(1990, 5, 15); // June 15, 1990
const age = calculateAge(birthDate);
console.log(`${age.years} years, ${age.months} months, ${age.days} days`);
```

---

## Date Validation

### Checking Validity

```javascript
import { isValid, isDate } from "date-fns";

// Check if Date object is valid
const validDate = new Date(2024, 0, 15);
isValid(validDate); // true

const invalidDate = new Date("invalid");
isValid(invalidDate); // false

// Check if value is a Date object
isDate(validDate); // true
isDate("2024-01-15"); // false
isDate(123); // false
```

### Range Validation

```javascript
import { isWithinInterval, isAfter, isBefore } from "date-fns";

function isDateInRange(date, startDate, endDate) {
  return isWithinInterval(date, { start: startDate, end: endDate });
}

function isFutureDate(date) {
  return isAfter(date, new Date());
}

function isPastDate(date) {
  return isBefore(date, new Date());
}

// Example
const date = new Date(2024, 6, 15);
const start = new Date(2024, 0, 1);
const end = new Date(2024, 11, 31);

isDateInRange(date, start, end); // true
isFutureDate(date); // depends on current date
isPastDate(date); // depends on current date
```

---

## Manipulating Dates

### Setting Date Parts

```javascript
import {
  setYear,
  setMonth,
  setDate,
  setDay,
  setHours,
  setMinutes,
  setSeconds,
  setMilliseconds,
} from "date-fns";

const date = new Date(2024, 0, 15, 10, 30, 45);

// Set date parts
setYear(date, 2025); // 2025-01-15
setMonth(date, 5); // 2024-06-15 (June - 5 = June!)
setDate(date, 20); // 2024-01-20
setDay(date, 1); // Set to Monday of same week

// Set time parts
setHours(date, 14); // 2:30:45 PM
setMinutes(date, 0); // 10:00:45
setSeconds(date, 0); // 10:30:00
setMilliseconds(date, 500); // 10:30:45.500
```

### Start and End of Time Units

```javascript
import {
  startOfDay,
  endOfDay,
  startOfWeek,
  endOfWeek,
  startOfMonth,
  endOfMonth,
  startOfYear,
  endOfYear,
} from "date-fns";

const date = new Date(2024, 0, 15, 14, 30, 45);

// Start of units
startOfDay(date); // 2024-01-15 00:00:00
startOfWeek(date); // 2024-01-14 (Sunday or Monday based on locale)
startOfMonth(date); // 2024-01-01 00:00:00
startOfYear(date); // 2024-01-01 00:00:00

// End of units
endOfDay(date); // 2024-01-15 23:59:59.999
endOfWeek(date); // 2024-01-20 23:59:59.999
endOfMonth(date); // 2024-01-31 23:59:59.999
endOfYear(date); // 2024-12-31 23:59:59.999
```

### Rounding Dates

```javascript
import { roundToNearestMinutes } from "date-fns";

const date = new Date(2024, 0, 15, 14, 23, 45);

// Round to nearest 5 minutes
roundToNearestMinutes(date, { nearestTo: 5 }); // 14:25:00

// Round to nearest 15 minutes
roundToNearestMinutes(date, { nearestTo: 15 }); // 14:30:00

// Round to nearest hour
roundToNearestMinutes(date, { nearestTo: 60 }); // 14:00:00
```

---

## Time Zones

### Working with UTC

```javascript
import { utcToZonedTime, zonedTimeToUtc, format } from "date-fns-tz";

// Install: npm install date-fns-tz

// Convert UTC to specific timezone
const utcDate = new Date("2024-01-15T14:30:00Z");
const nyDate = utcToZonedTime(utcDate, "America/New_York");
const tokyoDate = utcToZonedTime(utcDate, "Asia/Tokyo");

// Format in timezone
format(nyDate, "yyyy-MM-dd HH:mm:ss", { timeZone: "America/New_York" });

// Convert local time to UTC
const localDate = new Date(2024, 0, 15, 14, 30);
const utcDateFromLocal = zonedTimeToUtc(localDate, "America/New_York");
```

### Getting Timezone Information

```javascript
import { getTimezoneOffset } from "date-fns-tz";

const date = new Date();
const offset = getTimezoneOffset("America/New_York", date);
console.log(offset); // Offset in milliseconds
```

---

## Relative Time

### Format Distance (Human-readable)

```javascript
import {
  formatDistance,
  formatDistanceStrict,
  formatDistanceToNow,
} from "date-fns";

const date = new Date(2024, 0, 15);
const now = new Date(2024, 0, 20);

// Approximate distance
formatDistance(date, now); // "5 days"
formatDistance(date, now, { addSuffix: true }); // "5 days ago"

// Strict distance (no approximation)
formatDistanceStrict(date, now); // "5 days"

// Distance to now
formatDistanceToNow(date); // "5 days ago"
formatDistanceToNow(date, { addSuffix: false }); // "5 days"
formatDistanceToNow(new Date(Date.now() + 5000)); // "less than 5 seconds"
```

### Format Distance with Options

```javascript
import { formatDistance } from "date-fns";

const start = new Date(2024, 0, 15, 10, 0);
const end = new Date(2024, 0, 15, 14, 30);

// Different units
formatDistance(start, end, { unit: "hour" }); // "4 hours"
formatDistance(start, end, { unit: "minute" }); // "270 minutes"

// Include seconds
formatDistance(start, end, { includeSeconds: true }); // "4 hours"

// Rounding
formatDistance(start, end, { roundingMethod: "floor" }); // "4 hours"
formatDistance(start, end, { roundingMethod: "ceil" }); // "5 hours"
formatDistance(start, end, { roundingMethod: "round" }); // "5 hours"
```

---

## Locale Support

### Loading Locales

```javascript
import { format, formatDistance } from "date-fns";
import { es, fr, de, ja, zhCN, ar, ru } from "date-fns/locale";

const date = new Date(2024, 0, 15);

// Format with locale
format(date, "EEEE", { locale: es }); // "lunes"
format(date, "EEEE", { locale: fr }); // "lundi"
format(date, "EEEE", { locale: de }); // "Montag"
format(date, "EEEE", { locale: ja }); // "月曜日"
format(date, "EEEE", { locale: zhCN }); // "星期一"
format(date, "EEEE", { locale: ar }); // "الاثنين"
format(date, "EEEE", { locale: ru }); // "понедельник"
```

### Format Distance with Locale

```javascript
import { formatDistanceToNow } from "date-fns";
import { es, fr, de } from "date-fns/locale";

const pastDate = new Date(2024, 0, 10);
const now = new Date(2024, 0, 15);

formatDistanceToNow(pastDate, { locale: es }); // "hace 5 días"
formatDistanceToNow(pastDate, { locale: fr }); // "il y a 5 jours"
formatDistanceToNow(pastDate, { locale: de }); // "vor 5 Tagen"
```

### Creating Custom Locale

```javascript
import { format } from "date-fns";

const customLocale = {
  options: { weekStartsOn: 1 },
  localize: {
    day: (n) => ["Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"][n],
    month: (n) =>
      [
        "Jan",
        "Feb",
        "Mar",
        "Apr",
        "May",
        "Jun",
        "Jul",
        "Aug",
        "Sep",
        "Oct",
        "Nov",
        "Dec",
      ][n],
  },
  formatLong: {
    date: () => "MM/dd/yyyy",
  },
};

format(new Date(), "EEEE", { locale: customLocale });
```

---

## Complete Examples

### Example 1: Date Picker Component

```jsx
import { useState } from "react";
import {
  format,
  addMonths,
  subMonths,
  startOfMonth,
  endOfMonth,
  startOfWeek,
  endOfWeek,
  eachDayOfInterval,
  isSameMonth,
  isSameDay,
  isToday,
  isWeekend,
} from "date-fns";

function DatePicker({ selectedDate, onSelect }) {
  const [currentMonth, setCurrentMonth] = useState(selectedDate || new Date());

  const monthStart = startOfMonth(currentMonth);
  const monthEnd = endOfMonth(currentMonth);
  const startDate = startOfWeek(monthStart);
  const endDate = endOfWeek(monthEnd);

  const days = eachDayOfInterval({ start: startDate, end: endDate });

  const prevMonth = () => setCurrentMonth(subMonths(currentMonth, 1));
  const nextMonth = () => setCurrentMonth(addMonths(currentMonth, 1));

  return (
    <div className="date-picker">
      <div className="header">
        <button onClick={prevMonth}>←</button>
        <h2>{format(currentMonth, "MMMM yyyy")}</h2>
        <button onClick={nextMonth}>→</button>
      </div>

      <div className="weekdays">
        {["Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"].map((day) => (
          <div key={day}>{day}</div>
        ))}
      </div>

      <div className="days">
        {days.map((day) => (
          <button
            key={day.toString()}
            className={`
              day
              ${!isSameMonth(day, currentMonth) ? "other-month" : ""}
              ${isToday(day) ? "today" : ""}
              ${selectedDate && isSameDay(day, selectedDate) ? "selected" : ""}
              ${isWeekend(day) ? "weekend" : ""}
            `}
            onClick={() => onSelect(day)}
          >
            {format(day, "d")}
          </button>
        ))}
      </div>
    </div>
  );
}
```

### Example 2: Countdown Timer

```jsx
import { useState, useEffect } from "react";
import {
  differenceInSeconds,
  differenceInMinutes,
  differenceInHours,
  differenceInDays,
} from "date-fns";

function CountdownTimer({ targetDate }) {
  const [timeLeft, setTimeLeft] = useState({});

  useEffect(() => {
    const timer = setInterval(() => {
      const now = new Date();

      setTimeLeft({
        days: differenceInDays(targetDate, now),
        hours: differenceInHours(targetDate, now) % 24,
        minutes: differenceInMinutes(targetDate, now) % 60,
        seconds: differenceInSeconds(targetDate, now) % 60,
      });
    }, 1000);

    return () => clearInterval(timer);
  }, [targetDate]);

  const isExpired =
    timeLeft.days < 0 ||
    timeLeft.hours < 0 ||
    timeLeft.minutes < 0 ||
    timeLeft.seconds < 0;

  if (isExpired) {
    return <div>Time's up!</div>;
  }

  return (
    <div className="countdown">
      <div>{timeLeft.days} days</div>
      <div>{timeLeft.hours} hours</div>
      <div>{timeLeft.minutes} minutes</div>
      <div>{timeLeft.seconds} seconds</div>
    </div>
  );
}
```

### Example 3: Calendar Component

```jsx
import { useState } from "react";
import {
  format,
  startOfMonth,
  endOfMonth,
  eachDayOfInterval,
  isSameMonth,
  isSameDay,
  addMonths,
  subMonths,
  getDate,
  getDay,
  getDaysInMonth,
} from "date-fns";

function Calendar({ events = [], onEventClick }) {
  const [currentDate, setCurrentDate] = useState(new Date());

  const monthStart = startOfMonth(currentDate);
  const monthEnd = endOfMonth(currentDate);
  const daysInMonth = getDaysInMonth(currentDate);
  const startDay = getDay(monthStart);

  const days = [];

  // Add empty cells for days before month starts
  for (let i = 0; i < startDay; i++) {
    days.push(null);
  }

  // Add days of the month
  for (let i = 1; i <= daysInMonth; i++) {
    const date = new Date(currentDate.getFullYear(), currentDate.getMonth(), i);
    days.push(date);
  }

  const getEventsForDay = (date) => {
    return events.filter((event) => isSameDay(new Date(event.date), date));
  };

  return (
    <div className="calendar">
      <div className="calendar-header">
        <button onClick={() => setCurrentDate(subMonths(currentDate, 1))}>
          ←
        </button>
        <h2>{format(currentDate, "MMMM yyyy")}</h2>
        <button onClick={() => setCurrentDate(addMonths(currentDate, 1))}>
          →
        </button>
      </div>

      <div className="calendar-weekdays">
        {["Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"].map((day) => (
          <div key={day}>{day}</div>
        ))}
      </div>

      <div className="calendar-days">
        {days.map((day, index) => {
          const dayEvents = day ? getEventsForDay(day) : [];

          return (
            <div key={index} className={`calendar-day ${!day ? "empty" : ""}`}>
              {day && (
                <>
                  <span className="day-number">{getDate(day)}</span>
                  {dayEvents.map((event) => (
                    <div
                      key={event.id}
                      className="event"
                      onClick={() => onEventClick(event)}
                    >
                      {event.title}
                    </div>
                  ))}
                </>
              )}
            </div>
          );
        })}
      </div>
    </div>
  );
}
```

### Example 4: Date Range Picker

```jsx
import { useState } from "react";
import {
  format,
  addDays,
  subDays,
  eachDayOfInterval,
  isWithinInterval,
  isBefore,
  isAfter,
  isSameDay,
} from "date-fns";

function DateRangePicker({ onRangeChange }) {
  const [startDate, setStartDate] = useState(null);
  const [endDate, setEndDate] = useState(null);
  const [hoverDate, setHoverDate] = useState(null);

  const handleDateClick = (date) => {
    if (!startDate || (startDate && endDate)) {
      setStartDate(date);
      setEndDate(null);
    } else if (startDate && !endDate) {
      if (isBefore(date, startDate)) {
        setStartDate(date);
      } else {
        setEndDate(date);
        onRangeChange({ start: startDate, end: date });
      }
    }
  };

  const isInRange = (date) => {
    if (!startDate) return false;
    if (startDate && !endDate && hoverDate) {
      const start = isBefore(startDate, hoverDate) ? startDate : hoverDate;
      const end = isBefore(startDate, hoverDate) ? hoverDate : startDate;
      return isWithinInterval(date, { start, end });
    }
    if (startDate && endDate) {
      return isWithinInterval(date, { start: startDate, end: endDate });
    }
    return false;
  };

  const generateMonthDays = (year, month) => {
    const firstDay = new Date(year, month, 1);
    const lastDay = new Date(year, month + 1, 0);
    return eachDayOfInterval({ start: firstDay, end: lastDay });
  };

  return (
    <div className="date-range-picker">
      {/* Render calendar months here */}
      <div className="calendar-grid">
        {generateMonthDays(2024, 0).map((day) => (
          <button
            key={day.toString()}
            className={`
              date-cell
              ${isSameDay(day, startDate) ? "start-date" : ""}
              ${isSameDay(day, endDate) ? "end-date" : ""}
              ${isInRange(day) ? "in-range" : ""}
            `}
            onClick={() => handleDateClick(day)}
            onMouseEnter={() => setHoverDate(day)}
            onMouseLeave={() => setHoverDate(null)}
          >
            {format(day, "d")}
          </button>
        ))}
      </div>

      {startDate && endDate && (
        <div className="selected-range">
          {format(startDate, "MMM dd, yyyy")} -{" "}
          {format(endDate, "MMM dd, yyyy")}
        </div>
      )}
    </div>
  );
}
```

---

## API Reference

### Most Commonly Used Functions

| Category       | Function                                       | Description             |
| -------------- | ---------------------------------------------- | ----------------------- |
| **Format**     | `format()`                                     | Format a date string    |
|                | `parse()`                                      | Parse string to date    |
|                | `parseISO()`                                   | Parse ISO string        |
| **Add**        | `addDays()`, `addMonths()`, `addYears()`       | Add time to date        |
| **Subtract**   | `subDays()`, `subMonths()`, `subYears()`       | Subtract time from date |
| **Difference** | `differenceInDays()`, `differenceInMonths()`   | Calculate difference    |
| **Comparison** | `isBefore()`, `isAfter()`, `isEqual()`         | Compare dates           |
| **Getting**    | `getYear()`, `getMonth()`, `getDate()`         | Extract date parts      |
| **Setting**    | `setYear()`, `setMonth()`, `setDate()`         | Set date parts          |
| **Start/End**  | `startOfDay()`, `endOfDay()`, `startOfMonth()` | Get boundaries          |
| **Validation** | `isValid()`, `isDate()`                        | Validate dates          |
| **Relative**   | `formatDistance()`, `formatDistanceToNow()`    | Human readable          |

### Common Format Tokens

| Token  | Output              | Example |
| ------ | ------------------- | ------- |
| `yyyy` | 4-digit year        | 2024    |
| `yy`   | 2-digit year        | 24      |
| `MMMM` | Full month          | January |
| `MMM`  | Abbreviated month   | Jan     |
| `MM`   | 2-digit month       | 01      |
| `M`    | 1-digit month       | 1       |
| `dd`   | 2-digit day         | 15      |
| `d`    | 1-digit day         | 15      |
| `EEEE` | Full weekday        | Monday  |
| `EEE`  | Abbreviated weekday | Mon     |
| `HH`   | 24-hour (2-digit)   | 14      |
| `hh`   | 12-hour (2-digit)   | 02      |
| `mm`   | Minutes (2-digit)   | 30      |
| `ss`   | Seconds (2-digit)   | 45      |
| `aa`   | AM/PM               | PM      |
| `do`   | Ordinal day         | 15th    |

---

## Troubleshooting

**Problem 1:** Month is off by 1

**Solution:** Remember that `getMonth()` returns 0-11 (Jan = 0)

```javascript
const month = getMonth(date) + 1; // Add 1 for human-readable
```

**Problem 2:** Timezone issues

**Solution:** Use UTC functions or date-fns-tz for timezone handling

**Problem 3:** Invalid date errors

**Solution:** Always validate with `isValid()` before using parsed dates

**Problem 4:** Large bundle size

**Solution:** Import only specific functions, not the entire library

```javascript
// Bad
import * as dateFns from "date-fns";

// Good
import { format, addDays } from "date-fns";
```

---

## Useful Resources

- date-fns Documentation: https://date-fns.org/
- date-fns GitHub: https://github.com/date-fns/date-fns
- date-fns-tz (Timezones): https://github.com/date-fns/date-fns-tz

---
