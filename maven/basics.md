---

# 📦 Maven Basics

Maven is a powerful project management and build automation tool used primarily for Java projects. It simplifies the process of building, packaging, and managing dependencies for your Java applications.

## 🛠️ What is Maven?

Maven, developed by the Apache Software Foundation, provides a consistent way to manage a project's build, reporting, and documentation from a central piece of information known as the Project Object Model (POM).

### 📘 Key Concepts

1. **POM (Project Object Model):**
   - The `pom.xml` file is the core of a Maven project. It contains information about the project and configuration details used by Maven to build the project.
   - The POM file manages dependencies, plugins, build profiles, and other project settings.

2. **Dependencies:**
   - Maven makes it easy to manage dependencies. Dependencies are external libraries or frameworks that your project needs to function.
   - Maven automatically downloads these dependencies from a central repository and stores them in your local repository.

   **Example:**
   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-web</artifactId>
       <version>2.7.0</version>
   </dependency>
   ```

3. **Plugins:**
   - Plugins are used to perform tasks such as compiling code, running tests, and packaging your application. Maven has a wide range of plugins available for different tasks.
   - Common plugins include the Compiler Plugin, Surefire Plugin (for testing), and the Shade Plugin (for creating executable JARs).

4. **Repositories:**
   - Maven uses repositories to store and retrieve dependencies. The most common repository is Maven Central, but you can also define custom repositories.
   - Repositories can be local, central, or remote.

   **Example of a repository configuration:**
   ```xml
   <repositories>
       <repository>
           <id>my-repo</id>
           <url>http://myrepo.com/maven2</url>
       </repository>
   </repositories>
   ```

5. **Build Lifecycle:**
   - Maven follows a specific lifecycle to build and deploy projects. The main phases are:
     - **validate:** Validates the project is correct and all necessary information is available.
     - **compile:** Compiles the source code of the project.
     - **test:** Tests the compiled source code using a suitable unit testing framework.
     - **package:** Packages the compiled code into a distributable format, such as a JAR or WAR file.
     - **install:** Installs the package into the local repository, for use as a dependency in other projects.
     - **deploy:** Copies the final package to a remote repository for sharing with other developers and projects.

## 🔧 Setting Up Maven

### 1. **Installing Maven:**

   - Download Maven from the official website and follow the installation instructions.
   - Set the `MAVEN_HOME` environment variable to point to your Maven installation directory.
   - Add Maven’s `bin` directory to your `PATH`.

### 2. **Creating a New Maven Project:**

   - Use the following command to create a new Maven project:
     ```bash
     mvn archetype:generate -DgroupId=com.example -DartifactId=my-app -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
     ```

### 3. **Running Maven Commands:**

   - **Compile:**
     ```bash
     mvn compile
     ```
   - **Test:**
     ```bash
     mvn test
     ```
   - **Package:**
     ```bash
     mvn package
     ```

## 📜 Summary

Maven is an essential tool for managing Java projects, providing a standardized way to handle dependencies, builds, and project lifecycles. By mastering Maven basics, you can streamline your development process and focus more on writing code rather than managing builds manually.

---
