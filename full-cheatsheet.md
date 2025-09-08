# Gradle Cheatsheet

## What is Gradle?
Think of Gradle as a **master chef** for your code kitchen. It takes your raw ingredients (source code, dependencies, resources) and follows recipes (build scripts) to create finished dishes (compiled applications, JARs, etc.). Just like a chef coordinates multiple cooking processes, Gradle orchestrates compilation, testing, packaging, and deployment.

## Core Concepts

### Projects & Tasks
- **Project**: Like a restaurant - it's the entire establishment with all its operations
- **Task**: Like individual cooking steps - chopping, sautéing, plating
- **Build Script**: The recipe book (`build.gradle` or `build.gradle.kts`)

### Gradle Wrapper
Think of it as a **universal remote** - ensures everyone uses the same Gradle version regardless of what's installed locally.

```bash
# Use wrapper (recommended)
./gradlew build      # Unix/Mac
gradlew.bat build    # Windows

# Direct gradle (if installed)
gradle build
```

## Essential Commands

### Basic Operations
```bash
# Build the project (like cooking the full meal)
./gradlew build

# Clean build artifacts (clear the kitchen)
./gradlew clean

# Compile only (prep ingredients)
./gradlew compileJava

# Run tests (quality control)
./gradlew test

# Create executable JAR (package for delivery)
./gradlew jar

# Run application (serve the meal)
./gradlew run
```

### Information & Debugging
```bash
# List all available tasks (see the menu)
./gradlew tasks

# Show project structure (kitchen layout)
./gradlew projects

# Dependency tree (ingredient supply chain)
./gradlew dependencies

# Build with detailed output (verbose cooking commentary)
./gradlew build --info

# Debug build issues
./gradlew build --debug --stacktrace
```

## Build Script Basics (`build.gradle`)

### Minimal Java Project
```groovy
// Like declaring what type of restaurant you're running
plugins {
    id 'java'
    id 'application'
}

// Where to find ingredients (dependencies)
repositories {
    mavenCentral()
}

// Your ingredient list
dependencies {
    implementation 'org.apache.commons:commons-lang3:3.12.0'
    testImplementation 'junit:junit:4.13.2'
}

// Main dish specification
application {
    mainClass = 'com.example.Main'
}
```

### Kotlin DSL Version (`build.gradle.kts`)
```kotlin
plugins {
    java
    application
}

repositories {
    mavenCentral()
}

dependencies {
    implementation("org.apache.commons:commons-lang3:3.12.0")
    testImplementation("junit:junit:4.13.2")
}

application {
    mainClass.set("com.example.Main")
}
```

## Dependency Management

### Dependency Configurations
Think of these as different **supply contracts** with vendors:

```groovy
dependencies {
    // Runtime ingredient (needed for cooking and serving)
    implementation 'org.springframework:spring-core:5.3.21'
    
    // Cooking tool only (needed for prep, not serving)
    compileOnly 'org.projectlombok:lombok:1.18.24'
    
    // Serving requirement (runtime only)
    runtimeOnly 'mysql:mysql-connector-java:8.0.29'
    
    // Quality testing supplies
    testImplementation 'org.junit.jupiter:junit-jupiter:5.8.2'
    testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
    
    // Development tools (like having a dishwasher in the kitchen)
    developmentOnly 'org.springframework.boot:spring-boot-devtools'
}
```

### Version Management
```groovy
// Version catalog (like a price list from suppliers)
ext {
    springVersion = '5.3.21'
    junitVersion = '5.8.2'
}

dependencies {
    implementation "org.springframework:spring-core:$springVersion"
    testImplementation "org.junit.jupiter:junit-jupiter:$junitVersion"
}
```

## Task Management

### Custom Tasks
```groovy
// Creating your own cooking procedure
task customClean(type: Delete) {
    description = 'Custom cleanup procedure'
    delete 'build/custom'
}

// Task that depends on others (like needing chopped onions before sautéing)
task packageApp(dependsOn: ['build', 'customClean']) {
    doLast {
        println 'Packaging application...'
        // Custom packaging logic
    }
}
```

### Task Execution Control
```bash
# Run specific task
./gradlew customClean

# Run multiple tasks in sequence
./gradlew clean build test

# Skip tests (skip quality control - use carefully!)
./gradlew build -x test

# Continue on failure (keep cooking even if one dish burns)
./gradlew build --continue
```

## Multi-Project Builds

Think of this as a **restaurant chain** - multiple locations (subprojects) with shared resources:

### Root `settings.gradle`
```groovy
rootProject.name = 'restaurant-chain'
include 'common', 'web-app', 'mobile-app'
```

