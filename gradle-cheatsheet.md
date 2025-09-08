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

### Maven Publishing
```groovy
apply plugin: 'maven-publish'

publishing {
    publications {
        maven(MavenPublication) {
            from components.java
            
            pom {
                name = 'My Library'
                description = 'A concise description of my library'
                url = 'http://www.example.com/library'
                
                licenses {
                    license {
                        name = 'The Apache License, Version 2.0'
                        url = 'http://www.apache.org/licenses/LICENSE-2.0.txt'
                    }
                }
                
                developers {
                    developer {
                        id = 'johndoe'
                        name = 'John Doe'
                        email = 'john.doe@example.com'
                    }
                }
                
                scm {
                    connection = 'scm:git:git://example.com/my-library.git'
                    developerConnection = 'scm:git:ssh://example.com/my-library.git'
                    url = 'http://example.com/my-library'
                }
            }
        }
    }
    
    repositories {
        maven {
            name = "GitHubPackages"
            url = uri("https://maven.pkg.github.com/username/repository")
            credentials {
                username = project.findProperty("gpr.user") ?: System.getenv("USERNAME")
                password = project.findProperty("gpr.key") ?: System.getenv("TOKEN")
            }
        }
    }
}
```

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

## Git Integration & Version Control

### Git-Aware Build Information
```groovy
// Automatically include Git info in your build (like stamping dishes with chef info)
def getGitHash = { ->
    def stdout = new ByteArrayOutputStream()
    exec {
        commandLine 'git', 'rev-parse', '--short', 'HEAD'
        standardOutput = stdout
    }
    return stdout.toString().trim()
}

def getGitBranch = { ->
    def stdout = new ByteArrayOutputStream()
    exec {
        commandLine 'git', 'rev-parse', '--abbrev-ref', 'HEAD'
        standardOutput = stdout
    }
    return stdout.toString().trim()
}

// Use in JAR manifest
jar {
    manifest {
        attributes(
            'Implementation-Version': version,
            'Git-Commit': getGitHash(),
            'Git-Branch': getGitBranch(),
            'Build-Date': new Date().toString()
        )
    }
}
```

### Gradle + Git Workflow Commands
```bash
# Complete development cycle
git checkout -b feature/new-algorithm    # Create feature branch
./gradlew clean build test              # Ensure everything works
git add .                               # Stage changes
git commit -m "feat: implement new sorting algorithm"
./gradlew check                         # Final verification
git push origin feature/new-algorithm   # Push to remote

# Pre-commit hooks workflow
./gradlew spotlessCheck                 # Check code formatting
./gradlew test                          # Run tests before committing
git add .
git commit -m "fix: resolve edge case in data processor"

# Release workflow
git checkout main                       # Switch to main branch
git pull origin main                    # Get latest changes
./gradlew clean build                   # Clean build
./gradlew publishToMavenLocal          # Test publishing
git tag v1.2.0                         # Tag release
git push origin v1.2.0                 # Push tag
./gradlew publish                       # Publish to repository
```

### Git-Ignore for Gradle Projects
**`.gitignore`** (what not to include in version control):
```gitignore
# Gradle files (don't track build outputs)
.gradle/
build/
gradle-app.setting
!gradle-wrapper.jar

# IDE files (personal workspace preferences)
.idea/
*.iml
*.ipr
*.iws
.vscode/
.settings/
.project
.classpath

# OS generated files
.DS_Store
.DS_Store?
._*
Thumbs.db

# Logs and temporary files
*.log
*.tmp
*.temp

# Local configuration (secrets and personal settings)
local.properties
gradle.properties.local

# Test outputs
/out/
/target/
```

### Version Management with Git Tags
```groovy
// Automatically set version based on Git tags
def getVersionFromGit = { ->
    try {
        def stdout = new ByteArrayOutputStream()
        exec {
            commandLine 'git', 'describe', '--tags', '--always'
            standardOutput = stdout
            errorOutput = new ByteArrayOutputStream() // Suppress errors
        }
        def version = stdout.toString().trim()
        return version.startsWith('v') ? version.substring(1) : version
    } catch (Exception e) {
        return '0.0.0-SNAPSHOT'
    }
}

version = getVersionFromGit()
```

### Useful Git Commands for Gradle Projects
```bash
# Check what files Gradle is ignoring
git status --ignored

# See what changed since last release
git log v1.1.0..HEAD --oneline

# Stash changes before switching branches (save work temporarily)
git stash                               # Save current work
./gradlew clean                         # Clean build artifacts  
git checkout main                       # Switch branches
git stash pop                           # Restore saved work

# Compare branches (useful for code reviews)
git diff main..feature/new-feature      # See what's different
./gradlew test                          # Test the differences

# Revert problematic commits
git revert <commit-hash>                # Safe way to undo changes
./gradlew clean build test              # Verify everything still works

# Clean up merged branches
git branch --merged                     # See what's been merged
git branch -d feature/completed-feature # Delete merged branches
./gradlew clean                         # Clean old build artifacts
```

### Git Hooks for Gradle Projects
**`.git/hooks/pre-commit`** (run checks before each commit):
```bash
#!/bin/sh
# Pre-commit hook to run Gradle checks

echo "Running pre-commit checks..."

# Run code formatting check
./gradlew spotlessCheck
if [ $? -ne 0 ]; then
    echo "Code formatting check failed. Run './gradlew spotlessApply' to fix."
    exit 1
fi

# Run tests
./gradlew test
if [ $? -ne 0 ]; then
    echo "Tests failed. Please fix before committing."
    exit 1
fi

echo "All checks passed!"
```

**`.git/hooks/pre-push`** (run before pushing to remote):
```bash
#!/bin/sh
# Pre-push hook to run full build

echo "Running pre-push validation..."

# Full clean build
./gradlew clean build
if [ $? -ne 0 ]; then
    echo "Build failed. Cannot push."
    exit 1
fi

echo "Build successful. Proceeding with push."
```

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
