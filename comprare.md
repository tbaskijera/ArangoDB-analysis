
```shell
toni@toni-WRT-WX9:~$ top

top - 18:58:12 up 1 day,  5:32,  1 user,  load average: 1,99, 0,69, 0,45
Tasks:   1 total,   0 running,   1 sleeping,   0 stopped,   0 zombie
%Cpu(s):  56,5 us,  18,2 sy,  0,0 ni, 20,9 id,  0,1 wa,  0,0 hi,  4,3 si,  0,0 st
MiB Mem :   7694,4 total,   1767,1 free,   2545,8 used,   3381,4 buff/cache
MiB Swap:   2048,0 total,   2019,4 free,     28,6 used.   3657,8 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND 
  13887 root      20   0 1122080 306980  47672 S 456,3   4,1   1:34.34 arangod
  13870 root      20   0  141148   3788   3352 S 144,5   0,0   0:34.39 arangobench


```

```shell
toni@toni-WRT-WX9:~$ top

Tasks: 358 total,   2 running, 356 sleeping,   0 stopped,   0 zombie
%Cpu(s): 40,5 us, 26,3 sy,  0,0 ni, 27,8 id,  0,1 wa,  0,0 hi,  7,3 si,  0,0 st
MiB Mem :   7694,4 total,    280,9 free,   4524,8 used,   3724,0 buff/cache
MiB Swap:   2048,0 total,   1771,8 free,    276,2 used.   1511,7 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND                                                                                
  34101 root      20   0  906708 219988  34260 S  94,0   2,1   1:00.11 arangod                                                                                
  34182 root      20   0  911316 216680  31668 S  93,7   2,0   0:40.01 arangod                                                                                
  37454 root      20   0  141148   3568   3200 S  26,2   0,0   0:30.14 arangobench                                                                            
  34025 root      20   0  712980 159300  31444 S   0,7   2,0   0:38.57 arangod                                                                                
  34770 root      20   0  697936  98964  42332 S   0,7   1,3   0:37.49 arangod                                                                                
  34851 root      20   0  698132 151920  29568 S   0,3   1,9   0:45.34 arangod
  34858 root      20   0  664820 155000  28968 S   0,3   2,0   0:46.54 arangod
  34854 root      20   0  693840  99020  42452 S   0,3   1,3   0:45.64 arangod
  33330 root      20   0  714880  14260   7832 S   0,0   0,2   0:03.70 arangodb                                                                               
  33826 root      20   0  715136  14720   7864 S   0,0   0,2   0:06.13 arangodb                                                                               
  33565 root      20   0  715136  15220   7676 S   0,0   0,2   0:03.68 arangodb  

```
