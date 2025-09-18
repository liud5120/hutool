# Hutool API Documentation

<p align="center">
	<img src="https://hutool.cn/images/logo.jpg" width="400">
</p>

<p align="center">
	<strong>A comprehensive Java utility library that keeps Java sweet.</strong>
</p>

## Overview

Hutool is a comprehensive Java utility library that provides a rich set of tools for common programming tasks. It simplifies Java development by offering well-designed APIs for string manipulation, HTTP requests, JSON processing, cryptography, database operations, and much more.

**Version**: 4.5.2  
**License**: Apache License 2.0  
**JDK Requirement**: 1.7+  

## Quick Start

### Maven Dependency
```xml
<dependency>
    <groupId>cn.hutool</groupId>
    <artifactId>hutool-all</artifactId>
    <version>4.5.2</version>
</dependency>
```

### Gradle Dependency
```groovy
implementation 'cn.hutool:hutool-all:4.5.2'
```

## Module Overview

Hutool is organized into several modules, each focusing on specific functionality:

| Module | Description | Key Classes |
|--------|-------------|-------------|
| `hutool-core` | Core utilities including string, array, date, I/O operations | `StrUtil`, `ArrayUtil`, `DateUtil`, `FileUtil` |
| `hutool-http` | HTTP client utilities | `HttpUtil`, `HttpRequest`, `HttpResponse` |
| `hutool-json` | JSON processing | `JSONUtil`, `JSONObject`, `JSONArray` |
| `hutool-crypto` | Cryptography and security | `SecureUtil`, `DigestUtil`, `SymmetricCrypto` |
| `hutool-db` | Database operations and ORM | `DbUtil`, `Db`, `Entity`, `SqlExecutor` |
| `hutool-cache` | Caching utilities | `CacheUtil`, `FIFOCache`, `LRUCache` |
| `hutool-cron` | Cron job scheduling | `CronUtil`, `CronPattern` |
| `hutool-extra` | Third-party integrations | Template engines, email, QR codes |
| `hutool-poi` | Excel operations | `ExcelUtil`, `ExcelReader`, `ExcelWriter` |
| `hutool-captcha` | CAPTCHA generation | `CaptchaUtil`, `LineCaptcha`, `CircleCaptcha` |

---

# Core Module (`hutool-core`)

The core module provides fundamental utilities that are commonly used in Java applications.

## String Utilities (`StrUtil`)

The `StrUtil` class provides comprehensive string manipulation utilities.

### Key Features
- String validation and checking
- String formatting and building
- String splitting and joining
- Case conversion
- String similarity comparison

### Common Methods

#### String Validation
```java
import cn.hutool.core.util.StrUtil;

// Check if string is blank (null, empty, or whitespace)
boolean isBlank = StrUtil.isBlank("   "); // true
boolean isNotBlank = StrUtil.isNotBlank("hello"); // true

// Check if string is empty
boolean isEmpty = StrUtil.isEmpty(""); // true
boolean isNotEmpty = StrUtil.isNotEmpty("world"); // true
```

#### String Formatting
```java
// Format string with placeholders
String formatted = StrUtil.format("Hello {}, welcome to {}", "John", "Hutool");
// Result: "Hello John, welcome to Hutool"

// Format with indexed placeholders
String indexed = StrUtil.format("User {0} has {1} points", "Alice", 100);
// Result: "User Alice has 100 points"
```

#### String Manipulation
```java
// Remove prefix/suffix
String withoutPrefix = StrUtil.removePrefix("Mr.John", "Mr."); // "John"
String withoutSuffix = StrUtil.removeSuffix("document.pdf", ".pdf"); // "document"

// Padding
String padded = StrUtil.padPre("5", 3, '0'); // "005"
String paddedPost = StrUtil.padAfter("test", 8, '-'); // "test----"

// Case conversion
String camelCase = StrUtil.toCamelCase("user_name"); // "userName"
String underlineCase = StrUtil.toUnderlineCase("userName"); // "user_name"
```

#### String Splitting and Joining
```java
// Split string
String[] parts = StrUtil.split("a,b,c", ","); // ["a", "b", "c"]
List<String> list = StrUtil.splitTrim("a, b , c", ","); // ["a", "b", "c"] (trimmed)

// Join strings
String joined = StrUtil.join(",", "a", "b", "c"); // "a,b,c"
String joinedList = StrUtil.join(",", Arrays.asList("x", "y", "z")); // "x,y,z"
```

#### String Similarity
```java
// Calculate string similarity
double similarity = StrUtil.similar("hello", "hallo"); // 0.8
boolean isSimilar = StrUtil.similar("test", "text", 0.7); // true if similarity >= 0.7
```

