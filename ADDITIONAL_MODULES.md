# Hutool Additional Modules Documentation

This document covers the remaining Hutool modules with comprehensive examples and usage patterns.

---

# Cache Module (`hutool-cache`)

The cache module provides various caching implementations for improving application performance.

## Key Classes

- `CacheUtil` - Factory methods for creating different cache types
- `LRUCache` - Least Recently Used cache
- `FIFOCache` - First In First Out cache  
- `TimedCache` - Time-based expiring cache
- `WeakCache` - Weak reference cache

## Cache Types and Usage

### LRU Cache (Least Recently Used)

```java
import cn.hutool.cache.CacheUtil;
import cn.hutool.cache.impl.LRUCache;

// Create LRU cache with maximum 100 entries
LRUCache<String, String> lruCache = CacheUtil.newLRUCache(100);

// Put values
lruCache.put("user:1", "John Doe");
lruCache.put("user:2", "Jane Smith");
lruCache.put("user:3", "Bob Johnson");

// Get values
String user1 = lruCache.get("user:1"); // "John Doe"
String user4 = lruCache.get("user:4"); // null

// Cache with custom object
LRUCache<String, User> userCache = CacheUtil.newLRUCache(50);
userCache.put("john@example.com", new User("John", "john@example.com"));

User john = userCache.get("john@example.com");

// Check cache status
int size = lruCache.size();
boolean isEmpty = lruCache.isEmpty();
boolean containsKey = lruCache.containsKey("user:1");
```

### FIFO Cache (First In First Out)

```java
import cn.hutool.cache.impl.FIFOCache;

// Create FIFO cache with maximum 50 entries
FIFOCache<String, Object> fifoCache = CacheUtil.newFIFOCache(50);

// Store different types of data
fifoCache.put("config:timeout", 30000);
fifoCache.put("config:retries", 3);
fifoCache.put("config:enabled", true);
fifoCache.put("user:session:abc123", new UserSession("user123", DateUtil.date()));

// Retrieve data
Integer timeout = (Integer) fifoCache.get("config:timeout");
UserSession session = (UserSession) fifoCache.get("user:session:abc123");

// When cache is full, oldest entries are removed first
for (int i = 0; i < 100; i++) {
    fifoCache.put("item:" + i, "value" + i);
}
// Only the last 50 items will remain
```

### Timed Cache (TTL - Time To Live)

```java
import cn.hutool.cache.impl.TimedCache;

// Create cache with 5-minute default TTL
TimedCache<String, String> timedCache = CacheUtil.newTimedCache(5 * 60 * 1000);

// Put with default TTL
timedCache.put("session:abc123", "user_data");

// Put with custom TTL (10 seconds)
timedCache.put("temp:data", "temporary", 10 * 1000);

// Put with specific expiration time
Date expirationTime = DateUtil.offsetMinute(DateUtil.date(), 15);
timedCache.put("scheduled:task", "task_data", expirationTime);

// Get data (returns null if expired)
String sessionData = timedCache.get("session:abc123");

// Check if expired
boolean isExpired = timedCache.isExpired("session:abc123");

// Manual cleanup of expired entries
timedCache.prune();

// Auto-cleanup with scheduled task
timedCache.schedulePrune(60 * 1000); // Clean every minute
```

### Weak Reference Cache

```java
import cn.hutool.cache.impl.WeakCache;

// Create weak reference cache (entries can be garbage collected)
WeakCache<String, LargeObject> weakCache = CacheUtil.newWeakCache();

// Store large objects that can be GC'd when memory is needed
LargeObject largeData = new LargeObject();
weakCache.put("large:data:1", largeData);

// Data might be null if it was garbage collected
LargeObject retrieved = weakCache.get("large:data:1");
if (retrieved != null) {
    // Use the object
    System.out.println("Data still available");
} else {
    // Object was garbage collected, reload if needed
    System.out.println("Data was garbage collected");
}
```

