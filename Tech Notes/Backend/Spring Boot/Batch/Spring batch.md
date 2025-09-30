```java
@Bean(STEP_ACTIVE_FUTURE_MDR)
public Step exportNCCCInstalmentMDRStep(
        ItemReader<NCCCInstalmentMDR> exportNCCCInstalmentMDRReader,
        ItemProcessor<NCCCInstalmentMDR, NCCCInstalmentMDR> exportNCCCInstalmentMDRProcessor,
        ItemWriter<NCCCInstalmentMDR> exportNCCCInstalmentMDRWriter,
        FlatFileItemWriter<NCCCInstalmentMDR> outputFile
) throws Exception {
    return this.stepBuilderFactory.get(STEP_ACTIVE_FUTURE_MDR)
            .<NCCCInstalmentMDR, NCCCInstalmentMDR>chunk(50)
            .reader(exportNCCCInstalmentMDRReader)
            .processor(exportNCCCInstalmentMDRProcessor)
            .writer(exportNCCCInstalmentMDRWriter)
            .stream(outputFile)
            .build();
}
```

> The `JobParameters#getParameters` returns a unmodifiable map of parameters (See [Javadoc](https://docs.spring.io/spring-batch/4.1.x/api/org/springframework/batch/core/JobParameters.html#getParameters--)). So adding the `pid` key as you did it wont work

## Passing value to ItemProcessor

![[Pasted image 20240318101743.png]]

@Value("#{jobExecutionContext['tempFilePath']}") String tempFilePath
(@Value("#{stepExecutionContext['input.file.name']}") String name

## Row counter

> Declare the counter field at ItemWriter or processor. It will increase 1 at itemwrtier/processor during the bean lifecycle.
> ![[Pasted image 20240326095248.png]]

## CompositeItemWriter

![[Pasted image 20240326095402.png]]
![[Pasted image 20240326095337.png]]

## CompositeStepExecutionListener / CompositeItemWriteListener

![[Pasted image 20240326095640.png]]

# 數據綁定

![[Pasted image 20240808164000.png]]

# Pass Value to Tasklet or Steps

```java
jobExecution.getExecutionContext().put("abc", "@@@@@");
```


```java
String temp1 = stepExecution.getJobExecution().getExecutionContext().getString(BatchJobConstant.UPLOAD_MINIO_PATH);
```

![[Pasted image 20240816165621.png]]