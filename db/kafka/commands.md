#kafka 

- Посмотреть топик `__consumer_offsets` :
	  `kafka-console-consumer.sh --bootstrap-server=kafka:9092 --topic=__consumer_offsets --from-beginning --formatter "kafka.coordinator.group.GroupMetadataManager\$OffsetsMessageFormatter"`
- Формат `__consumer_offsets` топика :
	```
	[**groupId**,**topicName**,**partitionNumber**]::[OffsetMetadata[**OffsetNumber**,NO_METADATA],CommitTime 1520613132835,ExpirationTime 1520699532835]
	```
- Посмотреть все consumer-group:
	`kafka-run-class.sh kafka.admin.ConsumerGroupCommand --bootstrap-server kafka:9092 --list`
- Посмотреть на конкретную группу и её консъюмеры
	  `kafka-run-class.sh kafka.admin.ConsumerGroupCommand --bootstrap-server kafka:9092 --group <GROUP_NAME> --describe`
	  (!) Оффсет для партиции, не для топика
	`CURRENT-OFFSET` - текущий номер записи, который сейчас обрабатывается 
	`LOG-END-OFFSET` - номер записи конца партиции
	`LAG` - сколько осталось до конца партиции