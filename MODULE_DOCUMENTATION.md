# Hutool Module Documentation

This document provides detailed documentation for all Hutool modules with comprehensive examples and API references.

---

# HTTP Module (`hutool-http`)

The HTTP module provides a powerful and easy-to-use HTTP client for making web requests.

## Key Classes

- `HttpUtil` - Static utility methods for simple HTTP operations
- `HttpRequest` - Fluent API for building HTTP requests
- `HttpResponse` - Represents HTTP response with status, headers, and body
- `HttpConnection` - Low-level HTTP connection wrapper

## HttpUtil Static Methods

### Simple GET Requests

```java
import cn.hutool.http.HttpUtil;

// Basic GET request
String response = HttpUtil.get("https://api.example.com/users");

// GET with parameters
Map<String, Object> params = new HashMap<>();
params.put("page", 1);
params.put("size", 10);
String response = HttpUtil.get("https://api.example.com/users", params);

// GET with custom charset
String response = HttpUtil.get("https://api.example.com/data", CharsetUtil.UTF_8);
```

### Simple POST Requests

```java
// POST with form data
Map<String, Object> formData = new HashMap<>();
formData.put("username", "john");
formData.put("password", "secret123");
String response = HttpUtil.post("https://api.example.com/login", formData);

// POST with JSON body
String jsonBody = "{\"name\":\"John\",\"email\":\"john@example.com\"}";
String response = HttpUtil.post("https://api.example.com/users", jsonBody);
```

### File Operations

```java
// Download file
HttpUtil.downloadFile("https://example.com/document.pdf", new File("document.pdf"));

// Download with progress callback
HttpUtil.downloadFile("https://example.com/large-file.zip", new File("large-file.zip"), 
    new StreamProgress() {
        @Override
        public void start() {
            System.out.println("Download started");
        }
        
        @Override
        public void progress(long progressSize) {
            System.out.println("Downloaded: " + progressSize + " bytes");
        }
        
        @Override
        public void finish() {
            System.out.println("Download completed");
        }
    });

// Upload file
Map<String, Object> uploadParams = new HashMap<>();
uploadParams.put("file", new File("upload.txt"));
uploadParams.put("description", "Test file upload");
String response = HttpUtil.post("https://api.example.com/upload", uploadParams);
```

## HttpRequest Fluent API

### Basic Request Building

```java
import cn.hutool.http.HttpRequest;
import cn.hutool.http.HttpResponse;

// GET request with headers
HttpResponse response = HttpRequest.get("https://api.example.com/data")
    .header("Authorization", "Bearer token123")
    .header("User-Agent", "MyApp/1.0")
    .header("Accept", "application/json")
    .execute();

// POST request with JSON body
HttpResponse response = HttpRequest.post("https://api.example.com/users")
    .header("Content-Type", "application/json")
    .body("{\"name\":\"John\",\"email\":\"john@example.com\"}")
    .execute();

// PUT request with form data
HttpResponse response = HttpRequest.put("https://api.example.com/users/123")
    .form("name", "John Smith")
    .form("email", "johnsmith@example.com")
    .execute();
```

### Advanced Configuration

```java
// Request with timeout and retry
HttpResponse response = HttpRequest.get("https://api.example.com/data")
    .timeout(10000) // 10 second timeout
    .setConnectionTimeout(5000) // 5 second connection timeout
    .setReadTimeout(15000) // 15 second read timeout
    .execute();

// Request with proxy
HttpResponse response = HttpRequest.get("https://api.example.com/data")
    .setProxy("proxy.example.com", 8080)
    .execute();

// Request with custom SSL configuration
HttpResponse response = HttpRequest.get("https://secure-api.example.com/data")
    .setSSLSocketFactory(SSLSocketFactoryBuilder.create().build())
    .setHostnameVerifier(new TrustAnyHostnameVerifier())
    .execute();
```

### Cookie Handling

```java
// Request with cookies
HttpResponse response = HttpRequest.get("https://api.example.com/data")
    .cookie("sessionId", "abc123")
    .cookie("userId", "456")
    .execute();

// Use cookie store for session management
ThreadLocalCookieStore cookieStore = new ThreadLocalCookieStore();
HttpGlobalConfig.setCookieStore(cookieStore);

// First request - login
HttpRequest.post("https://api.example.com/login")
    .form("username", "john")
    .form("password", "secret")
    .execute();

// Second request - cookies automatically included
HttpResponse response = HttpRequest.get("https://api.example.com/profile")
    .execute();
```

