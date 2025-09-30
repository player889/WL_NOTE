To generate a controller for CRUD operations in Spring Boot using a customized annotation, you can follow these steps:

1. Define the Custom Annotation: Create a custom annotation that will be used to mark the entities or classes for which you want to generate the controller. For example, let's call it `@CrudController`.

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface CrudController {
}
```

2. Create a Custom Annotation Processor: Implement a custom annotation processor that scans the classpath for classes marked with the `@CrudController` annotation and generates the corresponding controller classes.

```java
import org.springframework.beans.factory.config.BeanDefinition;
import org.springframework.context.annotation.ClassPathScanningCandidateComponentProvider;
import org.springframework.core.type.filter.AnnotationTypeFilter;
import org.springframework.stereotype.Component;

@Component
public class CrudControllerAnnotationProcessor {

    public void process() {
        ClassPathScanningCandidateComponentProvider scanner = new ClassPathScanningCandidateComponentProvider(false);
        scanner.addIncludeFilter(new AnnotationTypeFilter(CrudController.class));

        for (BeanDefinition beanDefinition : scanner.findCandidateComponents("com.example")) {
            String className = beanDefinition.getBeanClassName();
            // Generate controller class for className
            // Implement your logic here to generate the CRUD controller using className
        }
    }
}
```

3. Enable Annotation Processing: Configure Spring Boot to enable annotation processing by adding `@EnableAspectJAutoProxy` and `@ComponentScan` annotations to your main application class.

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.EnableAspectJAutoProxy;

@SpringBootApplication
@EnableAspectJAutoProxy
@ComponentScan(basePackages = "com.example")
public class YourApplication {

    public static void main(String[] args) {
        SpringApplication.run(YourApplication.class, args);
    }
}
```

4. Generate the Controller: Implement the logic inside the annotation processor to generate the CRUD controller class based on the annotated classes. You can use code generation libraries like Apache Velocity or Thymeleaf to generate the controller code dynamically.

```java
// Example code generation using Velocity template engine
import org.apache.velocity.Template;
import org.apache.velocity.VelocityContext;
import org.apache.velocity.app.VelocityEngine;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import java.io.StringWriter;
import java.util.HashMap;
import java.util.Map;

@Component
public class CrudControllerGenerator {

    @Autowired
    private VelocityEngine velocityEngine;

    public String generateController(String className) {
        Template template = velocityEngine.getTemplate("templates/controller.vm");

        VelocityContext context = new VelocityContext();
        context.put("className", className);

        StringWriter writer = new StringWriter();
        template.merge(context, writer);

        return writer.toString();
    }
}
```

5. Configure Template Engine: Configure the Velocity template engine to load the template file for generating the controller code. Place the template file `controller.vm` in the `src/main/resources/templates` directory.

6. Invoke the Annotation Processor: In your Spring Boot application, invoke the annotation processor to generate the controllers during the application startup. You can do this by autowiring the `CrudControllerAnnotationProcessor` and calling its `process()` method.

```java
import org.springframework.boot.ApplicationArguments;
import org.springframework.boot.ApplicationRunner;
import org.springframework.stereotype.Component;

@Component
public class AnnotationProcessorRunner implements ApplicationRunner {

    private final CrudControllerAnnotationProcessor annotationProcessor;

    public AnnotationProcessorRunner(CrudControllerAnnotationProcessor annotationProcessor) {
        this.annotationProcessor = annotationProcessor;
    }

    @Override
    public void run(ApplicationArguments args) {
        annotationProcessor.process();
    }
}
```

That's it! Now, when you annotate your classes with `@CrudController`, the annotation processor will scan the classpath, find the annotated classes, and generate the corresponding controllers using the defined template engine.

---

To create a file during the `mvn clean install` process, you can use the Maven Assembly Plugin. This plugin allows you to create an archive that includes files from your project or additional resources. Here's a step-by-step guide on how to achieve this:

Step 1: Add the Assembly Plugin to your `pom.xml`
In your project's `pom.xml`, add the Maven Assembly Plugin configuration inside the `<build><plugins>` section:

```xml
<project>
    <!-- ... other project configurations ... -->
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-assembly-plugin</artifactId>
                <version>3.3.0</version>
                <configuration>
                    <descriptorRefs>
                        <descriptorRef>jar-with-dependencies</descriptorRef>
                    </descriptorRefs>
                    <archive>
                        <manifest>
                            <mainClass><!-- Specify your main class here, if applicable --></mainClass>
                        </manifest>
                    </archive>
                    <!-- Add your file configurations here -->
                    <files>
                        <file>
                            <source><!-- Path to the source file you want to include --></source>
                            <outputDirectory><!-- Destination directory inside the assembled archive --></outputDirectory>
                            <destName><!-- Desired file name --></destName>
                        </file>
                    </files>
                </configuration>
                <executions>
                    <execution>
                        <id>make-assembly</id>
                        <phase>package</phase>
                        <goals>
                            <goal>single</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```

Step 2: Specify the File to Include
Inside the `<configuration>` section of the Maven Assembly Plugin, add the `<files>` section and specify the file you want to include. Replace the placeholders with the appropriate values:

- `<source>`: The path to the file you want to include, relative to your project's root directory.
- `<outputDirectory>`: The destination directory inside the assembled archive where the file should be placed.
- `<destName>`: The desired file name for the included file.

Step 3: Build with `mvn clean install`
Once you have added the Maven Assembly Plugin configuration to your `pom.xml`, you can run the `mvn clean install` command, and the plugin will create an archive with the specified file included.

