# Hutool Complete Documentation Index

This is the comprehensive documentation for Hutool (糊涂工具), a powerful Java utility library that simplifies common programming tasks.

## 📚 Documentation Structure

The documentation is organized into several comprehensive files:

### 1. [API_DOCUMENTATION.md](./API_DOCUMENTATION.md)
**Main Overview and Getting Started Guide**
- Project overview and introduction
- Installation instructions (Maven, Gradle)
- Quick start examples
- Module overview with key classes
- Core module documentation (StrUtil, ArrayUtil, DateUtil, FileUtil)
- HTTP module basics
- JSON processing fundamentals
- Crypto module essentials
- Database operations overview
- Best practices and troubleshooting

### 2. [API_REFERENCE.md](./API_REFERENCE.md)
**Detailed API Reference**
- Complete method signatures and parameters
- Detailed examples for each method
- Core utilities deep dive:
  - StrUtil - String manipulation methods
  - ArrayUtil - Array operations
  - DateUtil - Date and time utilities
  - FileUtil - File I/O operations
  - CollUtil - Collection utilities
- Return types and exception handling
- Parameter validation and edge cases

### 3. [USAGE_GUIDE.md](./USAGE_GUIDE.md)
**Practical Usage Patterns**
- Real-world examples and use cases
- Common programming scenarios
- Advanced patterns and techniques
- Integration examples (Spring Boot, Servlets)
- Performance optimization tips
- Configuration management
- Error handling strategies
- Testing utilities and patterns

### 4. [MODULE_DOCUMENTATION.md](./MODULE_DOCUMENTATION.md)
**Detailed Module Documentation**
- HTTP Module (`hutool-http`)
  - HttpUtil static methods
  - HttpRequest fluent API
  - HttpResponse handling
  - File upload/download
  - Cookie management
  - SSL/TLS configuration
- JSON Module (`hutool-json`)
  - JSONUtil operations
  - JSONObject manipulation
  - JSONArray handling
  - Object serialization/deserialization
  - Custom configurations
- Crypto Module (`hutool-crypto`)
  - Hash algorithms (MD5, SHA, HMAC)
  - Symmetric encryption (AES, DES, 3DES)
  - Asymmetric encryption (RSA)
  - Digital signatures
  - Key management
- Database Module (`hutool-db`)
  - Database configuration
  - CRUD operations
  - Transaction management
  - Pagination
  - Entity mapping
  - Custom SQL execution

### 5. [ADDITIONAL_MODULES.md](./ADDITIONAL_MODULES.md)
**Specialized Modules**
- Cache Module (`hutool-cache`)
  - LRU, FIFO, Timed caches
  - Multi-level caching
  - Custom cache implementations
- Cron Module (`hutool-cron`)
  - Job scheduling
  - Cron expressions
  - Task management
- POI Module (`hutool-poi`)
  - Excel reading/writing
  - Dynamic report generation
  - Multi-sheet operations
- Captcha Module (`hutool-captcha`)
  - Various CAPTCHA types
  - Web integration
  - Custom implementations
- Extra Module (`hutool-extra`)
  - Template engines
  - Email utilities
  - QR code generation

---

## 🚀 Quick Navigation

### For Beginners
Start with [API_DOCUMENTATION.md](./API_DOCUMENTATION.md) for:
- Installation instructions
- First examples
- Basic concepts
- Module overview

### For Developers
Use [USAGE_GUIDE.md](./USAGE_GUIDE.md) for:
- Practical examples
- Integration patterns
- Best practices
- Performance tips

### For Reference
Check [API_REFERENCE.md](./API_REFERENCE.md) for:
- Complete method signatures
- Parameter details
- Return types
- Exception handling

### For Specific Modules
See [MODULE_DOCUMENTATION.md](./MODULE_DOCUMENTATION.md) and [ADDITIONAL_MODULES.md](./ADDITIONAL_MODULES.md) for:
- Detailed module features
- Advanced configurations
- Specialized use cases

---

## 📖 Key Features Covered

### Core Utilities
- **String Processing**: Validation, formatting, manipulation, similarity
- **Array Operations**: Creation, manipulation, searching, conversion
- **Date/Time**: Parsing, formatting, arithmetic, comparison
- **File I/O**: Reading, writing, directory operations, metadata
- **Collections**: Filtering, mapping, joining, splitting

