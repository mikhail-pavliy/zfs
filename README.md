## ZFS
# 1. Определить алгоритм с наилучшим сжатием
Создаем вагрант-файл, в конфигурацию включим 6 дисков, пропишем необходимые команды для автоустановки ZFS в виртуалку.
Проверям диски командой lsblk
```ruby
[vagrant@ZFS ~]$ lsblk 
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   40G  0 disk 
└─sda1   8:1    0   40G  0 part /
sdb      8:16   0    1G  0 disk 
├─sdb1   8:17   0 1014M  0 part 
└─sdb9   8:25   0    8M  0 part 
sdc      8:32   0    1G  0 disk 
├─sdc1   8:33   0 1014M  0 part 
└─sdc9   8:41   0    8M  0 part 
sdd      8:48   0    1G  0 disk 
sde      8:64   0    1G  0 disk 
sdf      8:80   0    1G  0 disk 
sdg      8:96   0    1G  0 disk 
```
Создадим пул командой zpool create
```ruby
[vagrant@ZFS ~]$ zpool create -f mypool1 raidz2 sdb sdc sdd sde
[vagrant@ZFS ~]$ zpool list
NAME      SIZE  ALLOC   FREE  CKPOINT  EXPANDSZ   FRAG    CAP  DEDUP    HEALTH  ALTROOT
mypool1  3.75G   296K  3.75G        -         -     0%     0%  1.00x    ONLINE  -
```
Создаем файловые ситсемы с разной степенью сжатия
```ruby
[vagrant@ZFS ~]$ zfs create mypool/gzip
[vagrant@ZFS ~]$ zfs create mypool/lzjb
[vagrant@ZFS ~]$ zfs create mypool/zle
[vagrant@ZFS ~]$ zfs create mypool/lz4
```
Прверяем командой df -h
```ruby
[vagrant@ZFS ~]$ df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        912M     0  912M   0% /dev
tmpfs           919M     0  919M   0% /dev/shm
tmpfs           919M  8.6M  911M   1% /run
tmpfs           919M     0  919M   0% /sys/fs/cgroup
/dev/sda1        40G  3.1G   37G   8% /
tmpfs           184M     0  184M   0% /run/user/1000
tmpfs           184M     0  184M   0% /run/user/0
mypool         1.8G  128K  1.8G   1% /mypool
mypool/lz4     1.8G  128K  1.8G   1% /mypool/lz4
mypool/gzip    1.8G  128K  1.8G   1% /mypool/gzip
mypool/lzjb    1.8G  128K  1.8G   1% /mypool/lzjb
mypool/zle     1.8G  128K  1.8G   1% /mypool/zle
```
устанвливаем степень сжатия командой zfs set compression
```ruby
[vagrant@ZFS ~]$ zfs set compression=lz4 mypool/lz4
[vagrant@ZFS ~]$ zfs set compression=zle mypool/zle
[vagrant@ZFS ~]$ zfs set compression=lzjb mypool/lzjb
[vagrant@ZFS ~]$ zfs set compression=gzip mypool/gzip
```
Скачиваем нужный файл командой wget 
копируем наш файлик в наши дириктории командой CP
проверяем степень сжатия командой zfs list 
```ruby
zfs list 
NAME           USED  AVAIL     REFER  MOUNTPOINT
mypool       19.7M  1.72G     3.91M  /mypool
mypool/gzip  3.86M  1.72G     3.86M  /mypool/gzip
mypool/lz4   3.86M  1.72G     3.86M  /mypool/lz4
mypool/lzjb  3.88M  1.72G     3.88M  /mypool/lzjb
mypool/zle   3.87M  1.72G     3.87M  /mypool/zle
```
Как видно  на примере преимущество gzip  и  lz4. 

# 2. Определить настройки pool’a
Скачиваем и разахивируем архив с заданием:
```ruby
[root@ZFS vagrant]$ wget -q --no-check-certificate 'https://docs.google.com/uc?export=download&id=1KRBNW33QWqbvbVHa3hLJi 
vOAt60yukkg' -O zfs_task1.tar.gz
[root@ZFS vagrant]$ tar xf zfs_task1.tar.gz
```
Импортируем скачанный pool в нашу систему командой zpool import:
```ruby
[root@ZFS vagrant]$ zpool import -d zpoolexport otus
```
проверим статус нашего пула 
```ruby
[root@ZFS vagrant]$ sudo zpool status otus
ool: otus
 state: ONLINE
  scan: none requested
config:

	NAME                                 STATE     READ WRITE CKSUM
	otus                                 ONLINE       0     0     0
	  mirror-0                           ONLINE       0     0     0
	    /home/vagrant/zpoolexport/filea  ONLINE       0     0     0
	    /home/vagrant/zpoolexport/fileb  ONLINE       0     0     0

errors: No known data errors
```
По истории работы с pool'ом определяем его параметры: тип pool'а, метод сжатия и checksum
```ruby
[root@ZFS vagrant]$ zpool history otus|grep create
[root@ZFS vagrant]$ zpool history otus|grep create
[root@ZFS vagrant]$ zpool history otus|grep checksum
[root@ZFS vagrant]$ zpool history otus|grep compression
```
Размер - 480M
Тип - mirror
checksum - sha256
compression - zle
# 3. Найти сообщение от преподавателей
Скачиваем файл с заданием и монтируем его в нашу систему:
```ruby
[vagrant@ZFS ~]$ wget -q --no-check-certificate 'https://docs.google.com/uc?export=download&id=1gH8gCL9y7Nd5Ti3IRmplZPF1 
XjzxeRAG' -O otus_task2.file
[vagrant@ZFS ~]$ zfs create otus/storage-task2
[vagrant@ZFS ~]$ zfs receive otus/storage < otus_task2.file 
```
ищем сообщение
```ruby
[root@ZFS vagrant]$ cat /otus/storage/task1/file_mess/secret_message
https://github.com/sindresorhus/awesome
```
