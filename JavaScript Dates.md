---
creation date: 2021-11-03 10:36
aliases: 
tags: 
---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md) 
References: [Common Gotchas](https://stackoverflow.com/questions/15141762/how-to-initialize-a-javascript-date-to-a-particular-time-zone), [Why your date is off by 1 day](https://dev.to/zachgoll/a-complete-guide-to-javascript-dates-and-why-your-date-is-off-by-1-day-fi1)
# Basics
- UTC is a reference point for timezones (+/- 0)
- Unix Epoch is also a reference point for a computer date: Jan 1, 1970 midnight

# JavaScript Dates
`Date` objects contain a `number` that represents milliseconds since January 1, 1970 UTC. 

Three ways to represent a date in Javascript:
```js
const myDate = new Date('2020-09-30'); // JavaScript Date object

// Primitive value (number)
myDate.valueOf() // 1601424000000

// ISO 8601 (string)
myDate.toISOString() // "2020-09-30T00:00:00.000Z"

// ‼️ JavaScript Standard Format (string) is relative to computer's timezone!!! 
myDate.toString() // "Tue Sep 29 2020 20:00:00 GMT-0400 (Eastern Daylight Time)"
```

ISO 1601 is a standard (non-Javascript specific) for formatting dates and times
![Pasted image 20230710154835.png](./images/Pasted%20image%2020230710154835.png)
- `Z` indicates timezone, which is "zulu" or UTC

> [!HINT]
> Every time you use `toISOString()` on a JavaScript Date object, you will **ALWAYS** get a UTC representation of the date. JavaScript internally stores dates in UTC, but the `toString()` method prints it as a date string in *timezone relative to the computer*.

Checklist to debug off-by-1 date
- **What is the input value and did I specify a timezone?**
	- Default dates are in UTC if no timezone specific
- **What is UTC value of the date?**
	- check `date.ToISOString()`
- **What JavaScript method is being used to display the date?**
	- make sure you are using `date.getUTC[date/month/year]()` methods!! 

Sanity check:
```js
let tomorrow = new Date('2023-07-11')
// 2023-07-11T00:00:00.000Z

tomorrow.getDate()
10 // instead of 11th because I am in PDT timezone, 7 hours behind.
tomorrow.toString()
'Mon Jul 10 2023 17:00:00 GMT-0700 (Pacific Daylight Time)'

// stay in UTC
tomorrow.getUTCDate()
11 // consistent!


```