## HttpResponse Handling

```java
HttpResponse response = HttpRequest.get("https://api.example.com/data").execute();

// Get response status
int statusCode = response.getStatus();
boolean isOk = response.isOk(); // 200-299
boolean isRedirect = response.isRedirect(); // 300-399
boolean isError = response.isError(); // 400+

// Get response body
String bodyText = response.body();
byte[] bodyBytes = response.bodyBytes();
InputStream bodyStream = response.bodyStream();

// Get response headers
String contentType = response.header("Content-Type");
Map<String, List<String>> allHeaders = response.headers();

// Get cookies from response
String sessionCookie = response.getCookie("JSESSIONID");
List<HttpCookie> allCookies = response.getCookies();
```

## Advanced HTTP Features

### Custom Headers and User Agents

```java
public class ApiClient {
    private static final String USER_AGENT = "MyApp/1.0 (compatible; HttpUtil)";
    private static final String API_KEY = "your-api-key";
    
    public JSONObject getUser(String userId) {
        HttpResponse response = HttpRequest.get("https://api.example.com/users/" + userId)
            .header("Authorization", "Bearer " + API_KEY)
            .header("User-Agent", USER_AGENT)
            .header("Accept", "application/json")
            .header("X-Request-ID", IdUtil.fastSimpleUUID())
            .execute();
        
        if (response.isOk()) {
            return JSONUtil.parseObj(response.body());
        } else {
            throw new RuntimeException("API request failed: " + response.getStatus());
        }
    }
}
```

### Request/Response Interceptors

```java
public class HttpInterceptor {
    
    public static HttpRequest addCommonHeaders(HttpRequest request) {
        return request
            .header("X-Client-Version", "1.0.0")
            .header("X-Request-Time", String.valueOf(System.currentTimeMillis()))
            .header("User-Agent", "MyApp/1.0");
    }
    
    public static void logResponse(HttpResponse response) {
        System.out.println(StrUtil.format("Response: {} {} - {} bytes",
            response.getStatus(),
            response.getStatusMessage(),
            response.body().length()));
    }
    
    // Usage example
    public String makeRequest(String url) {
        HttpRequest request = HttpRequest.get(url);
        request = addCommonHeaders(request);
        
        HttpResponse response = request.execute();
        logResponse(response);
        
        return response.body();
    }
}
```

---

# JSON Module (`hutool-json`)

The JSON module provides comprehensive JSON processing capabilities with a simple and intuitive API.

## Key Classes

- `JSONUtil` - Static utility methods for JSON operations
- `JSONObject` - Represents a JSON object (key-value pairs)
- `JSONArray` - Represents a JSON array
- `JSONConfig` - Configuration for JSON processing

## JSONUtil Static Methods

### Creating JSON Objects

```java
import cn.hutool.json.JSONUtil;
import cn.hutool.json.JSONObject;
import cn.hutool.json.JSONArray;

// Create empty JSON objects
JSONObject obj = JSONUtil.createObj();
JSONArray array = JSONUtil.createArray();

// Parse JSON strings
JSONObject user = JSONUtil.parseObj("{\"name\":\"John\",\"age\":30,\"active\":true}");
JSONArray numbers = JSONUtil.parseArray("[1,2,3,4,5]");

// Parse from file
JSONObject config = JSONUtil.readJSONObject(new File("config.json"), CharsetUtil.UTF_8);
JSONArray data = JSONUtil.readJSONArray(new File("data.json"), CharsetUtil.UTF_8);
```

### Object to JSON Conversion

```java
// Convert Java objects to JSON
public class User {
    private String name;
    private int age;
    private String email;
    // constructors, getters, setters...
}

User user = new User("John", 30, "john@example.com");

// Convert to JSON string
String jsonString = JSONUtil.toJsonStr(user);
// Result: {"name":"John","age":30,"email":"john@example.com"}

// Convert to pretty JSON string
String prettyJson = JSONUtil.toJsonPrettyStr(user);
// Result: formatted with indentation and line breaks

// Convert collections
List<User> users = Arrays.asList(
    new User("John", 30, "john@example.com"),
    new User("Jane", 25, "jane@example.com")
);
String usersJson = JSONUtil.toJsonStr(users);
```

### JSON to Object Conversion

