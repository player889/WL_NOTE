```java
@Bean
public JdbcBatchItemWriter<MyItem> databaseWriter(DataSource dataSource) {
    return new JdbcBatchItemWriterBuilder<MyItem>()
        .itemSqlParameterSourceProvider(new BeanPropertyItemSqlParameterSourceProvider<>())
        .sql("INSERT INTO my_table (field1, field2, field3) VALUES (:field1, :field2, :field3)")
        .dataSource(dataSource)
        .assertUpdates(false)  // This allows us to check the number of affected rows
        .build();
}
```

```java
public class CustomCompositeWriter implements ItemWriter<MyItem> {

    private final JdbcBatchItemWriter<MyItem> databaseWriter;
    private final FlatFileItemWriter<MyItem> csvWriter;

    public CustomCompositeWriter(JdbcBatchItemWriter<MyItem> databaseWriter, FlatFileItemWriter<MyItem> csvWriter) {
        this.databaseWriter = databaseWriter;
        this.csvWriter = csvWriter;
    }

    @Override
    public void write(List<? extends MyItem> items) throws Exception {
        // Write to database and get the results
        List<MyItem> updatedItems = new ArrayList<>(items.size());
        for (MyItem item : items) {
            int affected = databaseWriter.write(Collections.singletonList(item));
            item.setInsertResult(affected > 0 ? "success" : "failed");
            updatedItems.add(item);
        }

        // Write to CSV
        csvWriter.write(updatedItems);
    }
}

```

```java
@Bean
public ItemWriter<MyItem> compositeWriter(JdbcBatchItemWriter<MyItem> databaseWriter, FlatFileItemWriter<MyItem> csvWriter) {
    return new CustomCompositeWriter(databaseWriter, csvWriter);
}
```

```java
@Bean
public Step step1(StepBuilderFactory stepBuilderFactory,
                  FlatFileItemReader<MyItem> reader,
                  ItemWriter<MyItem> compositeWriter,
                  PlatformTransactionManager transactionManager) {
    return stepBuilderFactory.get("step1")
        .<MyItem, MyItem>chunk(10)
        .reader(reader)
        .writer(compositeWriter)
        .transactionManager(transactionManager)
        .build();
}

```
