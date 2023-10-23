#linux #swapiness #kernel #sysctl #virt #memory #ram



- Хорошая лекция на тему
	  https://www.youtube.com/watch?v=W5g87nQGNyE&list=RDCMUCetgtvy93o3i3CvyGXKFU3g&index=3
- Как используется парметр *swappiness* в коде
	```
	file_prio = 200 - anon_prio;
	anon_prio = swappiness
	```
- Как посмотреть стату по памяти
	`free -m`
	Нас больше интересует полная выкладка
	`cat /proc/sys/vm/swappiness`
- В этой выкладке видим блоки про Анонимные страницы памяти и Файловые страницы памяти
- Файловые страницы памяти - файловый кэш, т.е. при обращении к файлу файл читается в ОП (файловый кэш). При изменении файла образуются *dirty pages*, которые сбрасываются на диск ОСой.
- Анонимные страницы - страницы в памяти, которые выделяются приложению в течении работы программы.
	Условно псевдокод
	```
	arr Arr = new arr(100000)
	for (int i=0; i<100000; i++)
	{ Arr[i] = i; }
	```
	приведет к тому, что:
	1. ОС зарезервирует память для массива Arr длиной sizeof(int)\*1M ~= 4МБ (*! но не выделит место !*)
	2. Мы можем посмотреть виртуальную память через *ps* колонка *VIRT*
	3. При первом обращении к Arr в цикле ОС выделит анонимный сегмент (4МБ > MMAP_THRESHOLD) с помощью *mmap*, возможно 1 страницу
	4. ОС заполнит страницу нулями
	5. Пока страница не заполнилась, код будет заполнять массив Arr
	6. ОС будет выделять еще страницы памяти и обнулит их по требованию
- Хорошая статья
	https://habr.com/ru/companies/smart_soft/articles/185226/
	![[linux_virt_memory_process.png]]
- И куча и стек выделяются с помощью вызова *mmap*, как анонимные сегменты
	```
	The anonymous memory or anonymous mappings represent memory that is not backed by a filesystem. Such mappings are implicitly created for program's stack and heap or by explicit calls to mmap(2) system call.
	```
- Память поделена на зоны, для приложений под разные архитектуры (16\\32\\64-бит)
	- Zone DMA: first 16MB/24bit for I/O
	- Zone DMA32: 4GB I/O
	- Zone Normal: all further memory
- Например в сообщениях `dmesg` можно увидеть
	``` logs
	Zone PFN ranges:
	DMA 0x00000001 -> 0x00001000
	DMA32 0x00001000 -> 0x00100000
	Normal 0x00100000 -> 0x00200000
	```
- Можно посмотреть инфу о зонах здесь (В wsl почемуто не работает)
	- `cat /proc/zoneinfo`
	-  Еще одно место:
	``` bash
	 cat /proc/buddyinfo
	 Node 0, zone DMA32 7 20 2 4 6 4 3 4 6 5 369
	 ```
	This means that in the DMA32 zone on this machine there are currently 7 free solo 4kb pages, 20 8kb two-page chunks, 2 16kb chunks, and so on, all the way up to 369 1024-page (4 Mbyte) chunks. Since the kernel will split larger chunks to get smaller ones if it needs to, the DMA32 zone on this machine is in pretty good shape despite seeming to not have many order 0 4kb pages available.
- В `zoneinfo` для каждой зоны указаны параметры для начала свапа страниц, а именно `pages free < pages low`
- Подробная статистика по памяти:
	`cat /proc/meminfo`
- `SwapCahced` - страницы свапа, закешированый в памяти. Нужно для каких-то внутренних целей
- Сбросить кэш (разный, в т.ч. дисковый кэш), при этом `free` покажет + в колонке `free`, - в колонке `buff/cache`
	`echo 3 > /proc/sys/vm/drop_caches`
- Cтраницы кэша делятся на "активные" и "неактивные". Идея заключается в том, что если вам нужна память и ее можно взять из кэша, то она будет забрана из неактивных страниц, поскольку ожидается, что она больше не будет использоваться. Подсистема виртуальной памяти постоянно отслеживает, какая память используется, и отмечает это в таблице страниц (pagetable) специальным битом.
- `Active` - память, которая использовалась совсем недавно. Обычно не освобождается без крайней необходимости.
- `Writeback` - память, которая в настоящий момент записывается на диск.
- `Slab` - кеш внутренних структур ядра
- `Committed_AS` - оценка объема оперативной памяти, необходимой для 99,99% гарантии того, что для текущей нагрузки системы не будет OOM (out of memory, нехватки памяти).
- `VmallocTotal` - макс объем памяти (virt), который может быть выделен процессу. Часто макс объем, который может адресовать архитектура #overcommit
- `PageTables` - объем памяти, выделенный для таблиц страниц.
- Посмотреть инфу о памяти и другой статистике конкретного процесса
	`cat /proc/<pid>/status`
- Посмотреть инфу о маппинге в память для процесса (видны все библиотеки и файлы, которые использует процесс)
	``` bash
	cat /proc/<pid>/status
	7ff2f7e3e000-7ff2f7e42000 rw-p 00000000 00:00 0
	7ff2f7e4e000-7ff2f7e4f000 r--p 00000000 00:00 771647             /usr/lib/locale/C.UTF-8/LC_NUMERIC
	7ff2f7e4f000-7ff2f7e50000 r--p 00000000 00:00 771650             /usr/lib/locale/C.UTF-8/LC_TIME
	7ff2f7e50000-7ff2f7e5e000 r--p 00000000 00:00 780209             /usr/lib/x86_64-linux-gnu/libtinfo.so.6.2
	7ff2f7e5e000-7ff2f7e6d000 r-xp 0000e000 00:00 780209             /usr/lib/x86_64-linux-gnu/libtinfo.so.6.2
```
- The maximum number of memory map areas is 262144