## Advanced Cache Usage

### Cache with Custom Loader

```java
public class DataCache {
    private final LRUCache<String, User> userCache;
    private final UserRepository userRepository;
    
    public DataCache(UserRepository repository) {
        this.userCache = CacheUtil.newLRUCache(1000);
        this.userRepository = repository;
    }
    
    public User getUser(String userId) {
        return userCache.get(userId, () -> {
            // Loader function - called when key not in cache
            return userRepository.findById(userId);
        });
    }
    
    public void invalidateUser(String userId) {
        userCache.remove(userId);
    }
    
    public void refreshUser(String userId) {
        User freshUser = userRepository.findById(userId);
        if (freshUser != null) {
            userCache.put(userId, freshUser);
        }
    }
}
```

### Multi-Level Cache

```java
public class MultiLevelCache<K, V> {
    private final LRUCache<K, V> l1Cache; // Fast, small cache
    private final TimedCache<K, V> l2Cache; // Larger, timed cache
    
    public MultiLevelCache(int l1Size, long l2TtlMs) {
        this.l1Cache = CacheUtil.newLRUCache(l1Size);
        this.l2Cache = CacheUtil.newTimedCache(l2TtlMs);
    }
    
    public V get(K key) {
        // Try L1 cache first
        V value = l1Cache.get(key);
        if (value != null) {
            return value;
        }
        
        // Try L2 cache
        value = l2Cache.get(key);
        if (value != null) {
            // Promote to L1 cache
            l1Cache.put(key, value);
            return value;
        }
        
        return null;
    }
    
    public void put(K key, V value) {
        l1Cache.put(key, value);
        l2Cache.put(key, value);
    }
    
    public void remove(K key) {
        l1Cache.remove(key);
        l2Cache.remove(key);
    }
}
```

---

# Cron Module (`hutool-cron`)

The cron module provides cron-like job scheduling functionality.

## Key Classes

- `CronUtil` - Main utility for cron operations
- `CronPattern` - Represents a cron expression
- `Scheduler` - Job scheduler
- `Task` - Represents a scheduled task

## Basic Cron Usage

### Starting and Managing Scheduler

```java
import cn.hutool.cron.CronUtil;

// Start the global scheduler
CronUtil.start();

// Schedule a simple task - runs every 5 seconds
CronUtil.schedule("*/5 * * * * *", new Runnable() {
    @Override
    public void run() {
        System.out.println("Task executed at: " + DateUtil.formatDateTime(DateUtil.date()));
    }
});

// Lambda version (Java 8+)
CronUtil.schedule("*/10 * * * * *", () -> {
    System.out.println("Lambda task executed");
});

// Stop the scheduler when done
Runtime.getRuntime().addShutdownHook(new Thread(() -> {
    CronUtil.stop();
}));
```

### Cron Expression Patterns

```java
// Common cron patterns
CronUtil.schedule("0 0 9 * * *", () -> {
    System.out.println("Daily at 9:00 AM");
});

CronUtil.schedule("0 0 9 * * MON-FRI", () -> {
    System.out.println("Weekdays at 9:00 AM");
});

CronUtil.schedule("0 0 0 1 * *", () -> {
    System.out.println("First day of every month at midnight");
});

CronUtil.schedule("0 */15 * * * *", () -> {
    System.out.println("Every 15 minutes");
});

CronUtil.schedule("0 0 12 * * SUN", () -> {
    System.out.println("Every Sunday at noon");
});
```

### Named Tasks

```java
// Schedule tasks with IDs for management
CronUtil.schedule("daily-report", "0 0 8 * * *", () -> {
    generateDailyReport();
});

CronUtil.schedule("cleanup-task", "0 0 2 * * *", () -> {
    performCleanup();
});

CronUtil.schedule("backup-task", "0 0 3 * * SUN", () -> {
    performBackup();
});

// Remove a specific task
CronUtil.remove("daily-report");

// Check if task exists
boolean exists = CronUtil.getScheduler().existTask("backup-task");
```