Please make sure to adapt the configuration according to your project structure and file paths.

---

# Maven and Volecity

To combine Maven, Velocity, and Java to generate Java classes during the `mvn clean install` process, you can follow these steps:

Step 1: Set up the Velocity Template
First, create a Velocity template (e.g., `MyClassTemplate.vm`) that represents the Java class you want to generate. For example:

```java
package com.example.generated;

public class $className {
    // Class content goes here
}
```

In the template, `$className` is a placeholder that will be replaced with the actual class name when generating the Java class.

Step 2: Configure the Maven Resources Plugin
Next, you need to configure the Maven Resources Plugin to copy the Velocity template to the appropriate location during the build process. Add the following configuration to your `pom.xml` within the `<build><plugins>` section:

```xml
<project>
    <!-- ... other project configurations ... -->
    <build>
        <plugins>
            <!-- ... other plugins ... -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
                <version>3.2.0</version>
                <executions>
                    <execution>
                        <id>copy-velocity-template</id>
                        <phase>generate-sources</phase>
                        <goals>
                            <goal>copy-resources</goal>
                        </goals>
                        <configuration>
                            <outputDirectory>${project.build.directory}/generated-sources/velocity</outputDirectory>
                            <resources>
                                <resource>
                                    <directory>src/main/velocity</directory> <!-- Path to the directory containing the Velocity template -->
                                </resource>
                            </resources>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
            <!-- ... other plugins ... -->
        </plugins>
    </build>
</project>
```

Step 3: Add Velocity as a Dependency
You need to add the Velocity Engine library as a Maven dependency. Add the following to your `pom.xml` within the `<dependencies>` section:

```xml
<dependencies>
    <!-- ... other dependencies ... -->
    <dependency>
        <groupId>org.apache.velocity</groupId>
        <artifactId>velocity-engine-core</artifactId>
        <version>2.3.0</version>
    </dependency>
    <!-- ... other dependencies ... -->
</dependencies>
```

Step 4: Create a Java Class Generation Plugin
Now, create a custom Maven plugin that uses Velocity to generate Java classes based on the template and some parameters. This plugin should execute during the `generate-sources` phase. The plugin will read the Velocity template, replace placeholders with appropriate values, and save the generated Java class in the target directory.

Below is an example of a custom Maven plugin:

```java
import org.apache.maven.plugin.AbstractMojo;
import org.apache.maven.plugin.MojoExecutionException;
import org.apache.maven.plugin.MojoFailureException;
import org.apache.maven.plugins.annotations.Mojo;
import org.apache.velocity.VelocityContext;
import org.apache.velocity.app.Velocity;
import org.apache.velocity.app.VelocityEngine;

import java.io.FileWriter;
import java.io.IOException;
import java.nio.file.Paths;
import java.util.Properties;

@Mojo(name = "generate-classes")
public class ClassGenerationMojo extends AbstractMojo {

    public void execute() throws MojoExecutionException, MojoFailureException {
        try {
            // Load the Velocity template
            VelocityEngine velocityEngine = new VelocityEngine();
            Properties properties = new Properties();
            properties.setProperty("resource.loader", "class");
            properties.setProperty("class.resource.loader.class",
                    "org.apache.velocity.runtime.resource.loader.ClasspathResourceLoader");
            velocityEngine.init(properties);

            // Load the Velocity template from the classpath
            org.apache.velocity.Template template = velocityEngine.getTemplate("MyClassTemplate.vm");

            // Replace placeholders with actual values
            VelocityContext context = new VelocityContext();
            context.put("className", "MyGeneratedClass"); // Replace this with the actual class name you want to generate

            // Define the output directory and file path for the generated Java class
            String outputDir = "src/main/java/com/example/generated"; // Change this to the desired output directory
            String filePath = Paths.get(outputDir, context.get("className") + ".java").toString();

            // Generate the Java class
            FileWriter writer = new FileWriter(filePath);
            template.merge(context, writer);
            writer.flush();
            writer.close();
        } catch (IOException e) {
            throw new MojoExecutionException("Failed to generate Java class", e);
        }
    }
}
```

Step 5: Build and Generate Java Class
With all the configurations and the custom Maven plugin in place, you can now run the `mvn clean install` command. The custom plugin will execute during the `generate-sources` phase, and the Java class will be generated based on the Velocity template.

Remember to adjust the configurations and code above to fit your specific project structure and requirements.

# Add List Size Valid - Customized Annotation

```java
import javax.validation.Constraint;
import javax.validation.Payload;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target({ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
@Constraint(validatedBy = ListSizeValidator.class)
public @interface ListSize {
    String message() default "Invalid list size";
    Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
    int min() default 0;
    int max() default Integer.MAX_VALUE;
}

```

```java
import javax.validation.ConstraintValidator;
import javax.validation.ConstraintValidatorContext;
import java.util.List;

public class ListSizeValidator implements ConstraintValidator<ListSize, List<?>> {

    private int min;
    private int max;

    @Override
    public void initialize(ListSize constraintAnnotation) {
        this.min = constraintAnnotation.min();
        this.max = constraintAnnotation.max();
    }

    @Override
    public boolean isValid(List<?> list, ConstraintValidatorContext constraintValidatorContext) {
        return list == null || (list.size() >= min && list.size() <= max);
    }
}

```

```java
import javax.validation.Valid;
import java.util.List;

public class MyRequest {

    @Valid
    @ListSize(min = 1, max = 10, message = "List must have between 1 and 10 elements")
    private List<String> myList;

    // Getter and setter methods
}

```