```java
// Parse JSON to Java objects
String jsonStr = "{\"name\":\"John\",\"age\":30,\"email\":\"john@example.com\"}";
User user = JSONUtil.toBean(jsonStr, User.class);

// Parse JSON array to List
String arrayJson = "[{\"name\":\"John\",\"age\":30},{\"name\":\"Jane\",\"age\":25}]";
List<User> users = JSONUtil.toList(arrayJson, User.class);

// Parse to generic types
Type listType = new TypeReference<List<User>>(){}.getType();
List<User> typedUsers = JSONUtil.toBean(arrayJson, listType);

// Parse to Map
Map<String, Object> userMap = JSONUtil.parseObj(jsonStr).toMap();
```

## JSONObject Operations

### Basic Object Operations

```java
// Create and populate JSONObject
JSONObject person = JSONUtil.createObj();
person.put("name", "John Doe");
person.put("age", 30);
person.put("email", "john@example.com");
person.put("active", true);
person.put("salary", 75000.50);

// Get values with type safety
String name = person.getStr("name");
Integer age = person.getInt("age");
String email = person.getStr("email");
Boolean active = person.getBool("active");
Double salary = person.getDouble("salary");

// Get with default values
String city = person.getStr("city", "Unknown");
int experience = person.getInt("experience", 0);
boolean verified = person.getBool("verified", false);
```

### Nested JSON Operations

```java
// Create nested JSON structure
JSONObject address = JSONUtil.createObj()
    .put("street", "123 Main St")
    .put("city", "New York")
    .put("state", "NY")
    .put("zipCode", "10001");

JSONObject person = JSONUtil.createObj()
    .put("name", "John Doe")
    .put("age", 30)
    .put("address", address);

// Access nested values
String street = person.getJSONObject("address").getStr("street");
String city = person.getJSONObject("address").getStr("city");

// Set nested values using path notation
JSONObject complex = JSONUtil.createObj();
complex.putByPath("user.profile.name", "John");
complex.putByPath("user.profile.settings.theme", "dark");
complex.putByPath("user.profile.settings.notifications", true);

// Get nested values using path notation
String theme = complex.getByPath("user.profile.settings.theme", String.class);
```

### JSON Object Manipulation

```java
JSONObject user = JSONUtil.parseObj("{\"name\":\"John\",\"age\":30,\"city\":\"NYC\"}");

// Check for keys
boolean hasName = user.containsKey("name");
boolean hasEmail = user.containsKey("email");

// Remove keys
user.remove("city");

// Get all keys
Set<String> keys = user.keySet();

// Get size
int size = user.size();

// Check if empty
boolean isEmpty = user.isEmpty();

// Clear all data
user.clear();

// Clone object
JSONObject clone = user.clone();

// Convert to different formats
Map<String, Object> map = user.toMap();
String jsonString = user.toString();
String prettyString = user.toJSONString(4); // 4-space indentation
```

## JSONArray Operations

### Basic Array Operations

```java
// Create and populate JSONArray
JSONArray fruits = JSONUtil.createArray();
fruits.put("apple");
fruits.put("banana");
fruits.put("orange");

// Add objects
JSONArray users = JSONUtil.createArray();
users.put(JSONUtil.createObj().put("name", "John").put("age", 30));
users.put(JSONUtil.createObj().put("name", "Jane").put("age", 25));

// Access elements
String firstFruit = fruits.getStr(0); // "apple"
JSONObject firstUser = users.getJSONObject(0);

// Get array size
int fruitCount = fruits.size();

// Check if empty
boolean isEmpty = fruits.isEmpty();
```

### Array Manipulation

```java
JSONArray numbers = JSONUtil.createArray();
numbers.put(1).put(2).put(3).put(4).put(5);

// Insert at specific position
numbers.put(2, 99); // Insert 99 at index 2

// Remove element
numbers.remove(1); // Remove element at index 1

// Convert to List
List<Integer> numberList = numbers.toList(Integer.class);
List<Object> objectList = numbers.toList(Object.class);

// Iterate through array
for (int i = 0; i < numbers.size(); i++) {
    Integer num = numbers.getInt(i);
    System.out.println("Number: " + num);
}

// Stream operations (if using Java 8+)
numbers.stream()
    .mapToInt(obj -> (Integer) obj)
    .filter(num -> num > 2)
    .forEach(System.out::println);
```

## Advanced JSON Features

### Custom JSON Configuration

```java
import cn.hutool.json.JSONConfig;

// Create custom JSON configuration
JSONConfig config = new JSONConfig();
config.setIgnoreNullValue(true); // Ignore null values
config.setDateFormat("yyyy-MM-dd HH:mm:ss"); // Custom date format
config.setOrder(true); // Maintain insertion order

// Use configuration with JSONUtil
User user = new User("John", null, "john@example.com");
String json = JSONUtil.toJsonStr(user, config);
// Result: null email field will be omitted
```