## Array Utilities (`ArrayUtil`)

The `ArrayUtil` class provides comprehensive array manipulation utilities.

### Key Features
- Array validation and checking
- Array creation and conversion
- Array searching and filtering
- Array manipulation operations

### Common Methods

#### Array Validation
```java
import cn.hutool.core.util.ArrayUtil;

// Check if array is empty
boolean isEmpty = ArrayUtil.isEmpty(new String[]{}); // true
boolean isNotEmpty = ArrayUtil.isNotEmpty(new int[]{1, 2, 3}); // true

// Check if array contains element
boolean contains = ArrayUtil.contains(new String[]{"a", "b", "c"}, "b"); // true
```

#### Array Creation
```java
// Create array from values
String[] array = ArrayUtil.newArray(String.class, 5); // String array of size 5
Integer[] intArray = ArrayUtil.wrap(1, 2, 3, 4, 5); // [1, 2, 3, 4, 5]

// Convert collection to array
List<String> list = Arrays.asList("x", "y", "z");
String[] fromList = ArrayUtil.toArray(list, String.class);
```

#### Array Operations
```java
// Reverse array
int[] original = {1, 2, 3, 4, 5};
int[] reversed = ArrayUtil.reverse(original); // [5, 4, 3, 2, 1]

// Remove elements
String[] withRemoved = ArrayUtil.remove(new String[]{"a", "b", "c"}, 1); // ["a", "c"]

// Add elements
String[] withAdded = ArrayUtil.append(new String[]{"a", "b"}, "c"); // ["a", "b", "c"]

// Find index
int index = ArrayUtil.indexOf(new String[]{"x", "y", "z"}, "y"); // 1
```

## Date Utilities (`DateUtil`)

The `DateUtil` class provides comprehensive date and time manipulation utilities.

### Key Features
- Date creation and parsing
- Date formatting
- Date arithmetic
- Date comparison
- Calendar operations

### Common Methods

#### Date Creation and Parsing
```java
import cn.hutool.core.date.DateUtil;
import cn.hutool.core.date.DateTime;

// Current date/time
DateTime now = DateUtil.date(); // Current DateTime
Date currentDate = DateUtil.date().toJdkDate(); // Current java.util.Date

// Parse date strings
DateTime parsed = DateUtil.parse("2023-12-25 10:30:00");
DateTime parseCustom = DateUtil.parse("25/12/2023", "dd/MM/yyyy");

// Create specific dates
DateTime specificDate = DateUtil.date(2023, 12, 25); // December 25, 2023
DateTime withTime = DateUtil.date(2023, 12, 25, 10, 30, 0); // With time
```

#### Date Formatting
```java
// Format dates
String formatted = DateUtil.format(new Date(), "yyyy-MM-dd HH:mm:ss");
String shortFormat = DateUtil.format(new Date(), "yyyy-MM-dd");

// Predefined formats
String isoFormat = DateUtil.formatDateTime(new Date()); // ISO format
String dateOnly = DateUtil.formatDate(new Date()); // Date only
String timeOnly = DateUtil.formatTime(new Date()); // Time only
```

#### Date Arithmetic
```java
// Add/subtract time
DateTime tomorrow = DateUtil.tomorrow(); // Tomorrow
DateTime yesterday = DateUtil.yesterday(); // Yesterday

// Offset operations
DateTime nextWeek = DateUtil.offsetDay(DateUtil.date(), 7); // Add 7 days
DateTime lastMonth = DateUtil.offsetMonth(DateUtil.date(), -1); // Subtract 1 month
DateTime nextHour = DateUtil.offsetHour(DateUtil.date(), 1); // Add 1 hour
```

#### Date Comparison and Calculation
```java
// Compare dates
Date date1 = DateUtil.parse("2023-01-01").toJdkDate();
Date date2 = DateUtil.parse("2023-12-31").toJdkDate();

boolean isSameDay = DateUtil.isSameDay(date1, date2); // false
boolean isAfter = DateUtil.compare(date2, date1) > 0; // true

// Calculate differences
long daysBetween = DateUtil.betweenDay(date1, date2, false); // Days between
long hoursBetween = DateUtil.between(date1, date2, DateUnit.HOUR); // Hours between
```

#### Age and Time Range
```java
// Calculate age
String birthday = "1990-05-15";
int age = DateUtil.ageOfNow(birthday); // Current age

// Time range operations
DateTime start = DateUtil.parse("2023-01-01 00:00:00");
DateTime end = DateUtil.parse("2023-01-31 23:59:59");
boolean isInRange = DateUtil.isIn(DateUtil.date(), start, end);
```