## Advanced Cron Features

### Custom Scheduler

```java
import cn.hutool.cron.Scheduler;
import cn.hutool.cron.task.Task;

// Create custom scheduler
Scheduler customScheduler = new Scheduler();
customScheduler.start();

// Add tasks to custom scheduler
customScheduler.schedule("custom-task", "*/30 * * * * *", new Task() {
    @Override
    public void execute() {
        System.out.println("Custom scheduler task");
    }
});

// Multiple schedulers for different purposes
Scheduler reportScheduler = new Scheduler("ReportScheduler");
Scheduler maintenanceScheduler = new Scheduler("MaintenanceScheduler");

reportScheduler.start();
maintenanceScheduler.start();
```

### Task Management

```java
public class TaskManager {
    
    public void scheduleReportTasks() {
        // Daily reports
        CronUtil.schedule("daily-sales", "0 0 9 * * *", () -> {
            generateSalesReport("daily");
        });
        
        // Weekly reports  
        CronUtil.schedule("weekly-summary", "0 0 9 * * MON", () -> {
            generateSalesReport("weekly");
        });
        
        // Monthly reports
        CronUtil.schedule("monthly-analysis", "0 0 9 1 * *", () -> {
            generateSalesReport("monthly");
        });
    }
    
    public void scheduleMaintenanceTasks() {
        // Database cleanup
        CronUtil.schedule("db-cleanup", "0 0 2 * * *", () -> {
            cleanupOldRecords();
        });
        
        // Log rotation
        CronUtil.schedule("log-rotation", "0 0 1 * * *", () -> {
            rotateLogs();
        });
        
        // Cache cleanup
        CronUtil.schedule("cache-cleanup", "0 */30 * * * *", () -> {
            cleanupExpiredCache();
        });
    }
    
    private void generateSalesReport(String type) {
        System.out.println("Generating " + type + " sales report at " + 
            DateUtil.formatDateTime(DateUtil.date()));
    }
    
    private void cleanupOldRecords() {
        System.out.println("Cleaning up old database records");
    }
    
    private void rotateLogs() {
        System.out.println("Rotating log files");
    }
    
    private void cleanupExpiredCache() {
        System.out.println("Cleaning expired cache entries");
    }
}
```

### Dynamic Task Scheduling

```java
public class DynamicTaskScheduler {
    
    public void scheduleUserTask(String userId, String cronExpression, Runnable task) {
        String taskId = "user-task-" + userId;
        
        // Remove existing task if any
        if (CronUtil.getScheduler().existTask(taskId)) {
            CronUtil.remove(taskId);
        }
        
        // Schedule new task
        CronUtil.schedule(taskId, cronExpression, task);
    }
    
    public void scheduleNotification(String userId, Date notificationTime, String message) {
        // Convert Date to cron expression for one-time execution
        String cronExpression = DateUtil.format(notificationTime, "s m H d M ? yyyy");
        String taskId = "notification-" + userId + "-" + System.currentTimeMillis();
        
        CronUtil.schedule(taskId, cronExpression, () -> {
            sendNotification(userId, message);
            // Remove task after execution since it's one-time
            CronUtil.remove(taskId);
        });
    }
    
    private void sendNotification(String userId, String message) {
        System.out.println(StrUtil.format("Notification to {}: {}", userId, message));
    }
}
```

---

# POI Module (`hutool-poi`)

The POI module provides utilities for working with Microsoft Office documents, particularly Excel files.

## Key Classes

- `ExcelUtil` - Main utility for Excel operations
- `ExcelReader` - Read Excel files
- `ExcelWriter` - Write Excel files
- `ExcelBase` - Base class for Excel operations

## Reading Excel Files

### Basic Excel Reading

```java
import cn.hutool.poi.excel.ExcelUtil;
import cn.hutool.poi.excel.ExcelReader;

// Read Excel file
ExcelReader reader = ExcelUtil.getReader(new File("data.xlsx"));

// Read all data as List<Map>
List<Map<String, Object>> rows = reader.readAll();
for (Map<String, Object> row : rows) {
    System.out.println("Row data: " + row);
}

// Read with header row
reader.setHeaderAlias("Name", "name");
reader.setHeaderAlias("Age", "age");
reader.setHeaderAlias("Email", "email");

List<Map<String, Object>> dataWithHeaders = reader.readAll();

// Read to Java objects
List<User> users = reader.readAll(User.class);

// Close reader
reader.close();
```

### Advanced Excel Reading

```java
// Read specific sheet
ExcelReader sheetReader = ExcelUtil.getReader(new File("workbook.xlsx"), "Sheet2");

// Read specific range
List<Map<String, Object>> rangeData = reader.read(1, 10); // Rows 1-10

// Read with custom start row (skip headers)
reader.setHeaderAlias(0, "id");
reader.setHeaderAlias(1, "name");
reader.setHeaderAlias(2, "email");
List<Map<String, Object>> dataFromRow2 = reader.read(1); // Start from row 1 (0-indexed)

// Read specific columns
List<Object> firstColumn = reader.readColumn(0);
List<Object> nameColumn = reader.readColumn("Name");

// Read cell by cell
Object cellValue = reader.readCellValue(2, 3); // Row 2, Column 3
String stringValue = reader.readAsText(2, 3);
```

### Reading Different File Formats

```java
// Read XLS (older format)
ExcelReader xlsReader = ExcelUtil.getReader(new File("data.xls"));

// Read XLSX (newer format)  
ExcelReader xlsxReader = ExcelUtil.getReader(new File("data.xlsx"));

// Read from InputStream
try (FileInputStream fis = new FileInputStream("data.xlsx")) {
    ExcelReader streamReader = ExcelUtil.getReader(fis, true); // true for XLSX
    List<Map<String, Object>> data = streamReader.readAll();
}

// Read CSV as Excel
ExcelReader csvReader = ExcelUtil.getReader(new File("data.csv"));
```

## Writing Excel Files

### Basic Excel Writing

```java
import cn.hutool.poi.excel.ExcelWriter;

// Create Excel writer
ExcelWriter writer = ExcelUtil.getWriter(new File("output.xlsx"));

// Write headers
writer.addHeaderAlias("name", "Full Name");
writer.addHeaderAlias("age", "Age");
writer.addHeaderAlias("email", "Email Address");
writer.addHeaderAlias("salary", "Annual Salary");

// Write data from objects
List<User> users = getUserData();
writer.write(users);

// Write single row
Map<String, Object> rowData = new HashMap<>();
rowData.put("name", "John Doe");
rowData.put("age", 30);
rowData.put("email", "john@example.com");
rowData.put("salary", 75000);
writer.writeRow(rowData);

// Close and save
writer.close();
```

### Advanced Excel Writing

```java
// Write to specific sheet
ExcelWriter sheetWriter = ExcelUtil.getWriter(new File("workbook.xlsx"), "Data");

// Write with custom formatting
ExcelWriter formattedWriter = ExcelUtil.getWriter(new File("formatted.xlsx"));

// Set column widths
formattedWriter.setColumnWidth(0, 20); // Column A width
formattedWriter.setColumnWidth(1, 15); // Column B width

// Write headers with styling
formattedWriter.writeHeadRow(Arrays.asList("Name", "Age", "Email", "Department"));

// Write data rows
List<List<Object>> rows = Arrays.asList(
    Arrays.asList("John Doe", 30, "john@example.com", "Engineering"),
    Arrays.asList("Jane Smith", 25, "jane@example.com", "Marketing"),
    Arrays.asList("Bob Johnson", 35, "bob@example.com", "Sales")
);

for (List<Object> row : rows) {
    formattedWriter.writeRow(row);
}

// Write to specific cell
formattedWriter.writeCellValue(5, 0, "Total Employees:");
formattedWriter.writeCellValue(5, 1, rows.size());

formattedWriter.close();
```