### Working with Complex Objects

```java
public class ComplexObject {
    private String id;
    private List<String> tags;
    private Map<String, Object> metadata;
    private Date createdAt;
    private List<NestedObject> children;
    
    // constructors, getters, setters...
}

public class NestedObject {
    private String name;
    private int value;
    // constructors, getters, setters...
}

// Convert complex object to JSON
ComplexObject complex = new ComplexObject();
complex.setId("obj-123");
complex.setTags(Arrays.asList("tag1", "tag2", "tag3"));
complex.setMetadata(Map.of("key1", "value1", "key2", 42));
complex.setCreatedAt(new Date());
complex.setChildren(Arrays.asList(
    new NestedObject("child1", 100),
    new NestedObject("child2", 200)
));

String json = JSONUtil.toJsonPrettyStr(complex);
System.out.println(json);

// Parse back to object
ComplexObject parsed = JSONUtil.toBean(json, ComplexObject.class);
```

### JSON Validation and Error Handling

```java
public class JSONValidator {
    
    public static boolean isValidJSON(String jsonString) {
        try {
            JSONUtil.parseObj(jsonString);
            return true;
        } catch (Exception e) {
            try {
                JSONUtil.parseArray(jsonString);
                return true;
            } catch (Exception e2) {
                return false;
            }
        }
    }
    
    public static Optional<JSONObject> safeParseObject(String jsonString) {
        try {
            return Optional.of(JSONUtil.parseObj(jsonString));
        } catch (Exception e) {
            System.err.println("Failed to parse JSON object: " + e.getMessage());
            return Optional.empty();
        }
    }
    
    public static JSONObject parseWithDefaults(String jsonString, JSONObject defaults) {
        try {
            JSONObject parsed = JSONUtil.parseObj(jsonString);
            
            // Merge with defaults
            for (String key : defaults.keySet()) {
                if (!parsed.containsKey(key)) {
                    parsed.put(key, defaults.get(key));
                }
            }
            
            return parsed;
        } catch (Exception e) {
            System.err.println("Failed to parse JSON, using defaults: " + e.getMessage());
            return defaults.clone();
        }
    }
}
```

---

# Crypto Module (`hutool-crypto`)

The crypto module provides comprehensive cryptographic utilities for hashing, encryption, and digital signatures.

## Key Classes

- `SecureUtil` - Static utility methods for cryptographic operations
- `DigestUtil` - Hash and digest utilities
- `SymmetricCrypto` - Symmetric encryption (AES, DES, etc.)
- `AsymmetricCrypto` - Asymmetric encryption (RSA, etc.)
- `Sign` - Digital signature operations

## Hash Algorithms

### MD5 Hashing

```java
import cn.hutool.crypto.SecureUtil;
import cn.hutool.crypto.digest.MD5;

// Simple MD5 hash
String md5Hash = SecureUtil.md5("Hello World");
// Result: "b10a8db164e0754105b7a99be72e3fe5"

// MD5 hash of file
String fileMd5 = SecureUtil.md5(new File("document.pdf"));

// MD5 hash of byte array
byte[] data = "sensitive data".getBytes();
String dataMd5 = SecureUtil.md5(data);

// Using MD5 class for more control
MD5 md5 = new MD5();
md5.update("Hello ".getBytes());
md5.update("World".getBytes());
String result = md5.digestHex();
```

### SHA Family Hashing

```java
// SHA-1
String sha1 = SecureUtil.sha1("Hello World");

// SHA-256
String sha256 = SecureUtil.sha256("Hello World");

// SHA-512
String sha512 = SecureUtil.sha512("Hello World");

// SHA-3 (if available)
String sha3 = SecureUtil.sha3_256("Hello World");

// Hash file with SHA-256
String fileSha256 = SecureUtil.sha256(new File("important-file.txt"));

// Custom salt with SHA-256
String saltedHash = SecureUtil.sha256("password" + "randomSalt");
```

### HMAC (Hash-based Message Authentication Code)