## File I/O Utilities (`FileUtil`)

The `FileUtil` class provides comprehensive file and directory operations.

### Key Features
- File reading and writing
- Directory operations
- File copying and moving
- File type detection
- Path manipulation

### Common Methods

#### File Reading
```java
import cn.hutool.core.io.FileUtil;
import java.io.File;

// Read file content
String content = FileUtil.readUtf8String(new File("data.txt"));
String contentWithCharset = FileUtil.readString(new File("data.txt"), "GBK");

// Read as bytes
byte[] bytes = FileUtil.readBytes(new File("image.jpg"));

// Read lines
List<String> lines = FileUtil.readUtf8Lines(new File("data.txt"));
```

#### File Writing
```java
// Write string content
FileUtil.writeUtf8String("Hello World", new File("output.txt"));
FileUtil.writeString("Content", new File("output.txt"), "UTF-8");

// Append content
FileUtil.appendUtf8String("Additional content", new File("output.txt"));

// Write bytes
byte[] data = "Binary data".getBytes();
FileUtil.writeBytes(data, new File("binary.dat"));
```

#### File Operations
```java
// Copy files
FileUtil.copy(new File("source.txt"), new File("destination.txt"), true);
FileUtil.copyContent(new File("source"), new File("target"), true); // Copy directory

// Move files
FileUtil.move(new File("old_location.txt"), new File("new_location.txt"), true);

// Delete files/directories
FileUtil.del(new File("unwanted.txt")); // Delete file
FileUtil.clean(new File("temp_dir")); // Clean directory contents
```

#### Directory Operations
```java
// Create directories
FileUtil.mkdir(new File("new_directory"));
FileUtil.mkParentDirs(new File("path/to/new/file.txt")); // Create parent directories

// List directory contents
List<File> files = FileUtil.loopFiles(new File("directory"));
List<File> txtFiles = FileUtil.loopFiles(new File("directory"), file -> 
    file.getName().endsWith(".txt"));
```

#### File Information
```java
// File properties
long size = FileUtil.size(new File("data.txt")); // File size in bytes
String readable = FileUtil.readableFileSize(size); // Human readable size

// File type detection
String mimeType = FileUtil.getMimeType("document.pdf");
String extension = FileUtil.getSuffix("document.pdf"); // "pdf"

// Path operations
String name = FileUtil.getName("path/to/file.txt"); // "file.txt"
String mainName = FileUtil.mainName("file.txt"); // "file"
```

---

# HTTP Module (`hutool-http`)

The HTTP module provides a simple and powerful HTTP client for making web requests.

## HTTP Utilities (`HttpUtil`)

### Key Features
- Simple HTTP GET/POST requests
- File upload and download
- Custom headers and cookies
- SSL/HTTPS support
- Proxy support

### Common Methods

#### Simple HTTP Requests
```java
import cn.hutool.http.HttpUtil;
import cn.hutool.http.HttpRequest;

// Simple GET request
String response = HttpUtil.get("https://api.example.com/data");

// GET with parameters
String responseWithParams = HttpUtil.get("https://api.example.com/search?q=hutool");

// POST request with form data
Map<String, Object> params = new HashMap<>();
params.put("username", "john");
params.put("password", "secret");
String postResponse = HttpUtil.post("https://api.example.com/login", params);
```

#### Advanced HTTP Requests
```java
// Using HttpRequest for more control
String advancedResponse = HttpRequest.get("https://api.example.com/data")
    .header("Authorization", "Bearer token123")
    .header("User-Agent", "MyApp/1.0")
    .timeout(5000) // 5 second timeout
    .execute()
    .body();

// POST with JSON body
String jsonResponse = HttpRequest.post("https://api.example.com/users")
    .header("Content-Type", "application/json")
    .body("{\"name\":\"John\",\"email\":\"john@example.com\"}")
    .execute()
    .body();
```

#### File Operations
```java
// Download file
HttpUtil.downloadFile("https://example.com/file.zip", new File("downloaded.zip"));

// Upload file
Map<String, Object> uploadParams = new HashMap<>();
uploadParams.put("file", new File("upload.txt"));
uploadParams.put("description", "Test file");
String uploadResponse = HttpUtil.post("https://api.example.com/upload", uploadParams);
```

## HTTP Request/Response Objects

### HttpRequest
```java
import cn.hutool.http.HttpRequest;
import cn.hutool.http.HttpResponse;

// Create request
HttpRequest request = HttpRequest.post("https://api.example.com/data")
    .header("Content-Type", "application/json")
    .header("Authorization", "Bearer token")
    .body("{\"key\":\"value\"}")
    .timeout(10000);

// Execute request
HttpResponse response = request.execute();
```

### HttpResponse
```java
// Get response information
int statusCode = response.getStatus(); // HTTP status code
String responseBody = response.body(); // Response body as string
byte[] responseBytes = response.bodyBytes(); // Response body as bytes
Map<String, List<String>> headers = response.headers(); // Response headers

// Check response status
boolean isOk = response.isOk(); // Status code 200-299
boolean isRedirect = response.isRedirect(); // Status code 300-399
```

---

# JSON Module (`hutool-json`)

The JSON module provides comprehensive JSON processing capabilities.

## JSON Utilities (`JSONUtil`)

### Key Features
- JSON parsing and generation
- Object to JSON conversion
- JSON to object conversion
- JSON validation and formatting

### Common Methods

#### JSON Creation
```java
import cn.hutool.json.JSONUtil;
import cn.hutool.json.JSONObject;
import cn.hutool.json.JSONArray;

// Create empty JSON objects
JSONObject obj = JSONUtil.createObj();
JSONArray array = JSONUtil.createArray();

// Parse JSON strings
JSONObject parsed = JSONUtil.parseObj("{\"name\":\"John\",\"age\":30}");
JSONArray parsedArray = JSONUtil.parseArray("[1,2,3,4,5]");
```

#### Object to JSON Conversion
```java
// Convert Java objects to JSON
User user = new User("John", 30, "john@example.com");
String jsonString = JSONUtil.toJsonStr(user);

// Convert with options
String prettyJson = JSONUtil.toJsonPrettyStr(user); // Pretty formatted

// Convert collections
List<User> users = Arrays.asList(new User("John", 30), new User("Jane", 25));
String usersJson = JSONUtil.toJsonStr(users);
```

#### JSON to Object Conversion
```java
// Parse JSON to objects
String jsonStr = "{\"name\":\"John\",\"age\":30,\"email\":\"john@example.com\"}";
User user = JSONUtil.toBean(jsonStr, User.class);

// Parse JSON array to list
String arrayJson = "[{\"name\":\"John\",\"age\":30},{\"name\":\"Jane\",\"age\":25}]";
List<User> users = JSONUtil.toList(arrayJson, User.class);

// Parse to Map
Map<String, Object> map = JSONUtil.parseObj(jsonStr).toMap();
```

## JSONObject Operations

### Basic Operations
```java
// Create and populate JSONObject
JSONObject json = new JSONObject();
json.put("name", "John");
json.put("age", 30);
json.put("active", true);

// Get values with type safety
String name = json.getStr("name");
Integer age = json.getInt("age");
Boolean active = json.getBool("active");

// Get with default values
String city = json.getStr("city", "Unknown");
int score = json.getInt("score", 0);
```

### Nested JSON Operations
```java
// Create nested JSON
JSONObject address = new JSONObject();
address.put("street", "123 Main St");
address.put("city", "New York");
address.put("zipCode", "10001");

JSONObject person = new JSONObject();
person.put("name", "John");
person.put("address", address);

// Access nested values
String street = person.getJSONObject("address").getStr("street");

// Set nested values using path
JSONObject complex = new JSONObject();
complex.putByPath("user.profile.name", "John");
complex.putByPath("user.profile.settings.theme", "dark");
```

## JSONArray Operations

### Basic Array Operations
```java
// Create and populate JSONArray
JSONArray array = new JSONArray();
array.put("apple");
array.put("banana");
array.put("orange");

// Access elements
String first = array.getStr(0); // "apple"
int size = array.size(); // 3

// Convert to list
List<String> fruits = array.toList(String.class);
```

### Array of Objects
```java
// Create array of JSON objects
JSONArray users = new JSONArray();
users.put(new JSONObject().put("name", "John").put("age", 30));
users.put(new JSONObject().put("name", "Jane").put("age", 25));

// Access object in array
JSONObject firstUser = users.getJSONObject(0);
String firstName = firstUser.getStr("name");
```

---

# Cryptography Module (`hutool-crypto`)

The crypto module provides comprehensive cryptographic utilities for encryption, decryption, and hashing.

## Security Utilities (`SecureUtil`)

### Key Features
- Symmetric encryption (AES, DES, 3DES)
- Asymmetric encryption (RSA)
- Hash algorithms (MD5, SHA family)
- HMAC algorithms
- Digital signatures

### Hash Algorithms

#### MD5 Hashing
```java
import cn.hutool.crypto.SecureUtil;

// MD5 hash of string
String md5 = SecureUtil.md5("Hello World"); // "b10a8db164e0754105b7a99be72e3fe5"

// MD5 hash of file
String fileMd5 = SecureUtil.md5(new File("document.pdf"));

// MD5 hash of bytes
byte[] data = "test data".getBytes();
String bytesMd5 = SecureUtil.md5(data);
```

#### SHA Hashing
```java
// SHA-1
String sha1 = SecureUtil.sha1("Hello World");

// SHA-256
String sha256 = SecureUtil.sha256("Hello World");

// SHA-512
String sha512 = SecureUtil.sha512("Hello World");
```

#### HMAC
```java
// HMAC with MD5
String hmacMd5 = SecureUtil.hmacMd5("secret-key").digestHex("Hello World");

// HMAC with SHA-256
String hmacSha256 = SecureUtil.hmacSha256("secret-key").digestHex("Hello World");
```

### Symmetric Encryption

#### AES Encryption
```java
// AES encryption with auto-generated key
AES aes = SecureUtil.aes();
String encrypted = aes.encryptHex("Hello World");
String decrypted = aes.decryptStr(encrypted);

// AES with custom key
String key = "MySecretKey12345"; // 16 bytes for AES-128
AES aesCustom = SecureUtil.aes(key.getBytes());
String encryptedCustom = aesCustom.encryptHex("Sensitive data");
String decryptedCustom = aesCustom.decryptStr(encryptedCustom);
```

#### DES Encryption
```java
// DES encryption
DES des = SecureUtil.des();
String desEncrypted = des.encryptHex("Hello World");
String desDecrypted = des.decryptStr(desEncrypted);

// DES with custom key
String desKey = "MyDesKey"; // 8 bytes for DES
DES desCustom = SecureUtil.des(desKey.getBytes());
```

### Asymmetric Encryption

#### RSA Encryption
```java
// Generate RSA key pair
KeyPair keyPair = SecureUtil.generateKeyPair("RSA");
RSA rsa = new RSA(keyPair.getPrivate(), keyPair.getPublic());

// Encrypt with public key, decrypt with private key
String encrypted = rsa.encryptHex("Hello World", KeyType.PublicKey);
String decrypted = rsa.decryptStr(encrypted, KeyType.PrivateKey);

// Sign and verify
String signature = rsa.sign("Hello World".getBytes(), SignAlgorithm.SHA256withRSA);
boolean verified = rsa.verify("Hello World".getBytes(), signature, SignAlgorithm.SHA256withRSA);
```

### Digital Signatures
```java
// Create signature
Sign sign = SecureUtil.sign(SignAlgorithm.SHA256withRSA);
byte[] signature = sign.sign("Hello World".getBytes());

// Verify signature
boolean isValid = sign.verify("Hello World".getBytes(), signature);
```

---

# Database Module (`hutool-db`)

The database module provides a lightweight ORM and database utilities.

## Database Utilities (`DbUtil` and `Db`)

### Key Features
- Database connection management
- SQL execution and query building
- Entity-based ORM operations
- Transaction support
- Multiple database dialect support

### Database Connection Setup

#### Using Properties File
Create a `db.setting` file in your classpath:
```properties
# Database configuration
url = jdbc:mysql://localhost:3306/mydb
user = username
pass = password
driver = com.mysql.jdbc.Driver

# Connection pool settings
initialSize = 5
maxActive = 20
maxWait = 60000
```

#### Programmatic Configuration
```java
import cn.hutool.db.Db;
import cn.hutool.db.Entity;
import cn.hutool.db.ds.simple.SimpleDataSource;

// Create data source
SimpleDataSource ds = new SimpleDataSource(
    "jdbc:mysql://localhost:3306/mydb",
    "username", 
    "password"
);

// Get database instance
Db db = Db.use(ds);
```

### Basic Database Operations

#### Query Operations
```java
// Query all records
List<Entity> users = db.findAll("user");

// Query with conditions
List<Entity> activeUsers = db.find("user", "status = ?", "active");

// Query single record
Entity user = db.findOne("user", "id = ?", 1);

// Count records
long userCount = db.count("user");
long activeCount = db.count("user", "status = ?", "active");
```

#### Insert Operations
```java
// Insert using Entity
Entity newUser = Entity.create("user")
    .set("name", "John Doe")
    .set("email", "john@example.com")
    .set("status", "active");

Long userId = db.insert(newUser);

// Insert multiple records
List<Entity> users = Arrays.asList(
    Entity.create("user").set("name", "Alice").set("email", "alice@example.com"),
    Entity.create("user").set("name", "Bob").set("email", "bob@example.com")
);
int[] results = db.insert(users);
```

