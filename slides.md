# Temporal 🕥

- Tim Austin
- Dev II
- Send-It

---

# Time is hard, why?

> Ref: Computerphile -- The Problem with Time & Timezones - <https://www.youtube.com/watch?v=-5wpm-gesOY>

* has both a local and a global reference
* timezones are numerous
* timezones are variable and changing

<!--
Ex: American Samoa

https://www.timetemperature.com/pacific/samoa_time_zone.shtml

- Went from UTC-11 to UTC +13
- Align with business interests
- Example that time zones are constructs that are meant to be useful to people
-->
---

# Is it relevant?

* We use references to dates and times everywhere:
  * timestamps
  * appointments
  * comparisons
  * shifts
  * task lists
  * receipts
  * tips

<!--
- And each use has its own nuances!
-->
---

# Is this a problem for JavaScript?

Javascript's Date object has some limitations:

* Browser implementations vary
  - e.g. `Date.parse()`
* Time is always viewed from local frame of reference

---

# In other words

* JS can usually parse any `iso8601` compliant datetime string
* BUT, in doing so, it will always translate the date time to the host computer's set localized date time based on the computer's time zone.

---

# An example

So let's imagine we have two locations

* one based in `UTC-6` (`America/Regina`)
* another based in `UTC-8` (`America/Los_Angeles`)

```javascript
new Date('2021-12-12 14:00:00-06:00')
// => Sun Dec 12 2021 14:10:10 GMT-0600 (Central Standard Time)

new Date('2021-12-12 14:00:00-08:00')
// => Sun Dec 12 2021 16:10:10 GMT-0600 (Central Standard Time)
```

---

# Is this wrong?

* No, for the most instances, this is probably the correct behaviour
* When we view events, bookings, due dates, or schedules, we often want to see what time it happens relative to our local reference of time.

---

# So what?

* How must time be handled if we were to view this as a date time, naive to the timezone?
  * Maybe we want to make use of Locale formatting?

<!-- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/DateTimeFormat -->

* We have to do a "hack" to mutate the date to compensate for the automatic conversion:

```javascript
const persisted = '2021-12-12T08:00:00-08:00'
// => '2021-12-12T08:00:00-08:00'
const persistedDate = new Date(persisted)
// => 2021-12-12T16:00:00.000Z
persistedDate.toLocaleString('en-CA')
// => '2021-12-12, 10:00:00 a.m.' <-- We want to show 8am! Not 10am..

const compensatedDate = new Date(persistedDate.getTime() - 2 * 60 * 60000)
// => 2021-12-12T14:00:00.000Z
compensatedDate.toLocaleString('en-CA')
// => '2021-12-12, 8:00:00 a.m.'
```

---

# Future solutions... TEMPORAL

* Fortunately/unfortunately this is a common problem
* There is [hope on the horizon](https://youtu.be/mNMk0XGa0bQ?t=16) with the [`Temporal` proposal](https://tc39.es/proposal-temporal/docs/index.html)

> Ecma International's TC39 is a group of JavaScript developers, implementers, academics, and more, collaborating with the community to maintain and evolve the definition of JavaScript. <https://tc39.es/>

---

# Temporal Proposal

* new global Date Time object called `Temporal`
* 5 new types of Date time constructs to disambiguate references to time:

  1) `Temporal.Now`
     - The current time.
  2) `Temporal.Instant`:
     - Represents a fixed point in time, without regard to a calendar/location timezone.

---
# Temporal Proposal (contd)

  3) `Temporal.ZonedDateTime`:
     - Is a timezone-aware, calendar-aware datetime object.
     - Is able to handle DST-safe arithmetic and other standards (e.g. iCal)
  4) `Temporal.PlainDate`, `Temporal.PlainTime`, `Temporal.PlainDateTime`, `Temporal.PlainYearMonth`, `Temporal.PlainMonthDay`:
     - Represents a date/time object that is not associated with a time zone
  5) `Temporal.Duration`
     - For expressing a length of time.

---

# Back to our example

So now when we retrieve our time from the database to create our record:

```javascript
const persisted = '2021-12-12T08:00:00-08:00'
const persistedInstant = Temporal.Instant.from(persisted)
const persistedZoned = persistedInstant.toZoneDateTimeISO('America/Los_Angeles')
persistedZoned.toLocaleString('en-CA')
// => '2021-12-12, 8:00:00 a.m. PST'
```

* 🤩🎉

---

# When will `Temporal` be available?

* Currently it is sitting at stage 3
  * requires implementation by browsers and engines
  * Hopefully this might land in ES2023/2024

# What can we do until then?

* Alpha polyfill: https://github.com/js-temporal/temporal-polyfill


---

# Questions?


# Source:

- <https://github.com/neenjaw/temporal-presentation>
