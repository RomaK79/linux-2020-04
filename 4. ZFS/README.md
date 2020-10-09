# ZFS 
## 1. Определить алгоритм с наилучшим сжатием

### Создаём пул устройств хранения данных ZFS
```bash
[vagrant@lvm ~]$ sudo zpool create storage mirror sdb sdc
[vagrant@lvm ~]$ sudo zpool add storage cache sdd sde
[vagrant@lvm ~]$ sudo zpool add storage log mirror sdf sdg
[vagrant@lvm ~]$ zpool status
  pool: storage
 state: ONLINE
  scan: none requested
config:

	NAME        STATE     READ WRITE CKSUM
	storage     ONLINE       0     0     0
	  mirror-0  ONLINE       0     0     0
	    sdb     ONLINE       0     0     0
	    sdc     ONLINE       0     0     0
	logs	
	  mirror-1  ONLINE       0     0     0
	    sdf     ONLINE       0     0     0
	    sdg     ONLINE       0     0     0
	cache
	  sdd       ONLINE       0     0     0
	  sde       ONLINE       0     0     0

errors: No known data errors
```
### Создаём файловые системы 
```bash
[root@lvm compressed]# zfs create storage/compressed01
[root@lvm compressed]# zfs create storage/compressed02
[root@lvm compressed]# zfs create storage/compressed03
[root@lvm compressed]# zfs create storage/compressed04
[root@lvm compressed]# zfs create storage/compressed05

```
### Установим для каждой ФС свой алгоритм сжатия
```bash
[root@lvm compressed]# zfs set compression=lzjb storage/compressed01
[root@lvm compressed]# zfs set compression=gzip storage/compressed02
[root@lvm compressed]# zfs set compression=gzip-9 storage/compressed03
[root@lvm compressed]# zfs set compression=zle storage/compressed04
[root@lvm compressed]# zfs set compression=lz4 storage/compressed05
[root@lvm compressed]# zfs get compression,compressratio
NAME                    PROPERTY       VALUE     SOURCE
storage                 compression    off       default
storage                 compressratio  1.06x     -
storage/compressed01    compression    lzjb      local
storage/compressed01    compressratio  1.00x     -
storage/compressed02    compression    gzip      local
storage/compressed02    compressratio  1.00x     -
storage/compressed03    compression    gzip-9    local
storage/compressed03    compressratio  1.00x     -
storage/compressed04    compression    zle       local
storage/compressed04    compressratio  1.00x     -
storage/compressed05    compression    lz4       local
storage/compressed05    compressratio  1.00x     -

```
### Загрузим текстовый файл 
```bash
[root@lvm compressed01]# wget -O War_and_Peace.txt http://www.gutenberg.org/ebooks/2600.txt.utf-8
[root@lvm compressed01]# cp War_and_Peace.txt ../compressed02
[root@lvm compressed01]# cp War_and_Peace.txt ../compressed03
[root@lvm compressed01]# cp War_and_Peace.txt ../compressed04
[root@lvm compressed01]# cp War_and_Peace.txt ../compressed05
```
### Проверяем уровень сжатия для разных алгоритмов
```bash
[root@lvm compressed01]# zfs get compression,compressratio
NAME                    PROPERTY       VALUE     SOURCE
storage                 compression    off       default
storage                 compressratio  1.07x     -
storage/compressed01    compression    lzjb      local
storage/compressed01    compressratio  1.07x     -
storage/compressed02    compression    gzip      local
storage/compressed02    compressratio  1.08x     -
storage/compressed03    compression    gzip-9    local
storage/compressed03    compressratio  1.08x     -
storage/compressed04    compression    zle       local
storage/compressed04    compressratio  1.08x     -
storage/compressed05    compression    lz4       local
storage/compressed05    compressratio  1.08x     -

```
## 2. Определить настройки pool’a