```java
import cn.hutool.crypto.digest.HMac;
import cn.hutool.crypto.digest.HmacAlgorithm;

// HMAC with MD5
String hmacMd5 = SecureUtil.hmacMd5("secret-key").digestHex("Hello World");

// HMAC with SHA-256
String hmacSha256 = SecureUtil.hmacSha256("secret-key").digestHex("Hello World");

// HMAC with custom algorithm
HMac hmac = new HMac(HmacAlgorithm.HmacSHA512, "my-secret-key".getBytes());
String hmacResult = hmac.digestHex("data to authenticate");

// Verify HMAC
boolean isValid = hmacResult.equals(hmac.digestHex("data to authenticate"));
```

## Symmetric Encryption

### AES Encryption

```java
import cn.hutool.crypto.symmetric.AES;
import cn.hutool.crypto.symmetric.SymmetricAlgorithm;
import cn.hutool.crypto.symmetric.SymmetricCrypto;

// AES with auto-generated key
AES aes = SecureUtil.aes();
String plaintext = "This is sensitive information";

String encrypted = aes.encryptHex(plaintext);
String decrypted = aes.decryptStr(encrypted);

// AES with custom key (16, 24, or 32 bytes for AES-128, AES-192, AES-256)
String customKey = "MySecretKey12345"; // 16 bytes for AES-128
AES aesCustom = SecureUtil.aes(customKey.getBytes());

String encryptedCustom = aesCustom.encryptHex("Secret message");
String decryptedCustom = aesCustom.decryptStr(encryptedCustom);

// AES with custom key and IV
byte[] key = "MySecretKey12345".getBytes(); // 16 bytes
byte[] iv = "MyInitVector1234".getBytes();  // 16 bytes
AES aesWithIV = new AES(Mode.CBC, Padding.PKCS5Padding, key, iv);

String encryptedWithIV = aesWithIV.encryptHex("Data with IV");
String decryptedWithIV = aesWithIV.decryptStr(encryptedWithIV);
```

### DES and 3DES Encryption

```java
import cn.hutool.crypto.symmetric.DES;
import cn.hutool.crypto.symmetric.DESede;

// DES encryption (8-byte key)
DES des = SecureUtil.des("MyDesKey".getBytes()); // 8 bytes
String desEncrypted = des.encryptHex("Hello DES");
String desDecrypted = des.decryptStr(desEncrypted);

// 3DES (Triple DES) encryption (24-byte key)
DESede desede = SecureUtil.desede("MyTripleDesKey1234567890".getBytes()); // 24 bytes
String desedeEncrypted = desede.encryptHex("Hello 3DES");
String desedeDecrypted = desede.decryptStr(desedeEncrypted);

// Generic symmetric crypto
SymmetricCrypto crypto = new SymmetricCrypto(SymmetricAlgorithm.AES, "MySecretKey12345".getBytes());
String genericEncrypted = crypto.encryptHex("Generic encryption");
String genericDecrypted = crypto.decryptStr(genericEncrypted);
```

### RC4 Stream Cipher

```java
import cn.hutool.crypto.symmetric.RC4;

// RC4 encryption
RC4 rc4 = new RC4("MyRC4Key");
String rc4Encrypted = rc4.encryptHex("Stream cipher data");
String rc4Decrypted = rc4.decryptStr(rc4Encrypted);
```

## Asymmetric Encryption

### RSA Encryption

```java
import cn.hutool.crypto.asymmetric.KeyType;
import cn.hutool.crypto.asymmetric.RSA;
import java.security.KeyPair;

// Generate RSA key pair
KeyPair keyPair = SecureUtil.generateKeyPair("RSA");
RSA rsa = new RSA(keyPair.getPrivate(), keyPair.getPublic());

// Encrypt with public key, decrypt with private key
String plaintext = "This is a secret message";
String encrypted = rsa.encryptHex(plaintext, KeyType.PublicKey);
String decrypted = rsa.decryptStr(encrypted, KeyType.PrivateKey);

// Encrypt with private key, decrypt with public key (for digital signatures)
String signatureEncrypted = rsa.encryptHex(plaintext, KeyType.PrivateKey);
String signatureDecrypted = rsa.decryptStr(signatureEncrypted, KeyType.PublicKey);

// RSA with custom key size
KeyPair largeKeyPair = SecureUtil.generateKeyPair("RSA", 2048);
RSA rsaLarge = new RSA(largeKeyPair.getPrivate(), largeKeyPair.getPublic());
```

### Working with Key Files

