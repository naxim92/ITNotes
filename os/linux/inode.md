#linux #inode

## Общее

- `inode` – это объект файловой системы, содержащий информацию о владельце/группе, которым принадлежит файл или каталог, его права доступа к нему, его размер, тип файла, `timestamp`-ы отражающие время модификации индексного дескриптора (_`ctime, changing time`_), время модификации содержимого файла (_`mtime, modification time`_) и время последнего доступа к файлу (_`atime, access time`_) и счётчик для учёта количества жёстких ссылок на файл. Каждый `inode` имеет собственный номер, который присваивается ему файловой системой в момент её создания (форматирования).
- Посмотреть инфу о файловой системе, в т.ч. общее и свободное кол-во inode, блоков, размер блока, размер inode, кол-во зарезервированных блоков (**Reserved block count**) и др
	`tune2fs -l /dev/sdb1`
- Для корневой директории ФС есть **фиксированный** inode `2`
- Посмотреть содержимое inode
	`debugfs /dev/sdb1`
- **Reserved block count** - 5% блоков ФС доступные для записи только руту. Используются, когда закончилось место на диске, а сделать что-то надо
- Узнать номер inode для файла
	` ls -i filename.txt`
- Найти файлы или каталоги по номеру inode
	`find / -inum 1234567`
- Проверить, сколько inode занято на файловой системе
	```
	df -i  
	Filesystem     Inodes  IUsed  IFree IUse% Mounted on  
	/dev/sda1      123456  65432  58024   53% /
	```
- Удалить файл или каталог по номеру inode
	`find / -inum 1234567 -exec rm {} \;`
- При инициализации ФС (ext2) через mke2fs (опция `-T`) можно задать профиль работы ФС - с маленькими, большими или гигантскими файлами
	```
	small = {
		blocksize = 1024
		inode_size = 128
		inode_ratio = 4096
	}
	big = {
		inode_ratio = 32768
	}
	largefile = {
		inode_ratio = 1048576
		blocksize = -1
	}
	```
- Для i386 размер блока не должен превышать 4к, но не обязательно должен быть ровно 4к, т.е. допустимы 1к и 2к. ( Я так понимаю, речь про extN)
	ext2/3/4 is based on the generic Linux VFS framework which requires block size to be less than or equal to page size
	`You may experience mounting problems if block size is greater than page size (i.e. 64KiB blocks on a i386 which only has 4KiB memory pages).`
	 [https://www.kernel.org/doc/html/latest/filesystems/ext4/overview.html](https://www.kernel.org/doc/html/latest/filesystems/ext4/overview.html)
- Maximum number of files per directory: ~1.3 × 10^20
- Полезные утилиты
	`lsyncd` - [Live Syncing (Mirror) Daemon](https://axkibe.github.io/lsyncd/). Он тоже работает через rsync, но дополнительно мониторит файловую систему на предмет изменений с использованием inotify и fsevents и запускает копирование только для тех файлов, которые появились или изменились.
	`iotop` — наподобие `top`, но показывает процессы, наиболее активно использующие диски.
- In an ext2 file system an inode consists of only 15 block pointers. The first 12 block pointers are called as Direct Block pointers. Which means that these pointers point to the address of the blocks containing the data of the file. 12 Block pointers can point to 12 data blocks. So in total the Direct Block pointers can address only 48K(12 * 4K) of data. Which means if the file is only of 48K or below in size, then inode itself can address all the blocks

## Hard link vs soft link
#hardlink #softlink

- **Жесткую ссылку** (_«hard link»_) на файл - ссылка на его inode, получая тот же самый файл (с новым именем), на который указывает ссылка, но без физического создании копии.
- **Символьная ссылка** (сокр. **_«symlink»_** от англ _«**sym**bolic **link**«_), в отличие от жесткой ссылки, указывает не на индексный номер файла, а на его имя (путь). В каком-то роде символьная ссылка является аналогом ярлыка в Windows-системах.
- Создать hardlink
	`ln file1 hardlink1`
- Создать softlink
	`ln -s file1 symlink1`
- **Жесткие ссылки**:
	- не могут пересекать границы файловой системы (т.е. жесткая ссылка работает только в пределах своей файловой системы);
	-  нельзя использовать с директориями;
	- имеют inode и разрешения исходного файла;
	- разрешения будут обновляться при изменении разрешения исходного файла;
	- связаны с содержимым исходного файла. Если вы создадите жесткую ссылку на файл и измените содержимое файла (или ссылки), то изменения будут присутствовать в обоих объектах;
	- с помощью жесткой ссылки вы можете просматривать содержимое файла, даже если исходный файл перемещен или удален.
- **Символьные ссылки**:
	 - могут пересекать границы файловой системы;
	- можно использовать с директориями;
	- имеют свои собственные (отдельные) inode и права доступа;
	- разрешения не будут обновляться;
	- связаны только с именем (путем) исходного файла, а не с его содержимым; удаление символьной ссылки не приводит к удалению файла;
	- можно изменить имя, атрибуты самой ссылки или перенаправить её ссылаться на другой файл, и при этом исходный файл затронут не будет (но имейте ввиду, что если вы по ссылке откроете для редактирования сам файл, то внесете изменения непосредственно в исходный файл).