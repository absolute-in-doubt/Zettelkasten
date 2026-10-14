
19-09-2026 11:27

Status:

Tags: [[jcmd - JVM Diagnostics & Monitoring]] [[JVM Diagnostics & Monitoring]]

---
# GC commands - jcmd


#### `GC.heap_info`

```bash
PS C:\java-workspace\hotels-rest\hotels-rest> jcmd 35852 GC.heap_info
35852:
 garbage-first heap   total reserved 3983360K, committed 92160K, used 59101K [0x000000070ce00000, 0x0000000800000000)
  region size 2048K, 14 young (28672K), 3 survivors (6144K)
 Metaspace       used 80541K, committed 81344K, reserved 1179648K
  class space    used 11608K, committed 11968K, reserved 1048576K

```

----
#### [[GC commands - jcmd - Flashcards|Link to flashcards]]



---
### References:

