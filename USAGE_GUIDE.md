# Hutool Usage Guide

This comprehensive guide provides practical examples and best practices for using Hutool in real-world Java applications.

## Table of Contents

- [Getting Started](#getting-started)
- [Common Use Cases](#common-use-cases)
- [Advanced Patterns](#advanced-patterns)
- [Integration Examples](#integration-examples)
- [Performance Tips](#performance-tips)
- [Best Practices](#best-practices)

---

# Getting Started

## Installation

### Maven
Add the following dependency to your `pom.xml`:

```xml
<dependency>
    <groupId>cn.hutool</groupId>
    <artifactId>hutool-all</artifactId>
    <version>4.5.2</version>
</dependency>
```

### Gradle
Add to your `build.gradle`:

```groovy
implementation 'cn.hutool:hutool-all:4.5.2'
```

### Individual Modules
You can also include specific modules instead of the full library:

```xml
<!-- Core utilities only -->
<dependency>
    <groupId>cn.hutool</groupId>
    <artifactId>hutool-core</artifactId>
    <version>4.5.2</version>
</dependency>

<!-- HTTP client only -->
<dependency>
    <groupId>cn.hutool</groupId>
    <artifactId>hutool-http</artifactId>
    <version>4.5.2</version>
</dependency>
```

## First Example

Here's a simple example demonstrating common Hutool operations:

```java
import cn.hutool.core.util.StrUtil;
import cn.hutool.core.date.DateUtil;
import cn.hutool.http.HttpUtil;
import cn.hutool.json.JSONUtil;

public class HutoolExample {
    public static void main(String[] args) {
        // String operations
        String formatted = StrUtil.format("Hello {}, today is {}", 
            "World", DateUtil.formatDate(DateUtil.date()));
        System.out.println(formatted);
        
        // HTTP request
        String response = HttpUtil.get("https://api.github.com/users/octocat");
        
        // JSON parsing
        if (StrUtil.isNotBlank(response)) {
            Object user = JSONUtil.parseObj(response);
            System.out.println("User data: " + user);
        }
    }
}
```

---

# Common Use Cases

## 1. String Processing

### Data Validation and Cleaning
```java
public class DataValidator {
    
    public boolean validateUserInput(String name, String email, String phone) {
        // Check for blank values
        if (StrUtil.hasBlank(name, email, phone)) {
            return false;
        }
        
        // Validate email format (basic check)
        if (!StrUtil.contains(email, "@") || !StrUtil.contains(email, ".")) {
            return false;
        }
        
        // Validate phone (digits only)
        String cleanPhone = StrUtil.cleanBlank(phone);
        if (!StrUtil.isNumeric(cleanPhone) || cleanPhone.length() < 10) {
            return false;
        }
        
        return true;
    }
    
    public String cleanAndFormatName(String rawName) {
        if (StrUtil.isBlank(rawName)) {
            return "";
        }
        
        // Trim and convert to proper case
        String cleaned = StrUtil.trim(rawName);
        return StrUtil.upperFirst(StrUtil.lowerFirst(cleaned));
    }
    
    public String generateSlug(String title) {
        return StrUtil.toUnderlineCase(title.toLowerCase())
                     .replaceAll("[^a-z0-9_]", "")
                     .replaceAll("_{2,}", "_");
    }
}
```

### Template Processing
```java
public class TemplateProcessor {
    
    public String processEmailTemplate(String template, Map<String, Object> variables) {
        String result = template;
        
        for (Map.Entry<String, Object> entry : variables.entrySet()) {
            String placeholder = "{" + entry.getKey() + "}";
            String value = String.valueOf(entry.getValue());
            result = StrUtil.replace(result, placeholder, value);
        }
        
        return result;
    }
    
    public String generateReport(List<Map<String, Object>> data) {
        StringBuilder report = new StringBuilder();
        report.append("# Data Report\n\n");
        
        for (int i = 0; i < data.size(); i++) {
            Map<String, Object> row = data.get(i);
            report.append(StrUtil.format("## Record {}\n", i + 1));
            
            for (Map.Entry<String, Object> entry : row.entrySet()) {
                report.append(StrUtil.format("- {}: {}\n", 
                    entry.getKey(), entry.getValue()));
            }
            report.append("\n");
        }
        
        return report.toString();
    }
}
```

## 2. File Operations

### Configuration File Management
```java
public class ConfigManager {
    private final File configFile;
    
    public ConfigManager(String configPath) {
        this.configFile = new File(configPath);
        initializeConfig();
    }
    
    private void initializeConfig() {
        if (!configFile.exists()) {
            // Create default configuration
            String defaultConfig = StrUtil.format(
                "# Application Configuration\n" +
                "app.name={}\n" +
                "app.version=1.0.0\n" +
                "app.debug=false\n" +
                "database.url=jdbc:h2:mem:testdb\n" +
                "database.username=sa\n" +
                "database.password=\n",
                "MyApplication"
            );
            
            FileUtil.writeUtf8String(defaultConfig, configFile);
        }
    }
    
    public Properties loadConfig() {
        String content = FileUtil.readUtf8String(configFile);
        Properties props = new Properties();
        
        List<String> lines = StrUtil.splitTrim(content, '\n');
        for (String line : lines) {
            if (StrUtil.isNotBlank(line) && !line.startsWith("#")) {
                String[] parts = StrUtil.splitToArray(line, "=", 2);
                if (parts.length == 2) {
                    props.setProperty(StrUtil.trim(parts[0]), StrUtil.trim(parts[1]));
                }
            }
        }
        
        return props;
    }
    
    public void saveConfig(Properties props) {
        StringBuilder content = new StringBuilder();
        content.append("# Application Configuration\n");
        content.append("# Generated on ").append(DateUtil.formatDateTime(DateUtil.date())).append("\n\n");
        
        for (String key : props.stringPropertyNames()) {
            content.append(key).append("=").append(props.getProperty(key)).append("\n");
        }
        
        FileUtil.writeUtf8String(content.toString(), configFile);
    }
}
```

### File Processing Pipeline
```java
public class FileProcessor {
    
    public void processDirectory(String inputDir, String outputDir, String fileExtension) {
        File input = new File(inputDir);
        File output = new File(outputDir);
        
        FileUtil.mkdir(output);
        
        // Find all files with specified extension
        List<File> files = FileUtil.loopFiles(input, file -> 
            file.getName().endsWith("." + fileExtension));
        
        System.out.println(StrUtil.format("Found {} files to process", files.size()));
        
        for (File file : files) {
            try {
                processFile(file, new File(output, file.getName()));
            } catch (Exception e) {
                System.err.println(StrUtil.format("Error processing {}: {}", 
                    file.getName(), e.getMessage()));
            }
        }
    }
    
    private void processFile(File input, File output) {
        String content = FileUtil.readUtf8String(input);
        
        // Process content (example: convert to uppercase)
        String processed = content.toUpperCase();
        
        // Add processing metadata
        String header = StrUtil.format(
            "# Processed on {}\n# Original file: {}\n# Size: {}\n\n",
            DateUtil.formatDateTime(DateUtil.date()),
            input.getName(),
            FileUtil.readableFileSize(input.length())
        );
        
        FileUtil.writeUtf8String(header + processed, output);
    }
    
    public Map<String, Object> analyzeDirectory(String dirPath) {
        File dir = new File(dirPath);
        List<File> files = FileUtil.loopFiles(dir);
        
        Map<String, Object> stats = new HashMap<>();
        Map<String, Integer> extensionCounts = new HashMap<>();
        long totalSize = 0;
        
        for (File file : files) {
            String ext = FileUtil.getSuffix(file);
            extensionCounts.merge(ext, 1, Integer::sum);
            totalSize += file.length();
        }
        
        stats.put("totalFiles", files.size());
        stats.put("totalSize", FileUtil.readableFileSize(totalSize));
        stats.put("extensionBreakdown", extensionCounts);
        stats.put("averageSize", FileUtil.readableFileSize(totalSize / Math.max(files.size(), 1)));
        
        return stats;
    }
}
```

## 3. HTTP Client Usage

### REST API Client
```java
public class ApiClient {
    private final String baseUrl;
    private final String apiKey;
    
    public ApiClient(String baseUrl, String apiKey) {
        this.baseUrl = baseUrl;
        this.apiKey = apiKey;
    }
    
    public JSONObject getUser(String userId) {
        String url = baseUrl + "/users/" + userId;
        
        try {
            String response = HttpRequest.get(url)
                .header("Authorization", "Bearer " + apiKey)
                .header("Accept", "application/json")
                .timeout(5000)
                .execute()
                .body();
            
            return JSONUtil.parseObj(response);
        } catch (Exception e) {
            throw new RuntimeException("Failed to fetch user: " + userId, e);
        }
    }
    
    public JSONObject createUser(String name, String email) {
        String url = baseUrl + "/users";
        
        JSONObject payload = JSONUtil.createObj()
            .put("name", name)
            .put("email", email)
            .put("created_at", DateUtil.formatDateTime(DateUtil.date()));
        
        try {
            String response = HttpRequest.post(url)
                .header("Authorization", "Bearer " + apiKey)
                .header("Content-Type", "application/json")
                .body(payload.toString())
                .timeout(10000)
                .execute()
                .body();
            
            return JSONUtil.parseObj(response);
        } catch (Exception e) {
            throw new RuntimeException("Failed to create user", e);
        }
    }
    
    public boolean uploadFile(String endpoint, File file, Map<String, String> metadata) {
        String url = baseUrl + endpoint;
        
        try {
            Map<String, Object> params = new HashMap<>(metadata);
            params.put("file", file);
            
            HttpResponse response = HttpRequest.post(url)
                .header("Authorization", "Bearer " + apiKey)
                .form(params)
                .timeout(30000)
                .execute();
            
            return response.isOk();
        } catch (Exception e) {
            System.err.println("Upload failed: " + e.getMessage());
            return false;
        }
    }
}
```

### Web Scraper
```java
public class WebScraper {
    
    public List<String> extractLinks(String url, String linkPattern) {
        try {
            String html = HttpRequest.get(url)
                .header("User-Agent", "Mozilla/5.0 (compatible; WebScraper/1.0)")
                .execute()
                .body();
            
            List<String> links = new ArrayList<>();
            
            // Simple regex-based link extraction
            Pattern pattern = Pattern.compile(linkPattern);
            Matcher matcher = pattern.matcher(html);
            
            while (matcher.find()) {
                String link = matcher.group(1);
                if (StrUtil.isNotBlank(link)) {
                    links.add(link);
                }
            }
            
            return links;
        } catch (Exception e) {
            System.err.println("Failed to scrape " + url + ": " + e.getMessage());
            return Collections.emptyList();
        }
    }
    
    public void downloadImages(List<String> imageUrls, String outputDir) {
        File dir = new File(outputDir);
        FileUtil.mkdir(dir);
        
        for (int i = 0; i < imageUrls.size(); i++) {
            String imageUrl = imageUrls.get(i);
            String fileName = StrUtil.format("image_{}.{}", 
                i + 1, FileUtil.getSuffix(imageUrl));
            
            try {
                HttpUtil.downloadFile(imageUrl, new File(dir, fileName));
                System.out.println("Downloaded: " + fileName);
            } catch (Exception e) {
                System.err.println("Failed to download " + imageUrl + ": " + e.getMessage());
            }
        }
    }
}
```

## 4. JSON Processing

### Data Transformation
```java
public class DataTransformer {
    
    public JSONObject transformUserData(JSONObject rawUser) {
        JSONObject transformed = JSONUtil.createObj();
        
        // Basic field mapping
        transformed.put("id", rawUser.getStr("user_id"));
        transformed.put("fullName", rawUser.getStr("first_name") + " " + rawUser.getStr("last_name"));
        transformed.put("email", rawUser.getStr("email_address"));
        
        // Date transformation
        String birthDateStr = rawUser.getStr("birth_date");
        if (StrUtil.isNotBlank(birthDateStr)) {
            DateTime birthDate = DateUtil.parse(birthDateStr);
            transformed.put("birthDate", DateUtil.formatDate(birthDate));
            transformed.put("age", DateUtil.ageOfNow(birthDate));
        }
        
        // Nested object handling
        JSONObject address = rawUser.getJSONObject("address");
        if (address != null) {
            String fullAddress = StrUtil.format("{}, {}, {} {}",
                address.getStr("street", ""),
                address.getStr("city", ""),
                address.getStr("state", ""),
                address.getStr("zip", "")
            );
            transformed.put("fullAddress", fullAddress);
        }
        
        // Array processing
        JSONArray hobbies = rawUser.getJSONArray("interests");
        if (hobbies != null) {
            List<String> hobbyList = hobbies.toList(String.class);
            transformed.put("hobbies", StrUtil.join(hobbyList, ", "));
        }
        
        return transformed;
    }
    
    public List<Map<String, Object>> jsonArrayToMapList(JSONArray jsonArray) {
        List<Map<String, Object>> result = new ArrayList<>();
        
        for (int i = 0; i < jsonArray.size(); i++) {
            JSONObject obj = jsonArray.getJSONObject(i);
            if (obj != null) {
                result.add(obj.toMap());
            }
        }
        
        return result;
    }
    
    public JSONObject aggregateData(JSONArray records, String groupBy) {
        JSONObject aggregated = JSONUtil.createObj();
        Map<String, List<JSONObject>> groups = new HashMap<>();
        
        // Group records
        for (int i = 0; i < records.size(); i++) {
            JSONObject record = records.getJSONObject(i);
            String groupKey = record.getStr(groupBy, "unknown");
            
            groups.computeIfAbsent(groupKey, k -> new ArrayList<>()).add(record);
        }
        
        // Calculate aggregations
        for (Map.Entry<String, List<JSONObject>> entry : groups.entrySet()) {
            JSONObject groupStats = JSONUtil.createObj();
            List<JSONObject> groupRecords = entry.getValue();
            
            groupStats.put("count", groupRecords.size());
            
            // Calculate numeric aggregations if applicable
            OptionalDouble avgValue = groupRecords.stream()
                .mapToDouble(obj -> obj.getDouble("value", 0.0))
                .average();
            
            if (avgValue.isPresent()) {
                groupStats.put("averageValue", Math.round(avgValue.getAsDouble() * 100.0) / 100.0);
            }
            
            aggregated.put(entry.getKey(), groupStats);
        }
        
        return aggregated;
    }
}
```

## 5. Database Operations

### Repository Pattern Implementation
```java
public class UserRepository {
    private final Db db;
    
    public UserRepository(Db database) {
        this.db = database;
    }
    
    public Optional<User> findById(Long id) {
        try {
            Entity userEntity = db.findOne("users", "id = ?", id);
            return userEntity != null ? Optional.of(entityToUser(userEntity)) : Optional.empty();
        } catch (Exception e) {
            System.err.println("Error finding user by ID: " + e.getMessage());
            return Optional.empty();
        }
    }
    
    public List<User> findActiveUsers(int limit) {
        List<Entity> entities = db.find("users", 
            "status = ? ORDER BY created_at DESC LIMIT ?", "active", limit);
        
        return entities.stream()
                .map(this::entityToUser)
                .collect(Collectors.toList());
    }
    
    public User save(User user) {
        Entity entity = userToEntity(user);
        
        if (user.getId() == null) {
            // Insert new user
            Long id = db.insert(entity);
            user.setId(id);
        } else {
            // Update existing user
            entity.set("updated_at", DateUtil.date());
            db.update(entity, "id = ?", user.getId());
        }
        
        return user;
    }
    
    public boolean deleteById(Long id) {
        try {
            int deleted = db.del("users", "id = ?", id);
            return deleted > 0;
        } catch (Exception e) {
            System.err.println("Error deleting user: " + e.getMessage());
            return false;
        }
    }
    
    public PageResult<User> findPaginated(int page, int size, String search) {
        Page pageObj = new Page(page, size);
        
        String whereClause = "";
        Object[] params = {};
        
        if (StrUtil.isNotBlank(search)) {
            whereClause = "name LIKE ? OR email LIKE ?";
            String searchPattern = "%" + search + "%";
            params = new Object[]{searchPattern, searchPattern};
        }
        
        PageResult<Entity> entityPage = StrUtil.isBlank(whereClause) 
            ? db.page("users", pageObj)
            : db.page("users", pageObj, whereClause, params);
        
        List<User> users = entityPage.getList().stream()
                .map(this::entityToUser)
                .collect(Collectors.toList());
        
        return new PageResult<>(users, entityPage.getTotal(), entityPage.getTotalPage());
    }
    
    private User entityToUser(Entity entity) {
        User user = new User();
        user.setId(entity.getLong("id"));
        user.setName(entity.getStr("name"));
        user.setEmail(entity.getStr("email"));
        user.setStatus(entity.getStr("status"));
        user.setCreatedAt(entity.getDate("created_at"));
        user.setUpdatedAt(entity.getDate("updated_at"));
        return user;
    }
    
    private Entity userToEntity(User user) {
        Entity entity = Entity.create("users");
        if (user.getId() != null) {
            entity.set("id", user.getId());
        }
        entity.set("name", user.getName());
        entity.set("email", user.getEmail());
        entity.set("status", user.getStatus());
        entity.set("created_at", user.getCreatedAt() != null ? user.getCreatedAt() : DateUtil.date());
        return entity;
    }
}
```

### Batch Processing
```java
public class BatchProcessor {
    private final Db db;
    private final int batchSize;
    
    public BatchProcessor(Db database, int batchSize) {
        this.db = database;
        this.batchSize = batchSize;
    }
    
    public void processCsvFile(String csvFilePath, String tableName) {
        List<String> lines = FileUtil.readUtf8Lines(new File(csvFilePath));
        if (lines.isEmpty()) {
            return;
        }
        
        // Parse header
        String[] headers = StrUtil.splitToArray(lines.get(0), ",");
        
        List<Entity> batch = new ArrayList<>();
        int processedCount = 0;
        
        for (int i = 1; i < lines.size(); i++) {
            String line = lines.get(i);
            String[] values = StrUtil.splitToArray(line, ",");
            
            Entity entity = Entity.create(tableName);
            for (int j = 0; j < Math.min(headers.length, values.length); j++) {
                entity.set(StrUtil.trim(headers[j]), StrUtil.trim(values[j]));
            }
            
            batch.add(entity);
            
            // Process batch when it reaches the specified size
            if (batch.size() >= batchSize) {
                processBatch(batch);
                processedCount += batch.size();
                batch.clear();
                
                System.out.println(StrUtil.format("Processed {} records", processedCount));
            }
        }
        
        // Process remaining records
        if (!batch.isEmpty()) {
            processBatch(batch);
            processedCount += batch.size();
        }
        
        System.out.println(StrUtil.format("Total processed: {} records", processedCount));
    }
    
    private void processBatch(List<Entity> batch) {
        db.tx(() -> {
            for (Entity entity : batch) {
                db.insert(entity);
            }
        });
    }
    
    public void exportToJson(String tableName, String outputFile) {
        List<Entity> allRecords = db.findAll(tableName);
        
        JSONArray jsonArray = JSONUtil.createArray();
        for (Entity entity : allRecords) {
            jsonArray.put(entity.toMap());
        }
        
        String jsonString = JSONUtil.toJsonPrettyStr(jsonArray);
        FileUtil.writeUtf8String(jsonString, new File(outputFile));
        
        System.out.println(StrUtil.format("Exported {} records to {}", 
            allRecords.size(), outputFile));
    }
}
```

---

# Advanced Patterns

## 1. Builder Pattern with Hutool

```java
public class ReportBuilder {
    private final StringBuilder content;
    private final Map<String, Object> metadata;
    
    public ReportBuilder() {
        this.content = new StringBuilder();
        this.metadata = new HashMap<>();
    }
    
    public ReportBuilder title(String title) {
        content.append("# ").append(title).append("\n\n");
        metadata.put("title", title);
        return this;
    }
    
    public ReportBuilder section(String sectionTitle) {
        content.append("## ").append(sectionTitle).append("\n\n");
        return this;
    }
    
    public ReportBuilder paragraph(String text) {
        content.append(text).append("\n\n");
        return this;
    }
    
    public ReportBuilder table(List<Map<String, Object>> data) {
        if (data.isEmpty()) {
            return this;
        }
        
        // Get headers from first row
        Set<String> headers = data.get(0).keySet();
        
        // Create table header
        content.append("| ").append(StrUtil.join(headers, " | ")).append(" |\n");
        content.append("| ").append(StrUtil.repeat("--- | ", headers.size())).append("\n");
        
        // Add data rows
        for (Map<String, Object> row : data) {
            List<String> values = new ArrayList<>();
            for (String header : headers) {
                values.add(String.valueOf(row.getOrDefault(header, "")));
            }
            content.append("| ").append(StrUtil.join(values, " | ")).append(" |\n");
        }
        content.append("\n");
        
        return this;
    }
    
    public ReportBuilder metadata(String key, Object value) {
        metadata.put(key, value);
        return this;
    }
    
    public String build() {
        StringBuilder report = new StringBuilder();
        
        // Add metadata header
        report.append("---\n");
        for (Map.Entry<String, Object> entry : metadata.entrySet()) {
            report.append(entry.getKey()).append(": ").append(entry.getValue()).append("\n");
        }
        report.append("generated: ").append(DateUtil.formatDateTime(DateUtil.date())).append("\n");
        report.append("---\n\n");
        
        // Add content
        report.append(content);
        
        return report.toString();
    }
    
    public void saveToFile(String filePath) {
        String report = build();
        FileUtil.writeUtf8String(report, new File(filePath));
    }
}
```

## 2. Event Processing System

```java
public class EventProcessor {
    
    public interface EventHandler {
        void handle(JSONObject event);
    }
    
    private final Map<String, List<EventHandler>> handlers = new HashMap<>();
    
    public void registerHandler(String eventType, EventHandler handler) {
        handlers.computeIfAbsent(eventType, k -> new ArrayList<>()).add(handler);
    }
    
    public void processEvent(String eventJson) {
        try {
            JSONObject event = JSONUtil.parseObj(eventJson);
            String eventType = event.getStr("type");
            
            if (StrUtil.isBlank(eventType)) {
                System.err.println("Event missing type field");
                return;
            }
            
            List<EventHandler> eventHandlers = handlers.get(eventType);
            if (eventHandlers != null) {
                for (EventHandler handler : eventHandlers) {
                    try {
                        handler.handle(event);
                    } catch (Exception e) {
                        System.err.println(StrUtil.format("Handler error for event {}: {}", 
                            eventType, e.getMessage()));
                    }
                }
            }
        } catch (Exception e) {
            System.err.println("Failed to process event: " + e.getMessage());
        }
    }
    
    public void processEventsFromFile(String filePath) {
        List<String> lines = FileUtil.readUtf8Lines(new File(filePath));
        
        for (String line : lines) {
            if (StrUtil.isNotBlank(line) && !line.startsWith("#")) {
                processEvent(line);
            }
        }
    }
    
    // Example usage
    public static void main(String[] args) {
        EventProcessor processor = new EventProcessor();
        
        // Register handlers
        processor.registerHandler("user.created", event -> {
            String userId = event.getStr("userId");
            String email = event.getStr("email");
            System.out.println(StrUtil.format("New user created: {} ({})", userId, email));
        });
        
        processor.registerHandler("order.placed", event -> {
            String orderId = event.getStr("orderId");
            double amount = event.getDouble("amount");
            System.out.println(StrUtil.format("Order placed: {} - ${}", orderId, amount));
        });
        
        // Process events
        processor.processEvent("{\"type\":\"user.created\",\"userId\":\"123\",\"email\":\"user@example.com\"}");
        processor.processEvent("{\"type\":\"order.placed\",\"orderId\":\"ORD001\",\"amount\":99.99}");
    }
}
```

## 3. Configuration Management System

```java
public class ConfigurationManager {
    private final Map<String, Object> config = new HashMap<>();
    private final List<String> configFiles = new ArrayList<>();
    
    public ConfigurationManager loadFromFile(String filePath) {
        configFiles.add(filePath);
        
        File file = new File(filePath);
        if (!file.exists()) {
            System.err.println("Configuration file not found: " + filePath);
            return this;
        }
        
        String content = FileUtil.readUtf8String(file);
        String extension = FileUtil.getSuffix(file);
        
        switch (extension.toLowerCase()) {
            case "json":
                loadJsonConfig(content);
                break;
            case "properties":
                loadPropertiesConfig(content);
                break;
            default:
                System.err.println("Unsupported config format: " + extension);
        }
        
        return this;
    }
    
    private void loadJsonConfig(String jsonContent) {
        try {
            JSONObject json = JSONUtil.parseObj(jsonContent);
            flattenJson("", json, config);
        } catch (Exception e) {
            System.err.println("Failed to parse JSON config: " + e.getMessage());
        }
    }
    
    private void loadPropertiesConfig(String content) {
        List<String> lines = StrUtil.splitTrim(content, '\n');
        
        for (String line : lines) {
            if (StrUtil.isNotBlank(line) && !line.startsWith("#")) {
                String[] parts = StrUtil.splitToArray(line, "=", 2);
                if (parts.length == 2) {
                    config.put(StrUtil.trim(parts[0]), StrUtil.trim(parts[1]));
                }
            }
        }
    }
    
    private void flattenJson(String prefix, JSONObject json, Map<String, Object> target) {
        for (String key : json.keySet()) {
            String fullKey = StrUtil.isBlank(prefix) ? key : prefix + "." + key;
            Object value = json.get(key);
            
            if (value instanceof JSONObject) {
                flattenJson(fullKey, (JSONObject) value, target);
            } else {
                target.put(fullKey, value);
            }
        }
    }
    
    public String getString(String key, String defaultValue) {
        Object value = config.get(key);
        return value != null ? String.valueOf(value) : defaultValue;
    }
    
    public int getInt(String key, int defaultValue) {
        Object value = config.get(key);
        if (value instanceof Number) {
            return ((Number) value).intValue();
        }
        try {
            return Integer.parseInt(String.valueOf(value));
        } catch (Exception e) {
            return defaultValue;
        }
    }
    
    public boolean getBoolean(String key, boolean defaultValue) {
        Object value = config.get(key);
        if (value instanceof Boolean) {
            return (Boolean) value;
        }
        return Boolean.parseBoolean(String.valueOf(value));
    }
    
    public void set(String key, Object value) {
        config.put(key, value);
    }
    
    public void saveToFile(String filePath) {
        String extension = FileUtil.getSuffix(filePath);
        
        switch (extension.toLowerCase()) {
            case "json":
                saveAsJson(filePath);
                break;
            case "properties":
                saveAsProperties(filePath);
                break;
            default:
                System.err.println("Unsupported save format: " + extension);
        }
    }
    
    private void saveAsJson(String filePath) {
        JSONObject json = JSONUtil.createObj();
        
        for (Map.Entry<String, Object> entry : config.entrySet()) {
            setNestedValue(json, entry.getKey(), entry.getValue());
        }
        
        String jsonString = JSONUtil.toJsonPrettyStr(json);
        FileUtil.writeUtf8String(jsonString, new File(filePath));
    }
    
    private void saveAsProperties(String filePath) {
        StringBuilder content = new StringBuilder();
        content.append("# Configuration saved on ")
               .append(DateUtil.formatDateTime(DateUtil.date()))
               .append("\n\n");
        
        for (Map.Entry<String, Object> entry : config.entrySet()) {
            content.append(entry.getKey())
                   .append("=")
                   .append(entry.getValue())
                   .append("\n");
        }
        
        FileUtil.writeUtf8String(content.toString(), new File(filePath));
    }
    
    private void setNestedValue(JSONObject json, String key, Object value) {
        String[] parts = StrUtil.splitToArray(key, ".");
        JSONObject current = json;
        
        for (int i = 0; i < parts.length - 1; i++) {
            String part = parts[i];
            if (!current.containsKey(part)) {
                current.put(part, JSONUtil.createObj());
            }
            current = current.getJSONObject(part);
        }
        
        current.put(parts[parts.length - 1], value);
    }
}
```

---

# Integration Examples

## Spring Boot Integration

### Configuration
```java
@Configuration
@EnableConfigurationProperties(HutoolProperties.class)
public class HutoolConfiguration {
    
    @Bean
    @ConditionalOnMissingBean
    public Db primaryDatabase(DataSource dataSource) {
        return Db.use(dataSource);
    }
    
    @Bean
    public ConfigurationManager configManager(HutoolProperties properties) {
        ConfigurationManager manager = new ConfigurationManager();
        
        if (StrUtil.isNotBlank(properties.getConfigFile())) {
            manager.loadFromFile(properties.getConfigFile());
        }
        
        return manager;
    }
}

@ConfigurationProperties(prefix = "hutool")
@Data
public class HutoolProperties {
    private String configFile;
    private Database database = new Database();
    
    @Data
    public static class Database {
        private int batchSize = 100;
        private boolean enableTransactions = true;
    }
}
```

### Service Layer
```java
@Service
public class UserService {
    
    @Autowired
    private Db db;
    
    @Autowired
    private ConfigurationManager configManager;
    
    public List<UserDto> findUsers(String search, Pageable pageable) {
        Page page = new Page(pageable.getPageNumber() + 1, pageable.getPageSize());
        
        String whereClause = "";
        Object[] params = {};
        
        if (StrUtil.isNotBlank(search)) {
            whereClause = "name LIKE ? OR email LIKE ?";
            String pattern = "%" + search + "%";
            params = new Object[]{pattern, pattern};
        }
        
        PageResult<Entity> result = StrUtil.isBlank(whereClause)
            ? db.page("users", page)
            : db.page("users", page, whereClause, params);
        
        return result.getList().stream()
                .map(this::entityToDto)
                .collect(Collectors.toList());
    }
    
    @Transactional
    public UserDto createUser(CreateUserRequest request) {
        // Validation
        if (StrUtil.hasBlank(request.getName(), request.getEmail())) {
            throw new IllegalArgumentException("Name and email are required");
        }
        
        Entity userEntity = Entity.create("users")
            .set("name", StrUtil.trim(request.getName()))
            .set("email", StrUtil.trim(request.getEmail()).toLowerCase())
            .set("status", "active")
            .set("created_at", DateUtil.date());
        
        Long userId = db.insert(userEntity);
        userEntity.set("id", userId);
        
        return entityToDto(userEntity);
    }
    
    private UserDto entityToDto(Entity entity) {
        UserDto dto = new UserDto();
        dto.setId(entity.getLong("id"));
        dto.setName(entity.getStr("name"));
        dto.setEmail(entity.getStr("email"));
        dto.setStatus(entity.getStr("status"));
        dto.setCreatedAt(entity.getDate("created_at"));
        return dto;
    }
}
```

## Servlet Integration

### Filter for Request Logging
```java
@WebFilter(urlPatterns = "/*")
public class RequestLoggingFilter implements Filter {
    
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, 
                        FilterChain chain) throws IOException, ServletException {
        
        HttpServletRequest httpRequest = (HttpServletRequest) request;
        HttpServletResponse httpResponse = (HttpServletResponse) response;
        
        String requestId = IdUtil.fastSimpleUUID();
        String startTime = DateUtil.formatDateTime(DateUtil.date());
        
        // Log request
        String requestLog = StrUtil.format("Request [{}] {} {} from {} at {}",
            requestId,
            httpRequest.getMethod(),
            httpRequest.getRequestURI(),
            httpRequest.getRemoteAddr(),
            startTime
        );
        System.out.println(requestLog);
        
        long start = System.currentTimeMillis();
        
        try {
            chain.doFilter(request, response);
        } finally {
            long duration = System.currentTimeMillis() - start;
            
            String responseLog = StrUtil.format("Response [{}] {} - {}ms",
                requestId,
                httpResponse.getStatus(),
                duration
            );
            System.out.println(responseLog);
        }
    }
}
```

---

# Performance Tips

## 1. String Operations Optimization

```java
public class PerformantStringProcessor {
    
    // Use StrBuilder for multiple concatenations
    public String buildLargeString(List<String> parts) {
        StrBuilder builder = StrUtil.strBuilder();
        
        for (String part : parts) {
            builder.append(part).append("\n");
        }
        
        return builder.toString();
    }
    
    // Reuse StringBuilder for repeated operations
    private final StrBuilder reusableBuilder = StrUtil.strBuilder();
    
    public String formatMultipleMessages(List<MessageData> messages) {
        reusableBuilder.reset(); // Clear previous content
        
        for (MessageData msg : messages) {
            reusableBuilder.append(StrUtil.format("[{}] {}: {}\n",
                DateUtil.formatTime(msg.getTimestamp()),
                msg.getSender(),
                msg.getContent()
            ));
        }
        
        return reusableBuilder.toString();
    }
}
```

## 2. File Processing Optimization

```java
public class EfficientFileProcessor {
    
    // Process large files in chunks
    public void processLargeFile(String filePath, int chunkSize) {
        try (BufferedReader reader = FileUtil.getUtf8Reader(new File(filePath))) {
            List<String> chunk = new ArrayList<>(chunkSize);
            String line;
            
            while ((line = reader.readLine()) != null) {
                chunk.add(line);
                
                if (chunk.size() >= chunkSize) {
                    processChunk(chunk);
                    chunk.clear();
                }
            }
            
            // Process remaining lines
            if (!chunk.isEmpty()) {
                processChunk(chunk);
            }
        } catch (IOException e) {
            throw new RuntimeException("Failed to process file", e);
        }
    }
    
    private void processChunk(List<String> lines) {
        // Process chunk of lines
        for (String line : lines) {
            // Process individual line
            if (StrUtil.isNotBlank(line)) {
                // Processing logic here
            }
        }
    }
    
    // Use streaming for large collections
    public void processDirectory(String dirPath) {
        FileUtil.loopFiles(new File(dirPath))
                .parallelStream()
                .filter(file -> file.getName().endsWith(".txt"))
                .forEach(this::processFile);
    }
    
    private void processFile(File file) {
        // Process individual file
        String content = FileUtil.readUtf8String(file);
        // Processing logic
    }
}
```

## 3. Database Batch Operations

```java
public class BatchDatabaseOperations {
    private final Db db;
    private final int batchSize;
    
    public BatchDatabaseOperations(Db db, int batchSize) {
        this.db = db;
        this.batchSize = batchSize;
    }
    
    public void batchInsert(String tableName, List<Map<String, Object>> records) {
        List<Entity> batch = new ArrayList<>(batchSize);
        
        for (Map<String, Object> record : records) {
            Entity entity = Entity.create(tableName);
            entity.putAll(record);
            batch.add(entity);
            
            if (batch.size() >= batchSize) {
                insertBatch(batch);
                batch.clear();
            }
        }
        
        // Insert remaining records
        if (!batch.isEmpty()) {
            insertBatch(batch);
        }
    }
    
    private void insertBatch(List<Entity> batch) {
        db.tx(() -> {
            db.insert(batch);
        });
    }
    
    // Use pagination for large result sets
    public void processAllRecords(String tableName, Consumer<Entity> processor) {
        int page = 1;
        int pageSize = 1000;
        
        while (true) {
            Page pageObj = new Page(page, pageSize);
            PageResult<Entity> result = db.page(tableName, pageObj);
            
            if (result.getList().isEmpty()) {
                break;
            }
            
            result.getList().forEach(processor);
            page++;
        }
    }
}
```

---

# Best Practices

## 1. Error Handling

```java
public class RobustService {
    
    public Optional<String> safeHttpRequest(String url) {
        try {
            String response = HttpRequest.get(url)
                .timeout(5000)
                .execute()
                .body();
            
            return StrUtil.isNotBlank(response) ? Optional.of(response) : Optional.empty();
        } catch (Exception e) {
            System.err.println(StrUtil.format("HTTP request failed for {}: {}", url, e.getMessage()));
            return Optional.empty();
        }
    }
    
    public boolean safeFileOperation(String filePath, String content) {
        try {
            File file = new File(filePath);
            
            // Ensure parent directories exist
            FileUtil.mkParentDirs(file);
            
            // Write content
            FileUtil.writeUtf8String(content, file);
            
            // Verify write was successful
            String written = FileUtil.readUtf8String(file);
            return content.equals(written);
            
        } catch (Exception e) {
            System.err.println(StrUtil.format("File operation failed for {}: {}", filePath, e.getMessage()));
            return false;
        }
    }
}
```

## 2. Resource Management

```java
public class ResourceManager {
    
    // Always close resources properly
    public String processFileWithResources(String filePath) {
        try (BufferedReader reader = FileUtil.getUtf8Reader(new File(filePath))) {
            StringBuilder result = new StringBuilder();
            String line;
            
            while ((line = reader.readLine()) != null) {
                result.append(processLine(line)).append("\n");
            }
            
            return result.toString();
        } catch (IOException e) {
            throw new RuntimeException("Failed to process file: " + filePath, e);
        }
    }
    
    private String processLine(String line) {
        // Process individual line
        return StrUtil.trim(line).toUpperCase();
    }
    
    // Use try-with-resources for database operations
    public List<Map<String, Object>> queryWithResources(String sql, Object... params) {
        try {
            return db.query(sql, params).stream()
                    .map(Entity::toMap)
                    .collect(Collectors.toList());
        } catch (Exception e) {
            System.err.println("Database query failed: " + e.getMessage());
            return Collections.emptyList();
        }
    }
}
```

## 3. Configuration and Logging

```java
public class ConfigurableService {
    private final ConfigurationManager config;
    private final Log logger = LogFactory.get(ConfigurableService.class);
    
    public ConfigurableService(ConfigurationManager config) {
        this.config = config;
    }
    
    public void performOperation(String input) {
        // Use configuration values
        int timeout = config.getInt("service.timeout", 5000);
        boolean debugMode = config.getBoolean("service.debug", false);
        
        if (debugMode) {
            logger.debug("Starting operation with input: {}", input);
        }
        
        try {
            // Perform operation with configured timeout
            String result = processWithTimeout(input, timeout);
            
            logger.info("Operation completed successfully");
            
        } catch (Exception e) {
            logger.error("Operation failed for input: {}", input, e);
            throw new RuntimeException("Service operation failed", e);
        }
    }
    
    private String processWithTimeout(String input, int timeout) {
        // Implementation with timeout
        return "processed: " + input;
    }
}
```

## 4. Testing Utilities

```java
public class TestUtils {
    
    // Create test data files
    public static File createTempFile(String content) {
        try {
            File tempFile = File.createTempFile("hutool-test", ".txt");
            tempFile.deleteOnExit();
            FileUtil.writeUtf8String(content, tempFile);
            return tempFile;
        } catch (IOException e) {
            throw new RuntimeException("Failed to create temp file", e);
        }
    }
    
    // Generate test JSON data
    public static JSONObject createTestUser(String name, String email) {
        return JSONUtil.createObj()
            .put("id", RandomUtil.randomLong(1000, 9999))
            .put("name", name)
            .put("email", email)
            .put("createdAt", DateUtil.formatDateTime(DateUtil.date()))
            .put("active", true);
    }
    
    // Mock HTTP responses
    public static String mockApiResponse(Object data) {
        JSONObject response = JSONUtil.createObj()
            .put("success", true)
            .put("timestamp", DateUtil.formatDateTime(DateUtil.date()))
            .put("data", data);
        
        return JSONUtil.toJsonStr(response);
    }
}
```

This usage guide provides comprehensive examples of how to use Hutool effectively in real-world applications. The patterns and examples shown here demonstrate best practices for common development scenarios.