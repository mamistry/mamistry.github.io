This document provides comprehensive documentation for the methods and functions found within the provided file.

---

# Documentation for `dateTimeToDate`

## Method Name: `dateTimeToDate`

### Description
Converts a date-time string into a `Date` object. It returns `null` if the input string successfully parses into a `Date` object whose `getTime()` value is exactly `0` (representing the Unix epoch). Otherwise, it returns the parsed `Date` object.

### Call Stack

#### Functions that call this method:
*   Not identifiable from the provided single file.

#### This method calls:
*   `Date` (built-in JavaScript constructor): Used to create a new `Date` object from a string representation.
*   `Date.prototype.getTime()` (built-in JavaScript method): A method of the `Date` object that returns the number of milliseconds since the Unix epoch (January 1, 1970, 00:00:00 UTC).

### Example Usage

```typescript
import { dateTimeToDate } from './your-file-path'; // Adjust the import path as necessary

// Example 1: Valid date-time string
const validDate = dateTimeToDate("2023-10-27T10:30:00Z");
console.log(validDate);
// Expected output (varies by locale/timezone, but represents): Fri Oct 27 2023 12:30:00 GMT+0200 (Central European Summer Time)
// (or similar, depending on your environment's timezone)

// Example 2: Date-time string that parses to the Unix epoch
const epochDate = dateTimeToDate("1970-01-01T00:00:00Z");
console.log(epochDate);
// Expected output: null
// This is because a Date object created from "1970-01-01T00:00:00Z" has getTime() === 0.

// Example 3: An "invalid" date-time string.
// When an invalid string is passed to `new Date()`, it creates an "Invalid Date" object.
// The `getTime()` method of an "Invalid Date" object returns `NaN`.
// Since `NaN !== 0`, the function will return the "Invalid Date" object itself, not null.
const invalidDate = dateTimeToDate("this is not a date");
console.log(invalidDate);
console.log(invalidDate?.toString());
console.log(invalidDate?.getTime());
// Expected output:
// Date { <invalid date> }
// "Invalid Date"
// NaN
```