### Dynamic Excel Generation

```java
public class ExcelReportGenerator {
    
    public void generateSalesReport(List<SalesRecord> sales, String outputPath) {
        ExcelWriter writer = ExcelUtil.getWriter(new File(outputPath));
        
        // Set up headers
        writer.addHeaderAlias("date", "Sale Date");
        writer.addHeaderAlias("product", "Product Name");
        writer.addHeaderAlias("quantity", "Quantity");
        writer.addHeaderAlias("unitPrice", "Unit Price");
        writer.addHeaderAlias("total", "Total Amount");
        writer.addHeaderAlias("customer", "Customer");
        
        // Write data
        writer.write(sales);
        
        // Add summary row
        int lastRow = sales.size() + 1;
        writer.writeCellValue(lastRow, 0, "TOTAL:");
        
        double totalAmount = sales.stream()
            .mapToDouble(SalesRecord::getTotal)
            .sum();
        writer.writeCellValue(lastRow, 4, totalAmount);
        
        // Format currency columns
        writer.setColumnWidth(3, 15); // Unit Price
        writer.setColumnWidth(4, 15); // Total Amount
        
        writer.close();
    }
    
    public void generateMultiSheetReport(Map<String, List<Object>> sheetData, String outputPath) {
        ExcelWriter writer = ExcelUtil.getWriter(new File(outputPath));
        
        for (Map.Entry<String, List<Object>> entry : sheetData.entrySet()) {
            String sheetName = entry.getKey();
            List<Object> data = entry.getValue();
            
            // Switch to sheet (creates if doesn't exist)
            writer.setSheet(sheetName);
            writer.write(data);
        }
        
        writer.close();
    }
}
```

---

# Captcha Module (`hutool-captcha`)

The captcha module provides various CAPTCHA generation utilities for security purposes.

## Key Classes

- `CaptchaUtil` - Factory methods for creating CAPTCHAs
- `LineCaptcha` - Line-based interference CAPTCHA
- `CircleCaptcha` - Circle-based interference CAPTCHA
- `ShearCaptcha` - Shear distortion CAPTCHA
- `GifCaptcha` - Animated GIF CAPTCHA

## Basic CAPTCHA Usage

### Line Captcha

```java
import cn.hutool.captcha.CaptchaUtil;
import cn.hutool.captcha.LineCaptcha;

// Create line captcha (width, height, code length, line count)
LineCaptcha lineCaptcha = CaptchaUtil.createLineCaptcha(200, 100, 4, 20);

// Get the verification code
String code = lineCaptcha.getCode();
System.out.println("Verification code: " + code);

// Save to file
lineCaptcha.write(new File("captcha.png"));

// Write to OutputStream
try (FileOutputStream fos = new FileOutputStream("captcha2.png")) {
    lineCaptcha.write(fos);
}

// Get as Base64 string for web use
String base64Image = lineCaptcha.getImageBase64();
// Use in HTML: <img src="data:image/png;base64,{base64Image}" />
```

### Circle Captcha

```java
import cn.hutool.captcha.CircleCaptcha;

// Create circle captcha (width, height, code length, circle count)
CircleCaptcha circleCaptcha = CaptchaUtil.createCircleCaptcha(200, 100, 4, 10);

String code = circleCaptcha.getCode();
circleCaptcha.write(new File("circle_captcha.png"));

// Verify user input
boolean isValid = circleCaptcha.verify("user_input_code");
```

### Shear Captcha

```java
import cn.hutool.captcha.ShearCaptcha;

// Create shear captcha with distortion
ShearCaptcha shearCaptcha = CaptchaUtil.createShearCaptcha(200, 100, 5, 4);

String code = shearCaptcha.getCode();
shearCaptcha.write(new File("shear_captcha.png"));
```

