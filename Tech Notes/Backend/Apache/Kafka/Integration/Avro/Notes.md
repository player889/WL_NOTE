maven lib
- kafka-avro-serializer
- kafka-schema-registry-client
- avro
- avro-maven-plugin (build)

create source file name with extension `.avsc` in sourceDirectory
`mvn clean install` avro generated the source file


Java class -> avro
```java
public class PojoToAvroConverter {
  public static void main(String[] args) throws IOException {
    Schema schema = ReflectData.get().getSchema(LoanDetailPojo.class);
    File schemaFile = new File("src/main/resources/loan-detail.avsc");
    FileWriter fileWriter = new FileWriter(schemaFile);
    fileWriter.append(schema.toString(true));
    fileWriter.close();
  }
}
```