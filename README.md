# ZFS
# 1. Определить алгоритм с наилучшим сжатием
Создаем вагрант-файл, в конфигурацию включим 6 дисков, пропишем необходимые команды для автоустановки ZFS в виртуалку.
Проверям диски командой lsblk
bash "
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
"