### GIF Captcha (Animated)

```java
import cn.hutool.captcha.GifCaptcha;

// Create animated GIF captcha
GifCaptcha gifCaptcha = CaptchaUtil.createGifCaptcha(200, 100, 4);

String code = gifCaptcha.getCode();
gifCaptcha.write(new File("animated_captcha.gif"));
```

## Web Integration Examples

### Servlet Integration

```java
@WebServlet("/captcha")
public class CaptchaServlet extends HttpServlet {
    
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        // Create captcha
        LineCaptcha captcha = CaptchaUtil.createLineCaptcha(150, 60, 4, 15);
        
        // Store code in session for verification
        request.getSession().setAttribute("captcha_code", captcha.getCode());
        
        // Set response headers
        response.setContentType("image/png");
        response.setHeader("Cache-Control", "no-cache, no-store, must-revalidate");
        response.setHeader("Pragma", "no-cache");
        response.setDateHeader("Expires", 0);
        
        // Write captcha image to response
        captcha.write(response.getOutputStream());
    }
}

@WebServlet("/verify")
public class VerifyServlet extends HttpServlet {
    
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        String userInput = request.getParameter("captcha");
        String sessionCode = (String) request.getSession().getAttribute("captcha_code");
        
        boolean isValid = userInput != null && userInput.equalsIgnoreCase(sessionCode);
        
        response.setContentType("application/json");
        response.getWriter().write("{\"valid\":" + isValid + "}");
        
        // Clear captcha from session
        request.getSession().removeAttribute("captcha_code");
    }
}
```

### Spring Boot Integration

```java
@RestController
@RequestMapping("/api/captcha")
public class CaptchaController {
    
    @GetMapping("/generate")
    public ResponseEntity<String> generateCaptcha(HttpServletRequest request) {
        LineCaptcha captcha = CaptchaUtil.createLineCaptcha(200, 80, 4, 20);
        
        // Store in session
        request.getSession().setAttribute("captcha_code", captcha.getCode());
        
        // Return base64 image
        String base64 = captcha.getImageBase64Data();
        
        Map<String, String> response = new HashMap<>();
        response.put("image", base64);
        response.put("timestamp", String.valueOf(System.currentTimeMillis()));
        
        return ResponseEntity.ok(JSONUtil.toJsonStr(response));
    }
    
    @PostMapping("/verify")
    public ResponseEntity<Map<String, Object>> verifyCaptcha(
            @RequestParam String code, 
            HttpServletRequest request) {
        
        String sessionCode = (String) request.getSession().getAttribute("captcha_code");
        boolean isValid = code != null && code.equalsIgnoreCase(sessionCode);
        
        Map<String, Object> response = new HashMap<>();
        response.put("valid", isValid);
        response.put("message", isValid ? "Verification successful" : "Invalid captcha");
        
        // Clear session
        request.getSession().removeAttribute("captcha_code");
        
        return ResponseEntity.ok(response);
    }
}
```

## Custom CAPTCHA Implementation

```java
public class CustomCaptchaService {
    private final Map<String, CaptchaInfo> captchaStore = new ConcurrentHashMap<>();
    
    public static class CaptchaInfo {
        private final String code;
        private final long timestamp;
        
        public CaptchaInfo(String code) {
            this.code = code;
            this.timestamp = System.currentTimeMillis();
        }
        
        public boolean isExpired(long ttlMs) {
            return System.currentTimeMillis() - timestamp > ttlMs;
        }
        
        // getters...
    }
    
    public String generateCaptcha(String sessionId) {
        // Create captcha with custom settings
        LineCaptcha captcha = CaptchaUtil.createLineCaptcha(180, 60, 5, 25);
        
        // Store with session ID
        captchaStore.put(sessionId, new CaptchaInfo(captcha.getCode()));
        
        // Return base64 image
        return captcha.getImageBase64Data();
    }
    
    public boolean verifyCaptcha(String sessionId, String userInput) {
        CaptchaInfo info = captchaStore.get(sessionId);
        
        if (info == null) {
            return false;
        }
        
        // Check expiration (5 minutes)
        if (info.isExpired(5 * 60 * 1000)) {
            captchaStore.remove(sessionId);
            return false;
        }
        
        // Verify code (case insensitive)
        boolean isValid = userInput != null && 
                          userInput.equalsIgnoreCase(info.getCode());
        
        // Remove after verification attempt
        captchaStore.remove(sessionId);
        
        return isValid;
    }
    
    // Cleanup expired captchas periodically
    @Scheduled(fixedRate = 60000) // Every minute
    public void cleanupExpired() {
        long now = System.currentTimeMillis();
        captchaStore.entrySet().removeIf(entry -> 
            entry.getValue().isExpired(5 * 60 * 1000));
    }
}
```