#### Update Operations
```java
// Update records
int updated = db.update(
    Entity.create("user").set("status", "inactive"),
    "last_login < ?",
    DateUtil.offsetDay(new Date(), -30)
);

// Update single record
Entity userUpdate = Entity.create("user")
    .set("name", "John Smith")
    .set("email", "johnsmith@example.com");
    
int result = db.update(userUpdate, "id = ?", 1);
```

#### Delete Operations
```java
// Delete records
int deleted = db.del("user", "status = ?", "inactive");

// Delete single record
int result = db.del("user", "id = ?", 1);
```

### Advanced Database Operations

#### Transaction Support
```java
// Manual transaction
db.tx(new TxFunc() {
    @Override
    public void call() throws SQLException {
        // All operations in this block are in one transaction
        db.insert(Entity.create("user").set("name", "Alice"));
        db.update(Entity.create("account").set("balance", 1000), "user_id = ?", 1);
        
        // If any exception occurs, transaction will be rolled back
    }
});

// Lambda style transaction (Java 8+)
db.tx(() -> {
    db.insert(Entity.create("order").set("user_id", 1).set("amount", 99.99));
    db.update(Entity.create("inventory").set("quantity", "quantity - 1"), "product_id = ?", 100);
});
```

#### Custom SQL Execution
```java
// Execute custom SQL
List<Entity> results = db.query("SELECT u.*, p.name as profile_name FROM user u " +
    "LEFT JOIN profile p ON u.id = p.user_id WHERE u.status = ?", "active");

// Execute update SQL
int affected = db.execute("UPDATE user SET last_login = NOW() WHERE id = ?", 1);

// Call stored procedure
List<Entity> procResults = db.call("{call getUsersByStatus(?)}", "active");
```

#### Pagination
```java
import cn.hutool.db.Page;
import cn.hutool.db.PageResult;

// Paginated query
Page page = new Page(1, 10); // Page 1, 10 records per page
PageResult<Entity> pageResult = db.page("user", page, "status = ?", "active");

List<Entity> users = pageResult.getList();
int totalPages = pageResult.getTotalPage();
long totalCount = pageResult.getTotal();
```

### Entity Operations

#### Entity Creation and Manipulation
```java
// Create entity
Entity user = Entity.create("user");
user.set("name", "John");
user.set("age", 30);
user.set("email", "john@example.com");

// Get values
String name = user.getStr("name");
Integer age = user.getInt("age");
Date createdAt = user.getDate("created_at");

// Convert to Map
Map<String, Object> userMap = user.toMap();

// Convert from Map
Map<String, Object> data = new HashMap<>();
data.put("name", "Jane");
data.put("age", 25);
Entity fromMap = Entity.create("user").putAll(data);
```

---

# Additional Modules

## Cache Module (`hutool-cache`)

### Cache Utilities
```java
import cn.hutool.cache.CacheUtil;
import cn.hutool.cache.impl.FIFOCache;
import cn.hutool.cache.impl.LRUCache;

// Create LRU cache
LRUCache<String, String> lruCache = CacheUtil.newLRUCache(100); // Max 100 entries
lruCache.put("key1", "value1");
String value = lruCache.get("key1");

// Create FIFO cache
FIFOCache<String, Object> fifoCache = CacheUtil.newFIFOCache(50);
fifoCache.put("user:1", new User("John", 30));

// Timed cache
TimedCache<String, String> timedCache = CacheUtil.newTimedCache(5 * 60 * 1000); // 5 minutes
timedCache.put("session:abc123", "user_data");
```

## Cron Module (`hutool-cron`)

### Cron Job Scheduling
```java
import cn.hutool.cron.CronUtil;

// Start cron scheduler
CronUtil.start();

// Schedule tasks
CronUtil.schedule("*/5 * * * * *", new Runnable() {
    @Override
    public void run() {
        System.out.println("Task executed every 5 seconds");
    }
});

// Schedule with ID for later management
CronUtil.schedule("daily-report", "0 0 9 * * *", () -> {
    generateDailyReport();
});

// Remove scheduled task
CronUtil.remove("daily-report");
```

## POI Module (`hutool-poi`)