```java
// Save keys to files
RSA rsa = new RSA();
String privateKeyBase64 = rsa.getPrivateKeyBase64();
String publicKeyBase64 = rsa.getPublicKeyBase64();

FileUtil.writeUtf8String(privateKeyBase64, new File("private.key"));
FileUtil.writeUtf8String(publicKeyBase64, new File("public.key"));

// Load keys from files
String privateKeyStr = FileUtil.readUtf8String(new File("private.key"));
String publicKeyStr = FileUtil.readUtf8String(new File("public.key"));

RSA rsaFromFiles = new RSA(privateKeyStr, publicKeyStr);
```

## Digital Signatures

### RSA Signatures

```java
import cn.hutool.crypto.asymmetric.Sign;
import cn.hutool.crypto.asymmetric.SignAlgorithm;

// Create RSA signature
RSA rsa = new RSA();
String data = "Important document content";

// Sign data
String signature = rsa.sign(data.getBytes(), SignAlgorithm.SHA256withRSA);

// Verify signature
boolean isValid = rsa.verify(data.getBytes(), signature, SignAlgorithm.SHA256withRSA);

// Using Sign class for more algorithms
Sign sign = SecureUtil.sign(SignAlgorithm.SHA256withRSA);
byte[] signatureBytes = sign.sign(data.getBytes());
boolean verified = sign.verify(data.getBytes(), signatureBytes);
```

### DSA Signatures

```java
// DSA signature
Sign dsaSign = SecureUtil.sign(SignAlgorithm.SHA1withDSA);
byte[] dsaSignature = dsaSign.sign("Document to sign".getBytes());
boolean dsaValid = dsaSign.verify("Document to sign".getBytes(), dsaSignature);
```

## Advanced Cryptographic Features

### Password Hashing with BCrypt

```java
import cn.hutool.crypto.digest.BCrypt;

// Hash password
String password = "userPassword123";
String hashedPassword = BCrypt.hashpw(password, BCrypt.gensalt());

// Verify password
boolean isCorrect = BCrypt.checkpw(password, hashedPassword);
boolean isWrong = BCrypt.checkpw("wrongPassword", hashedPassword);

// Custom salt rounds (higher = more secure but slower)
String strongHash = BCrypt.hashpw(password, BCrypt.gensalt(12));
```

### Key Derivation

```java
import cn.hutool.crypto.KeyUtil;
import javax.crypto.SecretKey;

// Generate key from password (PBKDF2)
String password = "userPassword";
byte[] salt = SecureUtil.generateKey("AES", 16).getEncoded(); // Random salt

SecretKey derivedKey = KeyUtil.generateKey("PBKDF2WithHmacSHA256", 
    password.toCharArray(), salt, 10000, 256);

// Use derived key for encryption
AES aes = new AES(derivedKey.getEncoded());
String encrypted = aes.encryptHex("Sensitive data");
```

### Secure Random Generation

```java
import cn.hutool.core.util.RandomUtil;

// Generate secure random bytes
byte[] randomBytes = SecureUtil.generateKey("AES").getEncoded();

// Generate random hex string
String randomHex = RandomUtil.randomString("0123456789ABCDEF", 32);

// Generate random password
String randomPassword = RandomUtil.randomString(
    "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789!@#$%^&*", 
    16);
```

---

# Database Module (`hutool-db`)

The database module provides a lightweight ORM and comprehensive database utilities.

## Key Classes

- `Db` - Main database operations class
- `Entity` - Represents a database record
- `SqlExecutor` - Low-level SQL execution
- `Page` - Pagination support
- `DbUtil` - Database utility methods

## Database Configuration

### Configuration File Setup

Create `db.setting` in your classpath:

```properties
# Database connection settings
url = jdbc:mysql://localhost:3306/myapp
user = dbuser
pass = dbpassword
driver = com.mysql.cj.jdbc.Driver

# Connection pool settings
initialSize = 5
maxActive = 20
maxWait = 60000
minIdle = 5
maxIdle = 10

# Validation settings
validationQuery = SELECT 1
testWhileIdle = true
testOnBorrow = false
testOnReturn = false
```

### Programmatic Configuration

```java
import cn.hutool.db.Db;
import cn.hutool.db.ds.simple.SimpleDataSource;
import cn.hutool.db.ds.pooled.PooledDataSource;

// Simple data source
SimpleDataSource simpleDs = new SimpleDataSource(
    "jdbc:mysql://localhost:3306/myapp",
    "dbuser", 
    "dbpassword"
);

Db db = Db.use(simpleDs);

// Pooled data source
PooledDataSource pooledDs = new PooledDataSource(
    "jdbc:mysql://localhost:3306/myapp",
    "dbuser",
    "dbpassword"
);
pooledDs.setMaxActive(20);
pooledDs.setInitialSize(5);

Db pooledDb = Db.use(pooledDs);
```

