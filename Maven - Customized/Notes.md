```shell
mvn io.fredia:test-maven-plugin:1.0-SNAPSHOT:hello

mvn org.example:generator-maven-plugin:hello

```

如果要執行的是你本地庫中最新版本的外掛，那麼你可以刪除掉版本號，並依照命名規則 `<myplugin>-maven-plugin`，則可以縮短執行命令。`Only for runner module.`

```shell
mvn generator:hello
```

---

TO CHECK

```xml
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.1</version>
        <configuration>
          <source>1.6</source>
          <target>1.6</target>
          <encoding>UTF-8</encoding>
          <includes>
            <include>src/main/java/**/*.java</include>
            <include>src-gen/gen/java/**/*.java</include>
            <include>src/extra/java/**/*.java</include>
          </includes>
        </configuration>
      </plugin>
```

---

Libs

```xml

<!--archetype -->
<dependency>
    <groupId>org.apache.maven.archetypes</groupId>
    <artifactId>maven-archetype-mojo</artifactId>
    <version>1.0</version>
</dependency>

<!--lib -->
<dependency>
	<groupId>org.apache.maven.plugin-tools</groupId>
	<artifactId>maven-plugin-annotations</artifactId>
	<version>3.6.1</version>
	<scope>provided</scope>
</dependency>
<dependency>
	<groupId>org.apache.maven</groupId>
	<artifactId>maven-plugin-api</artifactId>
	<version>3.6.1</version>
	<scope>provided</scope>
</dependency>


<!-- -->
<build>
    <plugins>
        <plugin>
            <groupId>io.fredia</groupId>
            <artifactId>test-maven-plugin</artifactId>
            <version>1.0-SNAPSHOT</version>
            <executions>
                <execution>
                    <goals>
                        <goal>hello</goal>
                    </goals>
                    <phase>package</phase>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```