### Excel Operations
```java
import cn.hutool.poi.excel.ExcelUtil;
import cn.hutool.poi.excel.ExcelReader;
import cn.hutool.poi.excel.ExcelWriter;

// Read Excel file
ExcelReader reader = ExcelUtil.getReader(new File("data.xlsx"));
List<Map<String, Object>> rows = reader.readAll();

// Read with header row
List<User> users = reader.readAll(User.class);

// Write Excel file
ExcelWriter writer = ExcelUtil.getWriter(new File("output.xlsx"));
writer.addHeaderAlias("name", "Full Name");
writer.addHeaderAlias("age", "Age");
writer.addHeaderAlias("email", "Email Address");

List<User> userData = getUserData();
writer.write(userData);
writer.close();
```

## CAPTCHA Module (`hutool-captcha`)

### CAPTCHA Generation
```java
import cn.hutool.captcha.CaptchaUtil;
import cn.hutool.captcha.LineCaptcha;
import cn.hutool.captcha.CircleCaptcha;

// Line captcha
LineCaptcha lineCaptcha = CaptchaUtil.createLineCaptcha(200, 100, 4, 20);
String code = lineCaptcha.getCode(); // Get verification code
lineCaptcha.write(new File("captcha.png")); // Save to file

// Circle captcha
CircleCaptcha circleCaptcha = CaptchaUtil.createCircleCaptcha(200, 100, 4, 10);
boolean verified = circleCaptcha.verify("user_input_code");
```

---

# Best Practices and Examples

## Error Handling

### Exception Handling
```java
import cn.hutool.core.exceptions.UtilException;
import cn.hutool.http.HttpException;
import cn.hutool.db.DbRuntimeException;

try {
    // HTTP operation
    String response = HttpUtil.get("https://api.example.com/data");
    
    // Database operation
    List<Entity> users = db.findAll("user");
    
    // File operation
    String content = FileUtil.readUtf8String(new File("config.txt"));
    
} catch (HttpException e) {
    // Handle HTTP errors
    System.err.println("HTTP request failed: " + e.getMessage());
} catch (DbRuntimeException e) {
    // Handle database errors
    System.err.println("Database operation failed: " + e.getMessage());
} catch (UtilException e) {
    // Handle general utility errors
    System.err.println("Utility operation failed: " + e.getMessage());
}
```

## Configuration Management

### Using Setting Files
```java
import cn.hutool.setting.Setting;

// Load settings from classpath
Setting setting = new Setting("app.setting");

// Get configuration values
String dbUrl = setting.get("database.url");
int maxConnections = setting.getInt("database.maxConnections", 10);
boolean debugMode = setting.getBool("app.debug", false);

// Get grouped settings
Setting dbGroup = setting.getSetting("database");
String host = dbGroup.get("host");
int port = dbGroup.getInt("port");
```

## Logging Integration

### Log Utilities
```java
import cn.hutool.log.Log;
import cn.hutool.log.LogFactory;

// Get logger
Log log = LogFactory.get();

// Log messages
log.debug("Debug message with parameter: {}", value);
log.info("Operation completed successfully");
log.warn("Warning: Resource usage is high");
log.error("Error occurred", exception);

// Named logger
Log namedLog = LogFactory.get("MyService");
namedLog.info("Service started");
```

## Performance Tips

### Efficient String Operations
```java
// Use StrBuilder for multiple string operations
StrBuilder builder = StrUtil.strBuilder();
builder.append("Hello");
builder.append(" ");
builder.append("World");
String result = builder.toString();

// Reuse StringBuilder
StrBuilder reusable = StrUtil.strBuilder();
for (String item : items) {
    reusable.append(item).append(",");
}
```

### Efficient Collection Operations
```java
// Use appropriate collection utilities
List<String> filtered = CollUtil.filter(originalList, item -> item.length() > 5);
List<String> mapped = CollUtil.map(originalList, String::toUpperCase);

// Batch operations
List<List<String>> batches = CollUtil.split(largeList, 100);
for (List<String> batch : batches) {
    processBatch(batch);
}
```

---

# Migration and Integration Guide

## Migrating from Apache Commons

### String Operations
```java
// Apache Commons Lang
// StringUtils.isBlank(str) → StrUtil.isBlank(str)
// StringUtils.join(array, ",") → StrUtil.join(",", array)
// StringUtils.split(str, ",") → StrUtil.split(str, ",")

// Before (Apache Commons)
boolean blank = StringUtils.isBlank(input);
String joined = StringUtils.join(items, ",");

// After (Hutool)
boolean blank = StrUtil.isBlank(input);
String joined = StrUtil.join(",", items);
```