## Basic CRUD Operations

### Create (Insert)

```java
import cn.hutool.db.Entity;
import cn.hutool.core.date.DateUtil;

// Insert single record
Entity newUser = Entity.create("users")
    .set("name", "John Doe")
    .set("email", "john@example.com")
    .set("age", 30)
    .set("status", "active")
    .set("created_at", DateUtil.date());

Long userId = db.insert(newUser);
System.out.println("Created user with ID: " + userId);

// Insert multiple records
List<Entity> users = Arrays.asList(
    Entity.create("users").set("name", "Alice").set("email", "alice@example.com").set("age", 25),
    Entity.create("users").set("name", "Bob").set("email", "bob@example.com").set("age", 35),
    Entity.create("users").set("name", "Carol").set("email", "carol@example.com").set("age", 28)
);

int[] results = db.insert(users);
System.out.println("Inserted " + results.length + " users");
```

### Read (Select)

```java
// Find all records
List<Entity> allUsers = db.findAll("users");

// Find with conditions
List<Entity> activeUsers = db.find("users", "status = ?", "active");
List<Entity> youngUsers = db.find("users", "age < ? AND status = ?", 30, "active");

// Find single record
Entity user = db.findOne("users", "id = ?", 1);
Entity userByEmail = db.findOne("users", "email = ?", "john@example.com");

// Find with ordering
List<Entity> usersByAge = db.find("users", "ORDER BY age DESC");
List<Entity> recentUsers = db.find("users", "status = ? ORDER BY created_at DESC LIMIT 10", "active");

// Count records
long totalUsers = db.count("users");
long activeCount = db.count("users", "status = ?", "active");
```

### Update

```java
// Update records with conditions
int updatedCount = db.update(
    Entity.create("users").set("status", "inactive").set("updated_at", DateUtil.date()),
    "last_login < ?",
    DateUtil.offsetDay(DateUtil.date(), -30) // 30 days ago
);

// Update single record by ID
Entity userUpdate = Entity.create("users")
    .set("name", "John Smith")
    .set("email", "johnsmith@example.com")
    .set("updated_at", DateUtil.date());

int result = db.update(userUpdate, "id = ?", 1);

// Update with SQL
int affected = db.execute("UPDATE users SET last_login = NOW() WHERE id = ?", 1);
```

### Delete

```java
// Delete records with conditions
int deletedCount = db.del("users", "status = ? AND last_login < ?", 
    "inactive", DateUtil.offsetDay(DateUtil.date(), -90));

// Delete single record
int result = db.del("users", "id = ?", 1);

// Delete all records (be careful!)
int allDeleted = db.del("users");
```

## Advanced Database Operations

### Transactions

```java
import cn.hutool.db.transaction.TxFunc;

// Manual transaction
db.tx(new TxFunc() {
    @Override
    public void call() throws SQLException {
        // All operations in this block are in one transaction
        
        // Transfer money between accounts
        db.execute("UPDATE accounts SET balance = balance - ? WHERE id = ?", 100.0, 1);
        db.execute("UPDATE accounts SET balance = balance + ? WHERE id = ?", 100.0, 2);
        
        // Log transaction
        db.insert(Entity.create("transactions")
            .set("from_account", 1)
            .set("to_account", 2)
            .set("amount", 100.0)
            .set("timestamp", DateUtil.date()));
        
        // If any exception occurs, transaction will be rolled back
    }
});

// Lambda style transaction (Java 8+)
db.tx(() -> {
    Long orderId = db.insert(Entity.create("orders")
        .set("user_id", 123)
        .set("total", 99.99)
        .set("status", "pending"));
    
    db.insert(Entity.create("order_items")
        .set("order_id", orderId)
        .set("product_id", 456)
        .set("quantity", 2)
        .set("price", 49.99));
    
    db.execute("UPDATE products SET stock = stock - ? WHERE id = ?", 2, 456);
});
```

### Pagination