### Network & Web
- **HTTP Client**: GET/POST requests, file operations, SSL support
- **JSON Processing**: Parsing, generation, object mapping
- **Web Integration**: Servlet filters, Spring Boot integration

### Security & Crypto
- **Hashing**: MD5, SHA family, HMAC, BCrypt
- **Encryption**: AES, DES, RSA, digital signatures
- **Key Management**: Generation, storage, derivation

### Data & Persistence
- **Database ORM**: CRUD operations, transactions, pagination
- **Excel Processing**: Reading, writing, formatting, reports
- **Caching**: LRU, FIFO, timed, multi-level caches

### Specialized Features
- **Job Scheduling**: Cron expressions, task management
- **CAPTCHA**: Various types, web integration
- **Template Engines**: Velocity, FreeMarker integration
- **Email**: SMTP, HTML, attachments
- **QR Codes**: Generation, decoding, customization

---

## 🔧 Installation

### Maven
```xml
<dependency>
    <groupId>cn.hutool</groupId>
    <artifactId>hutool-all</artifactId>
    <version>4.5.2</version>
</dependency>
```

### Gradle
```groovy
implementation 'cn.hutool:hutool-all:4.5.2'
```

### Individual Modules
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

---

## 💡 Quick Examples

### String Operations
```java
import cn.hutool.core.util.StrUtil;

// Validation
boolean isBlank = StrUtil.isBlank("   "); // true
boolean hasContent = StrUtil.isNotBlank("hello"); // true

// Formatting
String formatted = StrUtil.format("Hello {}, welcome to {}", "John", "Hutool");

// Case conversion
String camelCase = StrUtil.toCamelCase("user_name"); // "userName"
```

### HTTP Requests
```java
import cn.hutool.http.HttpUtil;
import cn.hutool.http.HttpRequest;

// Simple GET
String response = HttpUtil.get("https://api.example.com/data");

// Advanced POST
String result = HttpRequest.post("https://api.example.com/users")
    .header("Content-Type", "application/json")
    .body("{\"name\":\"John\"}")
    .execute()
    .body();
```

### JSON Processing
```java
import cn.hutool.json.JSONUtil;
import cn.hutool.json.JSONObject;

// Parse JSON
JSONObject user = JSONUtil.parseObj("{\"name\":\"John\",\"age\":30}");
String name = user.getStr("name");

// Generate JSON
User userObj = new User("John", 30);
String json = JSONUtil.toJsonStr(userObj);
```

### Database Operations
```java
import cn.hutool.db.Db;
import cn.hutool.db.Entity;

// Query
List<Entity> users = db.find("users", "status = ?", "active");

// Insert
Entity newUser = Entity.create("users")
    .set("name", "John")
    .set("email", "john@example.com");
Long id = db.insert(newUser);
```

---

## 🌟 Why Choose Hutool?

### Comprehensive
- **50+ utility classes** covering common programming tasks
- **Consistent API design** across all modules
- **Well-documented** with extensive examples

### Reliable
- **Mature library** with active development
- **Extensive unit tests** ensuring reliability
- **Production-ready** used by thousands of projects

### Easy to Use
- **Intuitive method names** and parameters
- **Fluent APIs** for complex operations
- **Minimal dependencies** and lightweight

### Flexible
- **Modular design** - use only what you need
- **Configurable** behavior for different scenarios
- **Extensible** with custom implementations

---

## 📞 Getting Help

- **Official Documentation**: [https://www.hutool.cn/docs/](https://www.hutool.cn/docs/)
- **API Reference**: [https://apidoc.gitee.com/loolly/hutool/](https://apidoc.gitee.com/loolly/hutool/)
- **GitHub Issues**: [https://github.com/looly/hutool/issues](https://github.com/looly/hutool/issues)
- **Gitee Issues**: [https://gitee.com/loolly/hutool/issues](https://gitee.com/loolly/hutool/issues)

---

## 🤝 Contributing

Contributions are welcome! Please read the contribution guidelines in the project repository and submit pull requests to the `v4-dev` branch.

### Guidelines
1. **Complete documentation** for new methods
2. **Follow existing code style** (Eclipse default formatting)
3. **No third-party dependencies** in core modules
4. **Comprehensive unit tests** for new functionality

---

## 📄 License

Hutool is licensed under the Apache License 2.0. See the LICENSE file for details.

---

*This documentation provides comprehensive coverage of all Hutool features with practical examples and best practices. For the most up-to-date information, please refer to the official documentation.*