```java
public class DataProcessor implements Processor < String, String > {
  private ProcessorContext context;

  @Override
  public void init(ProcessorContext context) {
    this.context = context;
  }

  @Override
  public void process(String key, String value) {
    if (value.contains("null")) {
      // Add code for processing missing records – To be done in Step 5
    } else {
      processRecord(key, value);

      context.forward(key, value);
    }
    context.commit();
  }

  @Override
  public void close() {}

  private void processRecord(String key, String value) {
    System.out.println("==== Record Processed ==== key: " + key + " and value: " + value);

  }
}
```

This class allows us to process records, and when the value misses a **null** string, it is moved on to the sink topic

In the **KafkaStreaming** class below, you piece together the parts that define the source topic, add both the processor and the sink
```java
// Read data from OUTER_JOIN_STREAM_OUT_TOPIC topic
topology.addSource("Source", OUTER_JOIN_STREAM_OUT_TOPIC);

// Add a stream processor – To be done in Step 5 
topology.addProcessor("StateProcessor",
					  new ProcessorSupplier < String, String > () {
						  public Processor < String, String > get() { return new DataProcessor();}},
						  "Source");

topology.addSink("Sink", PROCESSED_STREAM_OUT_TOPIC, "StateProcessor");
```