```java
import cn.hutool.db.Page;
import cn.hutool.db.PageResult;

// Basic pagination
Page page = new Page(1, 10); // Page 1, 10 records per page
PageResult<Entity> pageResult = db.page("users", page);

List<Entity> users = pageResult.getList();
int totalPages = pageResult.getTotalPage();
long totalCount = pageResult.getTotal();

System.out.println(StrUtil.format("Page 1 of {}, {} total users", totalPages, totalCount));

// Pagination with conditions
Page searchPage = new Page(2, 5); // Page 2, 5 records per page
PageResult<Entity> searchResult = db.page("users", searchPage, "status = ? AND age > ?", "active", 25);

// Pagination with custom SQL
PageResult<Entity> customResult = db.page(
    "SELECT u.*, p.name as profile_name FROM users u LEFT JOIN profiles p ON u.id = p.user_id",
    searchPage,
    "WHERE u.status = ?",
    "active"
);
```

### Custom SQL Queries

```java
// Execute custom SELECT queries
List<Entity> results = db.query(
    "SELECT u.name, u.email, COUNT(o.id) as order_count " +
    "FROM users u LEFT JOIN orders o ON u.id = o.user_id " +
    "WHERE u.status = ? " +
    "GROUP BY u.id " +
    "HAVING order_count > ?",
    "active", 5
);

// Execute stored procedures
List<Entity> procResults = db.call("{call getUsersByStatus(?)}", "active");

// Execute custom UPDATE/DELETE
int affected = db.execute(
    "UPDATE users SET last_login = NOW() " +
    "WHERE id IN (SELECT user_id FROM sessions WHERE active = ?)",
    true
);

// Batch operations
List<Object[]> batchParams = Arrays.asList(
    new Object[]{"John", "john@example.com", 30},
    new Object[]{"Jane", "jane@example.com", 25},
    new Object[]{"Bob", "bob@example.com", 35}
);

int[] batchResults = db.executeBatch(
    "INSERT INTO users (name, email, age) VALUES (?, ?, ?)",
    batchParams
);
```

### Working with Entity Objects

```java
// Create entity from Map
Map<String, Object> userData = new HashMap<>();
userData.put("name", "John Doe");
userData.put("email", "john@example.com");
userData.put("age", 30);

Entity user = Entity.create("users").putAll(userData);

// Entity manipulation
Entity existingUser = db.findOne("users", "id = ?", 1);

// Get values with type conversion
String name = existingUser.getStr("name");
Integer age = existingUser.getInt("age");
Date createdAt = existingUser.getDate("created_at");
Boolean isActive = existingUser.getBool("active", false); // with default

// Modify entity
existingUser.set("last_login", DateUtil.date());
existingUser.set("login_count", existingUser.getInt("login_count", 0) + 1);

// Update in database
db.update(existingUser, "id = ?", existingUser.getLong("id"));

// Convert entity to different formats
Map<String, Object> userMap = existingUser.toMap();
String userJson = JSONUtil.toJsonStr(existingUser.toMap());
```

## Database Schema Operations

### Table Management

```java
import cn.hutool.db.meta.MetaUtil;
import cn.hutool.db.meta.Table;
import cn.hutool.db.meta.Column;

// Get table metadata
Table userTable = MetaUtil.getTableMeta(db.getDataSource(), "users");
System.out.println("Table: " + userTable.getTableName());
System.out.println("Columns: " + userTable.getColumns().size());

// Get column information
for (Column column : userTable.getColumns()) {
    System.out.println(StrUtil.format("Column: {} ({})", 
        column.getName(), column.getTypeName()));
}

// Check if table exists
boolean tableExists = MetaUtil.getTableNames(db.getDataSource()).contains("users");

// Get all table names
List<String> tableNames = MetaUtil.getTableNames(db.getDataSource());
```

### Dynamic SQL Building

```java
import cn.hutool.db.sql.SqlBuilder;
import cn.hutool.db.sql.Condition;

// Build dynamic WHERE clause
List<Condition> conditions = new ArrayList<>();

String nameFilter = "John";
if (StrUtil.isNotBlank(nameFilter)) {
    conditions.add(new Condition("name", "LIKE", "%" + nameFilter + "%"));
}

Integer minAge = 25;
if (minAge != null) {
    conditions.add(new Condition("age", ">=", minAge));
}

String status = "active";
if (StrUtil.isNotBlank(status)) {
    conditions.add(new Condition("status", "=", status));
}

// Build SQL
SqlBuilder sqlBuilder = new SqlBuilder();
String sql = sqlBuilder.select("*")
    .from("users")
    .where(conditions)
    .orderBy("created_at DESC")
    .limit(10)
    .build();

// Execute dynamic query
List<Entity> results = db.query(sql, sqlBuilder.getParams().toArray());
```

This comprehensive documentation covers all major modules of Hutool with practical examples and best practices. Each module provides powerful utilities that can significantly simplify Java development tasks.