---

# Extra Module (`hutool-extra`)

The extra module provides integrations with third-party libraries and additional utilities.

## Key Features

- Template engine integration (Velocity, FreeMarker, Thymeleaf)
- Email utilities
- QR code generation
- Servlet utilities
- SSH/SFTP support
- Compression utilities

## Template Engine Integration

### Velocity Templates

```java
import cn.hutool.extra.template.Template;
import cn.hutool.extra.template.TemplateConfig;
import cn.hutool.extra.template.TemplateEngine;
import cn.hutool.extra.template.TemplateUtil;

// Create Velocity template engine
TemplateEngine velocityEngine = TemplateUtil.createEngine(new TemplateConfig());

// Template content
String templateContent = "Hello $name, welcome to $application!";
Template template = velocityEngine.getTemplate(templateContent);

// Render with data
Map<String, Object> data = new HashMap<>();
data.put("name", "John");
data.put("application", "Hutool Demo");

String result = template.render(data);
// Result: "Hello John, welcome to Hutool Demo!"
```

### Email Integration

```java
import cn.hutool.extra.mail.MailUtil;
import cn.hutool.extra.mail.MailAccount;

// Configure mail account
MailAccount account = new MailAccount();
account.setHost("smtp.gmail.com");
account.setPort(587);
account.setAuth(true);
account.setFrom("sender@example.com");
account.setUser("sender@example.com");
account.setPass("password");
account.setStarttlsEnable(true);

// Send simple email
MailUtil.send(account, "recipient@example.com", "Subject", "Email content", false);

// Send HTML email
String htmlContent = "<h1>Welcome!</h1><p>This is an <b>HTML</b> email.</p>";
MailUtil.send(account, "recipient@example.com", "HTML Email", htmlContent, true);

// Send email with attachment
MailUtil.send(account, 
    Arrays.asList("recipient1@example.com", "recipient2@example.com"),
    "Subject with Attachment",
    "Please find attached file.",
    false,
    new File("document.pdf"));
```

### QR Code Generation

```java
import cn.hutool.extra.qrcode.QrCodeUtil;
import cn.hutool.extra.qrcode.QrConfig;

// Generate simple QR code
QrCodeUtil.generate("https://hutool.cn", 300, 300, new File("qrcode.png"));

// Generate with custom configuration
QrConfig config = new QrConfig(300, 300);
config.setMargin(2);
config.setForeColor(Color.BLACK);
config.setBackColor(Color.WHITE);

QrCodeUtil.generate("Custom QR Code Content", config, new File("custom_qr.png"));

// Generate QR code with logo
BufferedImage qrWithLogo = QrCodeUtil.generate(
    "https://example.com", 
    QrConfig.create().setImg(new File("logo.png")));

// Decode QR code
String decoded = QrCodeUtil.decode(new File("qrcode.png"));
System.out.println("Decoded content: " + decoded);
```

This comprehensive documentation covers all major Hutool modules with practical examples and usage patterns. The library provides a consistent and intuitive API across all modules, making it easy to integrate into any Java application.