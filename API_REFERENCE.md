# Hutool API Reference

This document provides detailed API reference for all public methods and classes in the Hutool library.

## Table of Contents

- [Core Module (`cn.hutool.core`)](#core-module-cnhutoolcore)
  - [StrUtil](#strutil)
  - [ArrayUtil](#arrayutil)
  - [DateUtil](#dateutil)
  - [FileUtil](#fileutil)
  - [CollUtil](#collutil)
  - [NumberUtil](#numberutil)
  - [BeanUtil](#beanutil)
  - [ReflectUtil](#reflectutil)
  - [IdUtil](#idutil)
  - [RandomUtil](#randomutil)
- [HTTP Module (`cn.hutool.http`)](#http-module-cnhutoolhttp)
- [JSON Module (`cn.hutool.json`)](#json-module-cnhutooljson)
- [Crypto Module (`cn.hutool.crypto`)](#crypto-module-cnhutoolcrypto)
- [Database Module (`cn.hutool.db`)](#database-module-cnhutooldb)

---

# Core Module (`cn.hutool.core`)

## StrUtil

**Package**: `cn.hutool.core.util.StrUtil`

String utility class providing comprehensive string manipulation methods.

### Constants

```java
public static final int INDEX_NOT_FOUND = -1;
public static final String EMPTY = "";
public static final String SPACE = " ";
public static final String TAB = "\t";
public static final String DOT = ".";
public static final String SLASH = "/";
public static final String BACKSLASH = "\\";
public static final String CR = "\r";
public static final String LF = "\n";
public static final String CRLF = "\r\n";
public static final String UNDERLINE = "_";
public static final String COMMA = ",";
```

### String Validation Methods

#### `isBlank(CharSequence str)`
Checks if a string is blank (null, empty, or contains only whitespace).

**Parameters:**
- `str` - the string to check

**Returns:** `boolean` - true if the string is blank

**Example:**
```java
StrUtil.isBlank(null);      // true
StrUtil.isBlank("");        // true
StrUtil.isBlank("   ");     // true
StrUtil.isBlank("hello");   // false
```

#### `isNotBlank(CharSequence str)`
Checks if a string is not blank.

**Parameters:**
- `str` - the string to check

**Returns:** `boolean` - true if the string is not blank

#### `isEmpty(CharSequence str)`
Checks if a string is empty (null or zero length).

**Parameters:**
- `str` - the string to check

**Returns:** `boolean` - true if the string is empty

#### `isNotEmpty(CharSequence str)`
Checks if a string is not empty.

**Parameters:**
- `str` - the string to check

**Returns:** `boolean` - true if the string is not empty

#### `hasBlank(CharSequence... strs)`
Checks if any of the given strings is blank.

**Parameters:**
- `strs` - strings to check

**Returns:** `boolean` - true if any string is blank

#### `hasEmpty(CharSequence... strs)`
Checks if any of the given strings is empty.

**Parameters:**
- `strs` - strings to check

**Returns:** `boolean` - true if any string is empty

### String Formatting Methods

#### `format(String template, Object... params)`
Formats a string with placeholders `{}`.

**Parameters:**
- `template` - string template with `{}` placeholders
- `params` - parameters to replace placeholders

**Returns:** `String` - formatted string

**Example:**
```java
StrUtil.format("Hello {}, welcome to {}", "John", "Hutool");
// Result: "Hello John, welcome to Hutool"

StrUtil.format("User {} has {} points", "Alice", 100);
// Result: "User Alice has 100 points"
```

#### `indexedFormat(String pattern, Object... arguments)`
Formats a string with indexed placeholders `{0}`, `{1}`, etc.

**Parameters:**
- `pattern` - string pattern with indexed placeholders
- `arguments` - arguments to replace placeholders

**Returns:** `String` - formatted string

**Example:**
```java
StrUtil.indexedFormat("User {0} has {1} points and {0} is active", "Bob", 150);
// Result: "User Bob has 150 points and Bob is active"
```

### String Manipulation Methods

#### `trim(CharSequence str)`
Trims whitespace from both ends of a string.

**Parameters:**
- `str` - string to trim

**Returns:** `String` - trimmed string

#### `trimToEmpty(CharSequence str)`
Trims a string, returning empty string if null.

**Parameters:**
- `str` - string to trim

**Returns:** `String` - trimmed string or empty string if null

#### `trimToNull(CharSequence str)`
Trims a string, returning null if the result is empty.

**Parameters:**
- `str` - string to trim

**Returns:** `String` - trimmed string or null if empty

#### `removePrefix(CharSequence str, CharSequence prefix)`
Removes a prefix from a string.

**Parameters:**
- `str` - source string
- `prefix` - prefix to remove

**Returns:** `String` - string without prefix

**Example:**
```java
StrUtil.removePrefix("Mr.John", "Mr.");  // "John"
StrUtil.removePrefix("hello", "hi");     // "hello" (no change)
```

#### `removeSuffix(CharSequence str, CharSequence suffix)`
Removes a suffix from a string.

**Parameters:**
- `str` - source string
- `suffix` - suffix to remove

**Returns:** `String` - string without suffix

**Example:**
```java
StrUtil.removeSuffix("document.pdf", ".pdf");  // "document"
StrUtil.removeSuffix("hello", ".txt");         // "hello" (no change)
```

#### `padPre(CharSequence str, int minLength, char padChar)`
Pads a string on the left with specified character.

**Parameters:**
- `str` - source string
- `minLength` - minimum length of result
- `padChar` - character to pad with

**Returns:** `String` - padded string

**Example:**
```java
StrUtil.padPre("5", 3, '0');    // "005"
StrUtil.padPre("123", 3, '0');  // "123" (no change)
```

#### `padAfter(CharSequence str, int minLength, char padChar)`
Pads a string on the right with specified character.

**Parameters:**
- `str` - source string
- `minLength` - minimum length of result
- `padChar` - character to pad with

**Returns:** `String` - padded string

**Example:**
```java
StrUtil.padAfter("test", 8, '-');  // "test----"
```

### Case Conversion Methods

#### `toCamelCase(CharSequence name)`
Converts underscore-separated string to camelCase.

**Parameters:**
- `name` - underscore-separated string

**Returns:** `String` - camelCase string

**Example:**
```java
StrUtil.toCamelCase("user_name");        // "userName"
StrUtil.toCamelCase("first_name_last");  // "firstNameLast"
```

#### `toUnderlineCase(CharSequence str)`
Converts camelCase string to underscore-separated.

**Parameters:**
- `str` - camelCase string

**Returns:** `String` - underscore-separated string

**Example:**
```java
StrUtil.toUnderlineCase("userName");     // "user_name"
StrUtil.toUnderlineCase("firstName");    // "first_name"
```

#### `upperFirst(CharSequence str)`
Converts first character to uppercase.

**Parameters:**
- `str` - source string

**Returns:** `String` - string with first character uppercase

#### `lowerFirst(CharSequence str)`
Converts first character to lowercase.

**Parameters:**
- `str` - source string

**Returns:** `String` - string with first character lowercase

### String Splitting and Joining Methods

#### `split(CharSequence str, char separator)`
Splits a string by separator character.

**Parameters:**
- `str` - string to split
- `separator` - separator character

**Returns:** `String[]` - array of split strings

**Example:**
```java
StrUtil.split("a,b,c", ',');  // ["a", "b", "c"]
StrUtil.split("a;b;c", ';');  // ["a", "b", "c"]
```

#### `split(CharSequence str, String separator)`
Splits a string by separator string.

**Parameters:**
- `str` - string to split
- `separator` - separator string

**Returns:** `String[]` - array of split strings

#### `splitTrim(CharSequence str, char separator)`
Splits a string by separator and trims each part.

**Parameters:**
- `str` - string to split
- `separator` - separator character

**Returns:** `List<String>` - list of trimmed strings

**Example:**
```java
StrUtil.splitTrim("a, b , c", ',');  // ["a", "b", "c"]
```

#### `join(CharSequence conjunction, Object... objs)`
Joins objects into a string with separator.

**Parameters:**
- `conjunction` - separator string
- `objs` - objects to join

**Returns:** `String` - joined string

**Example:**
```java
StrUtil.join(",", "a", "b", "c");           // "a,b,c"
StrUtil.join("-", 1, 2, 3);                 // "1-2-3"
StrUtil.join("|", Arrays.asList("x", "y")); // "x|y"
```

### String Search Methods

#### `contains(CharSequence str, char searchChar)`
Checks if string contains a character.

**Parameters:**
- `str` - string to search in
- `searchChar` - character to search for

**Returns:** `boolean` - true if character is found

#### `contains(CharSequence str, CharSequence searchStr)`
Checks if string contains a substring.

**Parameters:**
- `str` - string to search in
- `searchStr` - substring to search for

**Returns:** `boolean` - true if substring is found

#### `indexOf(CharSequence str, char searchChar)`
Finds first index of character in string.

**Parameters:**
- `str` - string to search in
- `searchChar` - character to search for

**Returns:** `int` - index of character or -1 if not found

#### `indexOf(CharSequence str, CharSequence searchStr)`
Finds first index of substring in string.

**Parameters:**
- `str` - string to search in
- `searchStr` - substring to search for

**Returns:** `int` - index of substring or -1 if not found

#### `lastIndexOf(CharSequence str, char searchChar)`
Finds last index of character in string.

**Parameters:**
- `str` - string to search in
- `searchChar` - character to search for

**Returns:** `int` - last index of character or -1 if not found

### String Similarity Methods

#### `similar(CharSequence str1, CharSequence str2)`
Calculates similarity between two strings (0.0 to 1.0).

**Parameters:**
- `str1` - first string
- `str2` - second string

**Returns:** `double` - similarity score (0.0 = completely different, 1.0 = identical)

**Example:**
```java
StrUtil.similar("hello", "hallo");  // 0.8
StrUtil.similar("abc", "xyz");      // 0.0
StrUtil.similar("test", "test");    // 1.0
```

#### `similar(CharSequence str1, CharSequence str2, double threshold)`
Checks if two strings are similar above threshold.

**Parameters:**
- `str1` - first string
- `str2` - second string
- `threshold` - minimum similarity threshold

**Returns:** `boolean` - true if similarity >= threshold

---

## ArrayUtil

**Package**: `cn.hutool.core.util.ArrayUtil`

Array utility class providing comprehensive array manipulation methods.

### Constants

```java
public static final int INDEX_NOT_FOUND = -1;
```

### Array Validation Methods

#### `isEmpty(T... array)`
Checks if an array is empty or null.

**Parameters:**
- `array` - array to check

**Returns:** `boolean` - true if array is null or empty

**Example:**
```java
ArrayUtil.isEmpty(new String[]{});     // true
ArrayUtil.isEmpty(new String[]{"a"});  // false
ArrayUtil.isEmpty(null);               // true
```

#### `isNotEmpty(T... array)`
Checks if an array is not empty.

**Parameters:**
- `array` - array to check

**Returns:** `boolean` - true if array is not null and not empty

#### `hasNull(Object... array)`
Checks if array contains null elements.

**Parameters:**
- `array` - array to check

**Returns:** `boolean` - true if array contains null

### Array Creation Methods

#### `newArray(Class<?> componentType, int newSize)`
Creates a new array of specified type and size.

**Parameters:**
- `componentType` - component type of array
- `newSize` - size of new array

**Returns:** `T[]` - new array

**Example:**
```java
String[] strings = ArrayUtil.newArray(String.class, 5);
Integer[] integers = ArrayUtil.newArray(Integer.class, 10);
```

#### `wrap(Object... values)`
Wraps primitive or object values into array.

**Parameters:**
- `values` - values to wrap

**Returns:** `T[]` - array containing values

**Example:**
```java
Integer[] wrapped = ArrayUtil.wrap(1, 2, 3, 4, 5);
String[] stringArray = ArrayUtil.wrap("a", "b", "c");
```

### Array Manipulation Methods

#### `reverse(T[] array)`
Reverses the order of elements in array.

**Parameters:**
- `array` - array to reverse

**Returns:** `T[]` - reversed array

**Example:**
```java
String[] original = {"a", "b", "c"};
String[] reversed = ArrayUtil.reverse(original);  // ["c", "b", "a"]
```

#### `remove(T[] array, int index)`
Removes element at specified index.

**Parameters:**
- `array` - source array
- `index` - index to remove

**Returns:** `T[]` - new array without element

**Example:**
```java
String[] original = {"a", "b", "c"};
String[] result = ArrayUtil.remove(original, 1);  // ["a", "c"]
```

#### `removeEle(T[] array, T element)`
Removes first occurrence of element.

**Parameters:**
- `array` - source array
- `element` - element to remove

**Returns:** `T[]` - new array without element

#### `append(T[] buffer, T... newElements)`
Appends elements to array.

**Parameters:**
- `buffer` - original array
- `newElements` - elements to append

**Returns:** `T[]` - new array with appended elements

**Example:**
```java
String[] original = {"a", "b"};
String[] result = ArrayUtil.append(original, "c", "d");  // ["a", "b", "c", "d"]
```

#### `insert(T[] buffer, int index, T... newElements)`
Inserts elements at specified index.

**Parameters:**
- `buffer` - original array
- `index` - insertion index
- `newElements` - elements to insert

**Returns:** `T[]` - new array with inserted elements

### Array Search Methods

#### `contains(T[] array, T value)`
Checks if array contains value.

**Parameters:**
- `array` - array to search in
- `value` - value to search for

**Returns:** `boolean` - true if value is found

**Example:**
```java
String[] array = {"a", "b", "c"};
boolean found = ArrayUtil.contains(array, "b");  // true
```

#### `indexOf(T[] array, Object value)`
Finds first index of value in array.

**Parameters:**
- `array` - array to search in
- `value` - value to search for

**Returns:** `int` - index of value or -1 if not found

#### `lastIndexOf(T[] array, Object value)`
Finds last index of value in array.

**Parameters:**
- `array` - array to search in
- `value` - value to search for

**Returns:** `int` - last index of value or -1 if not found

### Array Conversion Methods

#### `join(T[] array, CharSequence conjunction)`
Joins array elements into string.

**Parameters:**
- `array` - array to join
- `conjunction` - separator string

**Returns:** `String` - joined string

**Example:**
```java
String[] array = {"a", "b", "c"};
String joined = ArrayUtil.join(array, ",");  // "a,b,c"
```

#### `toString(Object array)`
Converts array to string representation.

**Parameters:**
- `array` - array to convert

**Returns:** `String` - string representation

---

## DateUtil

**Package**: `cn.hutool.core.date.DateUtil`

Date utility class providing comprehensive date and time operations.

### Date Creation Methods

#### `date()`
Creates current DateTime object.

**Returns:** `DateTime` - current date and time

**Example:**
```java
DateTime now = DateUtil.date();
```

#### `date(Date date)`
Converts Date to DateTime.

**Parameters:**
- `date` - Date object to convert

**Returns:** `DateTime` - DateTime object

#### `date(long date)`
Creates DateTime from timestamp.

**Parameters:**
- `date` - timestamp in milliseconds

**Returns:** `DateTime` - DateTime object

#### `date(Calendar calendar)`
Creates DateTime from Calendar.

**Parameters:**
- `calendar` - Calendar object

**Returns:** `DateTime` - DateTime object

### Date Parsing Methods

#### `parse(CharSequence dateStr)`
Parses date string using common formats.

**Parameters:**
- `dateStr` - date string to parse

**Returns:** `DateTime` - parsed DateTime object

**Example:**
```java
DateTime date1 = DateUtil.parse("2023-12-25");
DateTime date2 = DateUtil.parse("2023-12-25 10:30:00");
DateTime date3 = DateUtil.parse("2023/12/25");
```

#### `parse(CharSequence dateStr, String pattern)`
Parses date string using specified pattern.

**Parameters:**
- `dateStr` - date string to parse
- `pattern` - date pattern

**Returns:** `DateTime` - parsed DateTime object

**Example:**
```java
DateTime date = DateUtil.parse("25/12/2023", "dd/MM/yyyy");
DateTime dateTime = DateUtil.parse("2023-12-25 10:30", "yyyy-MM-dd HH:mm");
```

#### `parseDate(CharSequence dateStr)`
Parses date string to Date object.

**Parameters:**
- `dateStr` - date string to parse

**Returns:** `Date` - parsed Date object

### Date Formatting Methods

#### `format(Date date, String format)`
Formats date using specified pattern.

**Parameters:**
- `date` - date to format
- `format` - format pattern

**Returns:** `String` - formatted date string

**Example:**
```java
Date now = new Date();
String formatted = DateUtil.format(now, "yyyy-MM-dd HH:mm:ss");
String dateOnly = DateUtil.format(now, "yyyy-MM-dd");
String timeOnly = DateUtil.format(now, "HH:mm:ss");
```

#### `formatDate(Date date)`
Formats date as "yyyy-MM-dd".

**Parameters:**
- `date` - date to format

**Returns:** `String` - formatted date string

#### `formatTime(Date date)`
Formats time as "HH:mm:ss".

**Parameters:**
- `date` - date to format

**Returns:** `String` - formatted time string

#### `formatDateTime(Date date)`
Formats date and time as "yyyy-MM-dd HH:mm:ss".

**Parameters:**
- `date` - date to format

**Returns:** `String` - formatted datetime string

### Date Arithmetic Methods

#### `offsetDay(Date date, int offset)`
Adds/subtracts days from date.

**Parameters:**
- `date` - source date
- `offset` - days to add (positive) or subtract (negative)

**Returns:** `DateTime` - new date with offset

**Example:**
```java
Date today = new Date();
DateTime tomorrow = DateUtil.offsetDay(today, 1);
DateTime yesterday = DateUtil.offsetDay(today, -1);
DateTime nextWeek = DateUtil.offsetDay(today, 7);
```

#### `offsetHour(Date date, int offset)`
Adds/subtracts hours from date.

**Parameters:**
- `date` - source date
- `offset` - hours to add/subtract

**Returns:** `DateTime` - new date with offset

#### `offsetMinute(Date date, int offset)`
Adds/subtracts minutes from date.

**Parameters:**
- `date` - source date
- `offset` - minutes to add/subtract

**Returns:** `DateTime` - new date with offset

#### `offsetSecond(Date date, int offset)`
Adds/subtracts seconds from date.

**Parameters:**
- `date` - source date
- `offset` - seconds to add/subtract

**Returns:** `DateTime` - new date with offset

#### `offsetMonth(Date date, int offset)`
Adds/subtracts months from date.

**Parameters:**
- `date` - source date
- `offset` - months to add/subtract

**Returns:** `DateTime` - new date with offset

#### `offsetYear(Date date, int offset)`
Adds/subtracts years from date.

**Parameters:**
- `date` - source date
- `offset` - years to add/subtract

**Returns:** `DateTime` - new date with offset

### Date Comparison Methods

#### `compare(Date date1, Date date2)`
Compares two dates.

**Parameters:**
- `date1` - first date
- `date2` - second date

**Returns:** `int` - negative if date1 < date2, 0 if equal, positive if date1 > date2

#### `isSameDay(Date date1, Date date2)`
Checks if two dates are on the same day.

**Parameters:**
- `date1` - first date
- `date2` - second date

**Returns:** `boolean` - true if same day

#### `isIn(Date date, Date beginDate, Date endDate)`
Checks if date is within range.

**Parameters:**
- `date` - date to check
- `beginDate` - range start
- `endDate` - range end

**Returns:** `boolean` - true if date is in range

### Date Calculation Methods

#### `betweenDay(Date beginDate, Date endDate, boolean isReset)`
Calculates days between two dates.

**Parameters:**
- `beginDate` - start date
- `endDate` - end date
- `isReset` - whether to reset time to start of day

**Returns:** `long` - number of days between dates

**Example:**
```java
Date start = DateUtil.parse("2023-01-01");
Date end = DateUtil.parse("2023-01-31");
long days = DateUtil.betweenDay(start, end, false);  // 30
```

#### `between(Date beginDate, Date endDate, DateUnit unit)`
Calculates time between dates in specified unit.

**Parameters:**
- `beginDate` - start date
- `endDate` - end date
- `unit` - time unit (MILLISECOND, SECOND, MINUTE, HOUR, DAY)

**Returns:** `long` - time difference in specified unit

#### `ageOfNow(Date birthDay)`
Calculates current age from birthday.

**Parameters:**
- `birthDay` - birth date

**Returns:** `int` - current age in years

#### `ageOfNow(String birthDay)`
Calculates current age from birthday string.

**Parameters:**
- `birthDay` - birth date string

**Returns:** `int` - current age in years

**Example:**
```java
int age1 = DateUtil.ageOfNow("1990-05-15");
int age2 = DateUtil.ageOfNow(DateUtil.parse("1985-12-25"));
```

### Convenience Methods

#### `today()`
Gets today's date (start of day).

**Returns:** `DateTime` - today's date

#### `yesterday()`
Gets yesterday's date.

**Returns:** `DateTime` - yesterday's date

#### `tomorrow()`
Gets tomorrow's date.

**Returns:** `DateTime` - tomorrow's date

#### `thisWeek()`
Gets start of current week.

**Returns:** `DateTime` - start of week

#### `thisMonth()`
Gets start of current month.

**Returns:** `DateTime` - start of month

#### `thisYear()`
Gets start of current year.

**Returns:** `DateTime` - start of year

---

## FileUtil

**Package**: `cn.hutool.core.io.FileUtil`

File utility class providing comprehensive file and directory operations.

### File Reading Methods

#### `readUtf8String(File file)`
Reads file content as UTF-8 string.

**Parameters:**
- `file` - file to read

**Returns:** `String` - file content

**Example:**
```java
File file = new File("data.txt");
String content = FileUtil.readUtf8String(file);
```

#### `readString(File file, Charset charset)`
Reads file content with specified charset.

**Parameters:**
- `file` - file to read
- `charset` - character encoding

**Returns:** `String` - file content

#### `readBytes(File file)`
Reads file content as byte array.

**Parameters:**
- `file` - file to read

**Returns:** `byte[]` - file content as bytes

#### `readUtf8Lines(File file)`
Reads file lines as UTF-8 string list.

**Parameters:**
- `file` - file to read

**Returns:** `List<String>` - list of lines

**Example:**
```java
List<String> lines = FileUtil.readUtf8Lines(new File("config.txt"));
for (String line : lines) {
    System.out.println(line);
}
```

### File Writing Methods

#### `writeUtf8String(String content, File file)`
Writes string content to file using UTF-8.

**Parameters:**
- `content` - content to write
- `file` - target file

**Returns:** `File` - the file written to

**Example:**
```java
FileUtil.writeUtf8String("Hello World", new File("output.txt"));
```

#### `writeString(String content, File file, Charset charset)`
Writes string content to file with specified charset.

**Parameters:**
- `content` - content to write
- `file` - target file
- `charset` - character encoding

**Returns:** `File` - the file written to

#### `writeBytes(byte[] data, File file)`
Writes byte array to file.

**Parameters:**
- `data` - data to write
- `file` - target file

**Returns:** `File` - the file written to

#### `appendUtf8String(String content, File file)`
Appends string content to file using UTF-8.

**Parameters:**
- `content` - content to append
- `file` - target file

**Returns:** `File` - the file appended to

### File Operation Methods

#### `copy(File src, File dest, boolean isOverride)`
Copies file or directory.

**Parameters:**
- `src` - source file/directory
- `dest` - destination file/directory
- `isOverride` - whether to override existing files

**Returns:** `File` - destination file

**Example:**
```java
FileUtil.copy(new File("source.txt"), new File("backup.txt"), true);
FileUtil.copy(new File("src_dir"), new File("dest_dir"), true);
```

#### `move(File src, File target, boolean isOverride)`
Moves file or directory.

**Parameters:**
- `src` - source file/directory
- `target` - target location
- `isOverride` - whether to override existing files

**Returns:** `File` - target file

#### `del(File file)`
Deletes file or directory.

**Parameters:**
- `file` - file/directory to delete

**Returns:** `boolean` - true if deletion successful

**Example:**
```java
boolean deleted = FileUtil.del(new File("unwanted.txt"));
FileUtil.del(new File("temp_directory"));
```

#### `clean(File directory)`
Cleans directory contents without deleting directory itself.

**Parameters:**
- `directory` - directory to clean

**Returns:** `boolean` - true if cleaning successful

### Directory Operations

#### `mkdir(File dir)`
Creates directory and parent directories if needed.

**Parameters:**
- `dir` - directory to create

**Returns:** `boolean` - true if creation successful

**Example:**
```java
FileUtil.mkdir(new File("path/to/new/directory"));
```

#### `mkParentDirs(File file)`
Creates parent directories for file.

**Parameters:**
- `file` - file whose parent directories to create

**Returns:** `File` - the file parameter

#### `loopFiles(File file)`
Lists all files in directory recursively.

**Parameters:**
- `file` - directory to scan

**Returns:** `List<File>` - list of all files

**Example:**
```java
List<File> allFiles = FileUtil.loopFiles(new File("project_dir"));
for (File f : allFiles) {
    System.out.println(f.getAbsolutePath());
}
```

#### `loopFiles(File file, FileFilter fileFilter)`
Lists filtered files in directory recursively.

**Parameters:**
- `file` - directory to scan
- `fileFilter` - filter for files

**Returns:** `List<File>` - list of filtered files

**Example:**
```java
// Find all .txt files
List<File> txtFiles = FileUtil.loopFiles(new File("docs"), 
    f -> f.getName().endsWith(".txt"));

// Find files larger than 1MB
List<File> largeFiles = FileUtil.loopFiles(new File("data"),
    f -> f.length() > 1024 * 1024);
```

### File Information Methods

#### `size(File file)`
Gets file or directory size in bytes.

**Parameters:**
- `file` - file or directory

**Returns:** `long` - size in bytes

#### `readableFileSize(long size)`
Converts byte size to human-readable format.

**Parameters:**
- `size` - size in bytes

**Returns:** `String` - human-readable size (e.g., "1.5 MB")

**Example:**
```java
long fileSize = FileUtil.size(new File("large_file.zip"));
String readable = FileUtil.readableFileSize(fileSize);
System.out.println("File size: " + readable);  // "File size: 15.2 MB"
```

#### `getName(File file)`
Gets file name without path.

**Parameters:**
- `file` - file

**Returns:** `String` - file name

#### `getName(String filePath)`
Gets file name from path string.

**Parameters:**
- `filePath` - file path

**Returns:** `String` - file name

**Example:**
```java
String name = FileUtil.getName("/path/to/document.pdf");  // "document.pdf"
```

#### `mainName(File file)`
Gets file name without extension.

**Parameters:**
- `file` - file

**Returns:** `String` - main name without extension

#### `mainName(String fileName)`
Gets main name from file name string.

**Parameters:**
- `fileName` - file name

**Returns:** `String` - main name without extension

**Example:**
```java
String mainName = FileUtil.mainName("document.pdf");  // "document"
```

#### `getSuffix(File file)`
Gets file extension.

**Parameters:**
- `file` - file

**Returns:** `String` - file extension

#### `getSuffix(String fileName)`
Gets extension from file name string.

**Parameters:**
- `fileName` - file name

**Returns:** `String` - file extension

**Example:**
```java
String ext = FileUtil.getSuffix("document.pdf");  // "pdf"
```

#### `getMimeType(String filePath)`
Gets MIME type of file.

**Parameters:**
- `filePath` - file path

**Returns:** `String` - MIME type

**Example:**
```java
String mimeType = FileUtil.getMimeType("image.jpg");  // "image/jpeg"
String pdfType = FileUtil.getMimeType("doc.pdf");     // "application/pdf"
```

---

## CollUtil

**Package**: `cn.hutool.core.collection.CollUtil`

Collection utility class providing comprehensive collection operations.

### Collection Validation Methods

#### `isEmpty(Collection<?> collection)`
Checks if collection is empty or null.

**Parameters:**
- `collection` - collection to check

**Returns:** `boolean` - true if collection is null or empty

#### `isNotEmpty(Collection<?> collection)`
Checks if collection is not empty.

**Parameters:**
- `collection` - collection to check

**Returns:** `boolean` - true if collection is not null and not empty

#### `hasNull(Collection<?> collection)`
Checks if collection contains null elements.

**Parameters:**
- `collection` - collection to check

**Returns:** `boolean` - true if collection contains null

### Collection Creation Methods

#### `newArrayList(T... values)`
Creates ArrayList with initial values.

**Parameters:**
- `values` - initial values

**Returns:** `ArrayList<T>` - new ArrayList

**Example:**
```java
List<String> list = CollUtil.newArrayList("a", "b", "c");
List<Integer> numbers = CollUtil.newArrayList(1, 2, 3, 4, 5);
```

#### `newHashSet(T... values)`
Creates HashSet with initial values.

**Parameters:**
- `values` - initial values

**Returns:** `HashSet<T>` - new HashSet

#### `newLinkedHashSet(T... values)`
Creates LinkedHashSet with initial values.

**Parameters:**
- `values` - initial values

**Returns:** `LinkedHashSet<T>` - new LinkedHashSet

### Collection Operation Methods

#### `filter(Collection<T> collection, Filter<T> filter)`
Filters collection elements.

**Parameters:**
- `collection` - source collection
- `filter` - filter predicate

**Returns:** `List<T>` - filtered list

**Example:**
```java
List<String> words = Arrays.asList("apple", "banana", "cherry", "date");
List<String> longWords = CollUtil.filter(words, word -> word.length() > 5);
// Result: ["banana", "cherry"]
```

#### `map(Collection<T> collection, Function<T, R> mapper)`
Maps collection elements to new type.

**Parameters:**
- `collection` - source collection
- `mapper` - mapping function

**Returns:** `List<R>` - mapped list

**Example:**
```java
List<String> words = Arrays.asList("hello", "world");
List<String> upperWords = CollUtil.map(words, String::toUpperCase);
// Result: ["HELLO", "WORLD"]

List<Integer> lengths = CollUtil.map(words, String::length);
// Result: [5, 5]
```

#### `join(Collection<T> collection, CharSequence conjunction)`
Joins collection elements into string.

**Parameters:**
- `collection` - collection to join
- `conjunction` - separator

**Returns:** `String` - joined string

**Example:**
```java
List<String> items = Arrays.asList("a", "b", "c");
String joined = CollUtil.join(items, ", ");  // "a, b, c"
```

#### `split(Collection<T> collection, int size)`
Splits collection into smaller lists.

**Parameters:**
- `collection` - collection to split
- `size` - size of each split

**Returns:** `List<List<T>>` - list of split collections

**Example:**
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9);
List<List<Integer>> batches = CollUtil.split(numbers, 3);
// Result: [[1,2,3], [4,5,6], [7,8,9]]
```

#### `reverse(List<T> list)`
Reverses list order.

**Parameters:**
- `list` - list to reverse

**Returns:** `List<T>` - reversed list

#### `sort(List<T> list, Comparator<T> comparator)`
Sorts list with comparator.

**Parameters:**
- `list` - list to sort
- `comparator` - comparison function

**Returns:** `List<T>` - sorted list

---

This API reference continues with additional utility classes. Each class provides comprehensive methods for specific domains like numbers, beans, reflection, IDs, and random operations. The consistent API design makes it easy to learn and use across all modules.