### Root `build.gradle`
```groovy
// Chain-wide policies
allprojects {
    repositories {
        mavenCentral()
    }
}

// Policies for all locations except headquarters
subprojects {
    apply plugin: 'java'
    
    dependencies {
        testImplementation 'junit:junit:4.13.2'
    }
}
```

### Subproject Dependencies
```groovy
// In web-app/build.gradle
dependencies {
    implementation project(':common')  // Use shared recipes
    implementation 'org.springframework.boot:spring-boot-starter-web'
}
```

## Advanced Features

### Build Types & Flavors
```groovy
// Like having different menu options (debug/release)
android {
    buildTypes {
        debug {
            debuggable true
            // Extra logging ingredients
        }
        release {
            minifyEnabled true
            // Optimized for production serving
        }
    }
}
```

### Custom Source Sets
```groovy
// Different ingredient storage areas
sourceSets {
    integration {
        java.srcDir 'src/integration/java'
        resources.srcDir 'src/integration/resources'
    }
}
```

### Gradle Properties

**`gradle.properties`** (like restaurant settings):
```properties
# JVM settings (kitchen equipment specs)
org.gradle.jvmargs=-Xmx2g -XX:MaxMetaspaceSize=512m

# Build optimization (parallel cooking)
org.gradle.parallel=true
org.gradle.caching=true

# Custom properties
version=1.2.3
springVersion=5.3.21
```

## Performance Optimization

### Build Cache
```bash
# Enable build cache (like having prep work done ahead)
./gradlew build --build-cache
```

### Parallel Execution
```properties
# In gradle.properties
org.gradle.parallel=true
org.gradle.workers.max=4
```

### Daemon Management
```bash
# Start daemon (keep the kitchen staff ready)
./gradlew --daemon

# Stop daemon (send staff home)
./gradlew --stop

# Check daemon status
./gradlew --status
```

## Testing

### Test Execution
```bash
# Run all tests
./gradlew test

# Run specific test class
./gradlew test --tests com.example.MyTest

# Run tests matching pattern
./gradlew test --tests "*Integration*"

# Generate test report
./gradlew test jacocoTestReport
```

### Test Configuration
```groovy
test {
    useJUnitPlatform()  // Use modern testing framework
    
    // Test execution settings (kitchen testing protocols)
    testLogging {
        events "passed", "skipped", "failed"
        exceptionFormat "full"
    }
    
    // Parallel test execution
    maxParallelForks = Runtime.runtime.availableProcessors() / 2
}
```

## Publishing & Distribution

### JAR Configuration
```groovy
jar {
    archiveBaseName = 'my-app'
    archiveVersion = '1.0.0'
    
    // Fat JAR (complete meal kit)
    from {
        configurations.runtimeClasspath.collect { it.isDirectory() ? it : zipTree(it) }
    }
}
```

### Application Plugin
```groovy
application {
    mainClass = 'com.example.Main'
    applicationDefaultJvmArgs = ['-Xmx1g']
}

// Creates distribution packages
./gradlew distZip  // ZIP package
./gradlew distTar  // TAR package
```

## Troubleshooting

### Common Issues & Solutions
```bash
# Clear Gradle cache (reset kitchen)
rm -rf ~/.gradle/caches/

# Force refresh dependencies (get fresh ingredients)
./gradlew build --refresh-dependencies

# Verbose output for debugging
./gradlew build --info --stacktrace

# Check for dependency conflicts
./gradlew dependencyInsight --dependency spring-core
```

### Build Debugging
```groovy
// Add to build.gradle for debugging
gradle.buildFinished { buildResult ->
    println "Build finished: ${buildResult.failure ? 'FAILED' : 'SUCCESS'}"
}
```

## Quick Reference

### Most Used Commands
```bash
./gradlew clean build    # Full clean build
./gradlew test          # Run tests
./gradlew tasks         # List available tasks
./gradlew dependencies  # Show dependency tree
./gradlew build -x test # Build without tests
./gradlew run          # Run application
```

### Key Files
- `build.gradle` / `build.gradle.kts` - Main build script (recipe book)
- `settings.gradle` - Project structure (restaurant layout)
- `gradle.properties` - Configuration (kitchen settings)
- `gradlew` / `gradlew.bat` - Gradle wrapper (universal remote)

### Best Practices
1. **Always use the wrapper** (`./gradlew`) for consistency
2. **Enable build cache** for faster builds
3. **Use specific dependency versions** to avoid surprises
4. **Keep build scripts clean** and well-commented
5. **Use multi-project structure** for complex applications
6. **Run tests regularly** - don't skip quality control!

---

*Remember: Gradle is like having a master chef who never gets tired, never forgets a step, and can coordinate dozens of cooking processes simultaneously. Once you set up the recipes (build scripts), it handles all the complex coordination for you!*