### File Operations
```java
// Apache Commons IO
// FileUtils.readFileToString(file, "UTF-8") → FileUtil.readUtf8String(file)
// FileUtils.writeStringToFile(file, content, "UTF-8") → FileUtil.writeUtf8String(content, file)

// Before (Apache Commons)
String content = FileUtils.readFileToString(file, StandardCharsets.UTF_8);
FileUtils.writeStringToFile(file, content, StandardCharsets.UTF_8);

// After (Hutool)
String content = FileUtil.readUtf8String(file);
FileUtil.writeUtf8String(content, file);
```

## Spring Integration

### Configuration
```java
@Configuration
public class HutoolConfig {
    
    @Bean
    public Db database() {
        // Configure Hutool database with Spring DataSource
        return Db.use(dataSource());
    }
    
    @Bean
    @Primary
    public DataSource dataSource() {
        return new SimpleDataSource(
            "jdbc:mysql://localhost:3306/mydb",
            "username",
            "password"
        );
    }
}
```

### Service Layer Integration
```java
@Service
public class UserService {
    
    @Autowired
    private Db db;
    
    public List<User> findActiveUsers() {
        List<Entity> entities = db.find("user", "status = ?", "active");
        return entities.stream()
            .map(this::entityToUser)
            .collect(Collectors.toList());
    }
    
    private User entityToUser(Entity entity) {
        User user = new User();
        user.setId(entity.getLong("id"));
        user.setName(entity.getStr("name"));
        user.setEmail(entity.getStr("email"));
        return user;
    }
}
```

---

# Troubleshooting

## Common Issues

### Character Encoding Issues
```java
// Ensure proper charset handling
String content = FileUtil.readString(file, CharsetUtil.UTF_8);
FileUtil.writeString(content, file, CharsetUtil.UTF_8);

// HTTP requests with encoding
String response = HttpRequest.get(url)
    .charset(CharsetUtil.UTF_8)
    .execute()
    .body();
```

### Database Connection Issues
```java
// Test database connection
try {
    Db db = Db.use();
    long count = db.count("user");
    System.out.println("Database connected successfully. User count: " + count);
} catch (Exception e) {
    System.err.println("Database connection failed: " + e.getMessage());
}
```

### Memory Management
```java
// Close resources properly
ExcelReader reader = null;
try {
    reader = ExcelUtil.getReader(new File("large_file.xlsx"));
    // Process data in chunks
    for (int i = 0; i < reader.getRowCount(); i += 1000) {
        List<Map<String, Object>> chunk = reader.read(i, i + 1000);
        processChunk(chunk);
    }
} finally {
    if (reader != null) {
        reader.close();
    }
}
```

## Performance Optimization

### Batch Operations
```java
// Batch database operations
List<Entity> batchInserts = new ArrayList<>();
for (int i = 0; i < 10000; i++) {
    batchInserts.add(Entity.create("user").set("name", "User" + i));
    
    // Insert in batches of 100
    if (batchInserts.size() >= 100) {
        db.insert(batchInserts);
        batchInserts.clear();
    }
}
// Insert remaining records
if (!batchInserts.isEmpty()) {
    db.insert(batchInserts);
}
```

### Connection Pooling
```java
// Use connection pooling for better performance
HikariDataSource dataSource = new HikariDataSource();
dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/mydb");
dataSource.setUsername("username");
dataSource.setPassword("password");
dataSource.setMaximumPoolSize(20);
dataSource.setMinimumIdle(5);

Db db = Db.use(dataSource);
```

---

# Conclusion

Hutool provides a comprehensive set of utilities that can significantly simplify Java development. Its consistent API design, extensive documentation, and active community make it an excellent choice for both new and experienced Java developers.

## Key Benefits

1. **Consistency**: All utilities follow similar naming conventions and patterns
2. **Comprehensive**: Covers most common programming tasks
3. **Performance**: Optimized implementations with minimal overhead
4. **Lightweight**: Minimal dependencies, can be used partially
5. **Well-tested**: Extensive unit test coverage
6. **Active Development**: Regular updates and bug fixes

## Getting Help

- **Official Documentation**: [https://www.hutool.cn/docs/](https://www.hutool.cn/docs/)
- **API Reference**: [https://apidoc.gitee.com/loolly/hutool/](https://apidoc.gitee.com/loolly/hutool/)
- **GitHub Issues**: [https://github.com/looly/hutool/issues](https://github.com/looly/hutool/issues)
- **Gitee Issues**: [https://gitee.com/loolly/hutool/issues](https://gitee.com/loolly/hutool/issues)

## Contributing

Contributions are welcome! Please read the contribution guidelines in the project repository and submit pull requests to the `v4-dev` branch.

---

*This documentation covers the major features and APIs of Hutool. For the most up-to-date information and advanced features, please refer to the official documentation and API reference.*