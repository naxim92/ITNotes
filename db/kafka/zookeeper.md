#zookeeper #kafka

- Посмотреть брокеры
	`ls /brokers/ids`
- Кто контроллер
	`get /controller`
- Синхронны ли партиции с репликами?
	`get /brokers/topics/<topic_name>/partitions/<partition_number>/state`