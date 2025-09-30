![[Pasted image 20230421113738.png|400]]
![[Pasted image 20230421114025.png|400]]

Several different applications will be able to subscribe to the _same_ topic. Each one will then read all the scores at its own pace. Each event will then be consumed by MyAppA and MyAppB.
![[Pasted image 20230421114040.png|400]]

To multiply the processing capacities, it is also possible to add several instances of the _same_ consumer, which will then be registered in the same **consumer group**. Each instance then consumes a subset of the topic's partitions, equally distributed among the consumers.
![[Pasted image 20230421114239.png|400]]