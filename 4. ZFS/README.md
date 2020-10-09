# ZFS 
## 1. Определить алгоритм с наилучшим сжатием
### Создаём пул устройств хранения данных ZFS
```
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
```
[root@lvm compressed]# zfs create storage/compressed01
[root@lvm compressed]# zfs create storage/compressed02
[root@lvm compressed]# zfs create storage/compressed03
[root@lvm compressed]# zfs create storage/compressed04
[root@lvm compressed]# zfs create storage/compressed05

```
### 
