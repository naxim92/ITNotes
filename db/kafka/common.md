#kafka

## FAQ

- kafka хранит бинарные логи
- 1 топик - 1 консъюмер из группы
- Хочешь больше консъюмеров (параллелилизм) - добавляй партиции в топик
- kafka гарантирует порядок только внутри партиции
- Хочешь сохранять определенную логику внутри топика при нескольких партициях - это должен делать поставщик, например с помощью `key:value`  заголовков (properties)
- В нашем случае, oracle debezium connector не гарантирует порядка на уровне топика
- Оффсеты хранятся в специальном топике `__consumer_offsets`
- Ждёт ли producer подтверждения (синхрон\\асинхрон)? - зависит от настройки самого поставщика - `acks`
- Надежная доставка только при `acks=all`
- После изменения кол-ва партиций\\консъюсеров происходит ребалансировка, т.е. заново каждому консъюмеру назначается партиции
- Во время ребалансировки консъюмеры не могу читать
- Клиент посылает bootstrap запрос, любой из серверов, посылает ответ с адресом правильного leader, к которому надо обращаться для работы с конкретным топиком\\партицией
- Консъюмер может выбрать, сколько данных выбрать за раз, а-ля batch
- После обработки сообщения консъюмером, консъюмер должен закоммитить сообщение, это приведет к тому, что в топике `__consumer_offsets` передвинется оффсет партиции
- Есть разные виды коммитов синхронные и асинхронные, при этом точный оффсет, который коммиттся, различается
- При выборе способа коммита, надо иметь в виду что, могут возникать дубликаты, из-за особенностей вида коммита
	Пример дубликата:
	- клиент взял пачку из 10 сообщений
	- включен автокоммит
	- обработал 8 сообщений
	- сработал автокоммит
	- обработал 9-ое сообщение
	- на 10-ом сообщении консъюмер упал
	- прошла ребалансировка == поднялся консъюмер 
	- консъюмер продолжил обрабатывать следующее сообщение, за прошлым коммитом, т.е. 9-ое
	- Консъюмер (group-leader) назначает всем конъюмерам партиции - именно поэтому можно сделать свой алгоритм распределения
	- Брокер (group-leader) выполняет поддерживает актуальный список консъюмеров

## Common

- kafka - брокер сообщений, data streaming platform
- Реализует FIFO
- Классическая инсталляция Kafka — это некий кластер с минимум тремя нодами, чтобы данные копировались как минимум дважды.
- Самый большой бенефит Kafka по сравнению с RabbitMQ или Pulsar — это её экосистема. Это количество коннекторов, приложений, проприетарных решений, которые просто из коробки будут работать с Kafka.
- Состоит из элементов:
	- zookeeper - кластерная система хранения конфигурации
	- брокер - сервер хранения данных
	- топик - логическая структура, где хранятся данные
	- партиция - "физическая" структура, где хранятся данные
	- консьюмер - потребитель, программа, которая читает данные из kafka
	- консьюмер-группа - группа потребителей, которые шарят между собой набор партиций и политики доступа к ним
	- продьюсер - поставщик, программа, которая записывает данные в kafka
-  Клиенты (консьюмеры и продьюссеры) для хранения данных **обращаются** к топикам, расположенным на брокерах, в отличие от RabbitMQ, где сам брокер отдает данные клиентам
- Топики делятся на партиции для:
	- распараллеливания работы с данными - в один момент времени работать с одной партицией может только один консьюмер
	- отказоустойчивости - партиции могут дублироваться на разных физических серверах в режиме active-standby
- Партиция может быть:
	- leader
	- ISR - in-sync replica (follower)
- Пример работы с consumer-group и параллелелизмом: ![[kafka_consumer_group_example.png]]

- Данные не изменяются и не удаляются, мы можем только добавить данные
- Данные не удаляются при протчении консьюмером. Они могут удаляться, после какого-то времени или места
- Параметры топиков:
	- `retention.bytes` - удалять сообщение при достижении порога в байтах(default=-1)
	- `retention.ms` - удалять сообщение, если прошло сколько-то времени
	- `log.retention.hours` - время в часах (default=1 week)
	- `-1` = бесконечность
- По дефолту (без доп фич) порядок данных гарантируется только внутри партиции, но не внутри топика
- Синхронность работы продъюссера зависит от режима работы продъюссера, т.е. параметра `ack`:
	- `acks=0` - асинхронный режим работы
		- поставщик отправляет данные kafka, и не ожидает надежной записи
		- ненадежно, данные могут даже не дойти до kafka
		- `offset=-1` в пакете от сервера (как я понял, они всё-таки шлются поставщику)
	- `acks=1` - default
		- kafka отправляет acknowledgement, когда leader записал данные в bin лог
		- не особо надежно, т.к. leader - точка отказа
		- данные потеряются, если leader упадет до синхронизации с in-sync репликами
	- `acks=all`
		- kafka запишет данные во все реплики, и только после этого отошлет поставщику acknowledgement 
		- надежно
- Брокер может стать **group-coordinator**
- **Group-coordinator** - поддерживает актаульный список консъюмеров
- **Group сoordinator** посылает heartbeat на все консъюмеры группы, если не вернулся ответ - консъюмер мертв, инициируем `REBALANCING`
- Консъюмер может стать **group-leader** 
- **Group-leader** ответственен за назначение партиций консъюмерам (!) *consumer назначает партиции другим consumer* (проводит `REBALANCING`)
- При изменении кол-ва партиций\\консъюмеров начинается `REBALANCING` - каждому консъюмеру выбирается заново набор партиций для обработки, подробнее [здесь](https://www.conduktor.io/kafka/consumer-incremental-rebalance-and-static-group-membership/)
- Консъюмерам назначаются партиции динамически или статически, если указан параметр `group.instance.id` [[configuration]]
- Динамисечкое назначение управляется алгоритмом, указанным в параметре `**partition.assignment.strategy**` [[configuration]]
- Пример сообщения для kafka:![[kafka_message.png]]
- Клиент посылает bootstrap запрос, любой из серверов, посылает ответ с адресом правильного leader, к которому надо обращаться для работы с конкретным топиком\\партицией
- Когда уже есть рабочий топик и мы к нему добавляем новую группу консъюмеров (еще одну), то мы можем указать новой группе с какой позиции начинать обрабатывать сообщения, за это отвечает параметр `auto.offset.reset`
- После того, как консъюмер считал сообщения в партиции, он начинает их обрабатывать и, после успешной обработки, должен закоммитить изменения
- Каждый вызов commit API
- По-умолчанию консъюмер автоматически коммитит все обработанные сообщения через определенное время (5 сек), это настройка `enable.auto.commit`
	- это даёт нам **как минимум одну обработку** (“at least once”) одного сообщения, потому что клиент может обработать, упасть и не успеть заккомитить
	- в процессе обработки могут появляться дубликаты
- Есть ручные синхронные коммиты
	- надежные
	- блокируют поток выполнения программы
- Есть ручные асинхронные коммиты
- По коммитам можно смореть [сюда](https://medium.com/@rramiz.rraza/kafka-programming-different-ways-to-commit-offsets-7bcd179b225a)