
19-09-2026 11:31

Status:

Tags: [[jcmd - JVM Diagnostics & Monitoring]] [[JVM Diagnostics & Monitoring]]

---
# Thread commands - jcmd


#### `Thread.print`

```bash
PS C:\java-workspace\hotels-rest\hotels-rest> jcmd 35852 Thread.print
35852:
2026-09-19 11:32:36
Full thread dump Java HotSpot(TM) 64-Bit Server VM (23.0.1+11-39 mixed mode, emulated-client, sharing):

Threads class SMR info:
_java_thread_list=0x000001b7f32be820, length=26, elements={
0x000001b7ac475cc0, 0x000001b7ac47a9f0, 0x000001b7ac47b710, 0x000001b7ac47d2e0,
0x000001b7ac47dd40, 0x000001b7ac47e7a0, 0x000001b7ac47f200, 0x000001b7ac73b760,
0x000001b7ac73dde0, 0x000001b7f37adc30, 0x000001b7f38510a0, 0x000001b7f3855f30,
0x000001b7f2835390, 0x000001b7f3ba24c0, 0x000001b7f3b9f040, 0x000001b7f3ba1110,
0x000001b7f3ba17a0, 0x000001b7f3b9fd60, 0x000001b7f4573ad0, 0x000001b7f4570ce0,
0x000001b7f4570650, 0x000001b7f456f2a0, 0x000001b7f4571370, 0x000001b7f45747f0,
0x000001b7f4572720, 0x000001b7f4572db0
}

"Reference Handler" #9 [36272] daemon prio=10 os_prio=2 cpu=0.00ms elapsed=1359.06s tid=0x000001b7ac475cc0 nid=36272 waiting on condition  [0x000000a580bff000]
   java.lang.Thread.State: RUNNABLE
        at java.lang.ref.Reference.waitForReferencePendingList(java.base@23.0.1/Native Method)
        at java.lang.ref.Reference.processPendingReferences(java.base@23.0.1/Reference.java:246)
        at java.lang.ref.Reference$ReferenceHandler.run(java.base@23.0.1/Reference.java:208)

"Finalizer" #10 [35968] daemon prio=8 os_prio=1 cpu=0.00ms elapsed=1359.06s tid=0x000001b7ac47a9f0 nid=35968 in Object.wait()  [0x000000a580cfe000]
   java.lang.Thread.State: WAITING (on object monitor)
        at java.lang.Object.wait0(java.base@23.0.1/Native Method)
        - waiting on <0x000000070d260868> (a java.lang.ref.NativeReferenceQueue$Lock)
        at java.lang.Object.wait(java.base@23.0.1/Object.java:378)
        at java.lang.Object.wait(java.base@23.0.1/Object.java:352)
        at java.lang.ref.NativeReferenceQueue.await(java.base@23.0.1/NativeReferenceQueue.java:48)
        at java.lang.ref.ReferenceQueue.remove0(java.base@23.0.1/ReferenceQueue.java:166)
        at java.lang.ref.NativeReferenceQueue.remove(java.base@23.0.1/NativeReferenceQueue.java:89)
        - locked <0x000000070d260868> (a java.lang.ref.NativeReferenceQueue$Lock)
        at java.lang.ref.Finalizer$FinalizerThread.run(java.base@23.0.1/Finalizer.java:173)

"Signal Dispatcher" #11 [20980] daemon prio=9 os_prio=2 cpu=0.00ms elapsed=1359.06s tid=0x000001b7ac47b710 nid=20980 waiting on condition  [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"Attach Listener" #12 [7444] daemon prio=5 os_prio=2 cpu=0.00ms elapsed=1359.06s tid=0x000001b7ac47d2e0 nid=7444 waiting on condition  [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"Service Thread" #13 [36768] daemon prio=9 os_prio=0 cpu=0.00ms elapsed=1359.06s tid=0x000001b7ac47dd40 nid=36768 runnable  [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"Monitor Deflation Thread" #14 [34324] daemon prio=9 os_prio=0 cpu=15.62ms elapsed=1359.06s tid=0x000001b7ac47e7a0 nid=34324 runnable  [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"C1 CompilerThread0" #15 [35612] daemon prio=9 os_prio=2 cpu=437.50ms elapsed=1359.06s tid=0x000001b7ac47f200 nid=35612 waiting on condition  [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE
   No compile task

"Notification Thread" #27 [19596] daemon prio=9 os_prio=0 cpu=0.00ms elapsed=1359.03s tid=0x000001b7ac73b760 nid=19596 runnable  [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"Common-Cleaner" #28 [11360] daemon prio=8 os_prio=1 cpu=0.00ms elapsed=1359.03s tid=0x000001b7ac73dde0 nid=11360 waiting on condition  [0x000000a5815ff000]
   java.lang.Thread.State: TIMED_WAITING (parking)
        at jdk.internal.misc.Unsafe.park(java.base@23.0.1/Native Method)
        - parking to wait for  <0x000000070d27d9b0> (a java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject)
        at java.util.concurrent.locks.LockSupport.parkNanos(java.base@23.0.1/LockSupport.java:269)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.await(java.base@23.0.1/AbstractQueuedSynchronizer.java:1852)
        at java.lang.ref.ReferenceQueue.await(java.base@23.0.1/ReferenceQueue.java:79)
        at java.lang.ref.ReferenceQueue.remove0(java.base@23.0.1/ReferenceQueue.java:151)
        at java.lang.ref.ReferenceQueue.remove(java.base@23.0.1/ReferenceQueue.java:229)
        at jdk.internal.ref.CleanerImpl.run(java.base@23.0.1/CleanerImpl.java:140)
        at java.lang.Thread.runWith(java.base@23.0.1/Thread.java:1588)
        at java.lang.Thread.run(java.base@23.0.1/Thread.java:1575)
        at jdk.internal.misc.InnocuousThread.run(java.base@23.0.1/InnocuousThread.java:186)

"Catalina-utility-1" #47 [34948] prio=1 os_prio=-2 cpu=46.88ms elapsed=1357.00s tid=0x000001b7f37adc30 nid=34948 waiting on condition  [0x000000a581fff000]
   java.lang.Thread.State: TIMED_WAITING (parking)
        at jdk.internal.misc.Unsafe.park(java.base@23.0.1/Native Method)
        - parking to wait for  <0x000000070d5cc9b0> (a java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject)
        at java.util.concurrent.locks.LockSupport.parkNanos(java.base@23.0.1/LockSupport.java:269)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.awaitNanos(java.base@23.0.1/AbstractQueuedSynchronizer.java:1763)
        at java.util.concurrent.ScheduledThreadPoolExecutor$DelayedWorkQueue.take(java.base@23.0.1/ScheduledThreadPoolExecutor.java:1182)
        at java.util.concurrent.ScheduledThreadPoolExecutor$DelayedWorkQueue.take(java.base@23.0.1/ScheduledThreadPoolExecutor.java:899)
        at java.util.concurrent.ThreadPoolExecutor.getTask(java.base@23.0.1/ThreadPoolExecutor.java:1070)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(java.base@23.0.1/ThreadPoolExecutor.java:1130)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(java.base@23.0.1/ThreadPoolExecutor.java:642)
        at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:63)
        at java.lang.Thread.runWith(java.base@23.0.1/Thread.java:1588)
        at java.lang.Thread.run(java.base@23.0.1/Thread.java:1575)

"Catalina-utility-2" #48 [2788] prio=1 os_prio=-2 cpu=15.62ms elapsed=1357.00s tid=0x000001b7f38510a0 nid=2788 waiting on condition  [0x000000a5827fe000]
   java.lang.Thread.State: WAITING (parking)
        at jdk.internal.misc.Unsafe.park(java.base@23.0.1/Native Method)
        - parking to wait for  <0x000000070d5cc9b0> (a java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject)
        at java.util.concurrent.locks.LockSupport.park(java.base@23.0.1/LockSupport.java:371)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionNode.block(java.base@23.0.1/AbstractQueuedSynchronizer.java:519)
        at java.util.concurrent.ForkJoinPool.unmanagedBlock(java.base@23.0.1/ForkJoinPool.java:4021)
        at java.util.concurrent.ForkJoinPool.managedBlock(java.base@23.0.1/ForkJoinPool.java:3967)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.await(java.base@23.0.1/AbstractQueuedSynchronizer.java:1712)
        at java.util.concurrent.ScheduledThreadPoolExecutor$DelayedWorkQueue.take(java.base@23.0.1/ScheduledThreadPoolExecutor.java:1177)
        at java.util.concurrent.ScheduledThreadPoolExecutor$DelayedWorkQueue.take(java.base@23.0.1/ScheduledThreadPoolExecutor.java:899)
        at java.util.concurrent.ThreadPoolExecutor.getTask(java.base@23.0.1/ThreadPoolExecutor.java:1070)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(java.base@23.0.1/ThreadPoolExecutor.java:1130)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(java.base@23.0.1/ThreadPoolExecutor.java:642)
        at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:63)
        at java.lang.Thread.runWith(java.base@23.0.1/Thread.java:1588)
        at java.lang.Thread.run(java.base@23.0.1/Thread.java:1575)

"container-0" #49 [35764] prio=5 os_prio=0 cpu=0.00ms elapsed=1357.00s tid=0x000001b7f3855f30 nid=35764 waiting on condition  [0x000000a5828ff000]
   java.lang.Thread.State: TIMED_WAITING (sleeping)
        at java.lang.Thread.sleepNanos0(java.base@23.0.1/Native Method)
        at java.lang.Thread.sleepNanos(java.base@23.0.1/Thread.java:496)
        at java.lang.Thread.sleep(java.base@23.0.1/Thread.java:527)
        at org.apache.catalina.core.StandardServer.await(StandardServer.java:518)
        at org.springframework.boot.web.embedded.tomcat.TomcatWebServer$1.run(TomcatWebServer.java:219)

"HikariPool-1:housekeeper" #50 [35980] daemon prio=5 os_prio=0 cpu=15.62ms elapsed=1356.75s tid=0x000001b7f2835390 nid=35980 waiting on condition  [0x000000a5829fe000]
   java.lang.Thread.State: TIMED_WAITING (parking)
        at jdk.internal.misc.Unsafe.park(java.base@23.0.1/Native Method)
        - parking to wait for  <0x000000070d5d0050> (a java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject)
        at java.util.concurrent.locks.LockSupport.parkNanos(java.base@23.0.1/LockSupport.java:269)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.awaitNanos(java.base@23.0.1/AbstractQueuedSynchronizer.java:1763)
        at java.util.concurrent.ScheduledThreadPoolExecutor$DelayedWorkQueue.take(java.base@23.0.1/ScheduledThreadPoolExecutor.java:1182)
        at java.util.concurrent.ScheduledThreadPoolExecutor$DelayedWorkQueue.take(java.base@23.0.1/ScheduledThreadPoolExecutor.java:899)
        at java.util.concurrent.ThreadPoolExecutor.getTask(java.base@23.0.1/ThreadPoolExecutor.java:1070)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(java.base@23.0.1/ThreadPoolExecutor.java:1130)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(java.base@23.0.1/ThreadPoolExecutor.java:642)
        at java.lang.Thread.runWith(java.base@23.0.1/Thread.java:1588)
        at java.lang.Thread.run(java.base@23.0.1/Thread.java:1575)

"http-nio-8092-exec-1" #56 [17508] daemon prio=5 os_prio=0 cpu=0.00ms elapsed=1353.76s tid=0x000001b7f3ba24c0 nid=17508 waiting on condition  [0x000000a582bfe000]
   java.lang.Thread.State: WAITING (parking)
        at jdk.internal.misc.Unsafe.park(java.base@23.0.1/Native Method)
        - parking to wait for  <0x000000070f4811f0> (a java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject)
        at java.util.concurrent.locks.LockSupport.park(java.base@23.0.1/LockSupport.java:371)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionNode.block(java.base@23.0.1/AbstractQueuedSynchronizer.java:519)
        at java.util.concurrent.ForkJoinPool.unmanagedBlock(java.base@23.0.1/ForkJoinPool.java:4021)
        at java.util.concurrent.ForkJoinPool.managedBlock(java.base@23.0.1/ForkJoinPool.java:3967)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.await(java.base@23.0.1/AbstractQueuedSynchronizer.java:1712)
        at java.util.concurrent.LinkedBlockingQueue.take(java.base@23.0.1/LinkedBlockingQueue.java:435)
        at org.apache.tomcat.util.threads.TaskQueue.take(TaskQueue.java:106)
        at org.apache.tomcat.util.threads.TaskQueue.take(TaskQueue.java:31)
        at org.apache.tomcat.util.threads.ThreadPoolExecutor.getTask(ThreadPoolExecutor.java:913)
        at org.apache.tomcat.util.threads.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:960)
        at org.apache.tomcat.util.threads.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:491)
        at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:63)
        at java.lang.Thread.runWith(java.base@23.0.1/Thread.java:1588)
        at java.lang.Thread.run(java.base@23.0.1/Thread.java:1575)

"http-nio-8092-exec-2" #57 [10696] daemon prio=5 os_prio=0 cpu=0.00ms elapsed=1353.76s tid=0x000001b7f3b9f040 nid=10696 waiting on condition  [0x000000a582dfe000]
   java.lang.Thread.State: WAITING (parking)
        at jdk.internal.misc.Unsafe.park(java.base@23.0.1/Native Method)
        - parking to wait for  <0x000000070f4811f0> (a java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject)
        at java.util.concurrent.locks.LockSupport.park(java.base@23.0.1/LockSupport.java:371)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionNode.block(java.base@23.0.1/AbstractQueuedSynchronizer.java:519)
        at java.util.concurrent.ForkJoinPool.unmanagedBlock(java.base@23.0.1/ForkJoinPool.java:4021)
        at java.util.concurrent.ForkJoinPool.managedBlock(java.base@23.0.1/ForkJoinPool.java:3967)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.await(java.base@23.0.1/AbstractQueuedSynchronizer.java:1712)
        at java.util.concurrent.LinkedBlockingQueue.take(java.base@23.0.1/LinkedBlockingQueue.java:435)
        at org.apache.tomcat.util.threads.TaskQueue.take(TaskQueue.java:106)
        at org.apache.tomcat.util.threads.TaskQueue.take(TaskQueue.java:31)
        at org.apache.tomcat.util.threads.ThreadPoolExecutor.getTask(ThreadPoolExecutor.java:913)
        at org.apache.tomcat.util.threads.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:960)
        at org.apache.tomcat.util.threads.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:491)
        at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:63)
        at java.lang.Thread.runWith(java.base@23.0.1/Thread.java:1588)
        at java.lang.Thread.run(java.base@23.0.1/Thread.java:1575)

"http-nio-8092-exec-3" #58 [36092] daemon prio=5 os_prio=0 cpu=0.00ms elapsed=1353.76s tid=0x000001b7f3ba1110 nid=36092 waiting on condition  [0x000000a582efe000]
   java.lang.Thread.State: WAITING (parking)
        at jdk.internal.misc.Unsafe.park(java.base@23.0.1/Native Method)
        - parking to wait for  <0x000000070f4811f0> (a java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject)
        at java.util.concurrent.locks.LockSupport.park(java.base@23.0.1/LockSupport.java:371)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionNode.block(java.base@23.0.1/AbstractQueuedSynchronizer.java:519)
        at java.util.concurrent.ForkJoinPool.unmanagedBlock(java.base@23.0.1/ForkJoinPool.java:4021)
        at java.util.concurrent.ForkJoinPool.managedBlock(java.base@23.0.1/ForkJoinPool.java:3967)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.await(java.base@23.0.1/AbstractQueuedSynchronizer.java:1712)
        at java.util.concurrent.LinkedBlockingQueue.take(java.base@23.0.1/LinkedBlockingQueue.java:435)
        at org.apache.tomcat.util.threads.TaskQueue.take(TaskQueue.java:106)
        at org.apache.tomcat.util.threads.TaskQueue.take(TaskQueue.java:31)
        at org.apache.tomcat.util.threads.ThreadPoolExecutor.getTask(ThreadPoolExecutor.java:913)
        at org.apache.tomcat.util.threads.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:960)
        at org.apache.tomcat.util.threads.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:491)
        at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:63)
        at java.lang.Thread.runWith(java.base@23.0.1/Thread.java:1588)
        at java.lang.Thread.run(java.base@23.0.1/Thread.java:1575)

"http-nio-8092-exec-4" #59 [3224] daemon prio=5 os_prio=0 cpu=0.00ms elapsed=1353.76s tid=0x000001b7f3ba17a0 nid=3224 waiting on condition  [0x000000a582ffe000]
   java.lang.Thread.State: WAITING (parking)
        at jdk.internal.misc.Unsafe.park(java.base@23.0.1/Native Method)
        - parking to wait for  <0x000000070f4811f0> (a java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject)
        at java.util.concurrent.locks.LockSupport.park(java.base@23.0.1/LockSupport.java:371)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionNode.block(java.base@23.0.1/AbstractQueuedSynchronizer.java:519)
        at java.util.concurrent.ForkJoinPool.unmanagedBlock(java.base@23.0.1/ForkJoinPool.java:4021)
        at java.util.concurrent.ForkJoinPool.managedBlock(java.base@23.0.1/ForkJoinPool.java:3967)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.await(java.base@23.0.1/AbstractQueuedSynchronizer.java:1712)
        at java.util.concurrent.LinkedBlockingQueue.take(java.base@23.0.1/LinkedBlockingQueue.java:435)
        at org.apache.tomcat.util.threads.TaskQueue.take(TaskQueue.java:106)
        at org.apache.tomcat.util.threads.TaskQueue.take(TaskQueue.java:31)
        at org.apache.tomcat.util.threads.ThreadPoolExecutor.getTask(ThreadPoolExecutor.java:913)
        at org.apache.tomcat.util.threads.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:960)
        at org.apache.tomcat.util.threads.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:491)
        at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:63)
        at java.lang.Thread.runWith(java.base@23.0.1/Thread.java:1588)
        at java.lang.Thread.run(java.base@23.0.1/Thread.java:1575)

"http-nio-8092-exec-5" #60 [3576] daemon prio=5 os_prio=0 cpu=0.00ms elapsed=1353.76s tid=0x000001b7f3b9fd60 nid=3576 waiting on condition  [0x000000a5830fe000]
   java.lang.Thread.State: WAITING (parking)
        at jdk.internal.misc.Unsafe.park(java.base@23.0.1/Native Method)
        - parking to wait for  <0x000000070f4811f0> (a java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject)
        at java.util.concurrent.locks.LockSupport.park(java.base@23.0.1/LockSupport.java:371)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionNode.block(java.base@23.0.1/AbstractQueuedSynchronizer.java:519)
        at java.util.concurrent.ForkJoinPool.unmanagedBlock(java.base@23.0.1/ForkJoinPool.java:4021)
        at java.util.concurrent.ForkJoinPool.managedBlock(java.base@23.0.1/ForkJoinPool.java:3967)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.await(java.base@23.0.1/AbstractQueuedSynchronizer.java:1712)
        at java.util.concurrent.LinkedBlockingQueue.take(java.base@23.0.1/LinkedBlockingQueue.java:435)
        at org.apache.tomcat.util.threads.TaskQueue.take(TaskQueue.java:106)
        at org.apache.tomcat.util.threads.TaskQueue.take(TaskQueue.java:31)
        at org.apache.tomcat.util.threads.ThreadPoolExecutor.getTask(ThreadPoolExecutor.java:913)
        at org.apache.tomcat.util.threads.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:960)
        at org.apache.tomcat.util.threads.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:491)
        at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:63)
        at java.lang.Thread.runWith(java.base@23.0.1/Thread.java:1588)
        at java.lang.Thread.run(java.base@23.0.1/Thread.java:1575)

"http-nio-8092-exec-6" #61 [36532] daemon prio=5 os_prio=0 cpu=0.00ms elapsed=1353.76s tid=0x000001b7f4573ad0 nid=36532 waiting on condition  [0x000000a5831ff000]
   java.lang.Thread.State: WAITING (parking)
        at jdk.internal.misc.Unsafe.park(java.base@23.0.1/Native Method)
        - parking to wait for  <0x000000070f4811f0> (a java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject)
        at java.util.concurrent.locks.LockSupport.park(java.base@23.0.1/LockSupport.java:371)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionNode.block(java.base@23.0.1/AbstractQueuedSynchronizer.java:519)
        at java.util.concurrent.ForkJoinPool.unmanagedBlock(java.base@23.0.1/ForkJoinPool.java:4021)
        at java.util.concurrent.ForkJoinPool.managedBlock(java.base@23.0.1/ForkJoinPool.java:3967)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.await(java.base@23.0.1/AbstractQueuedSynchronizer.java:1712)
        at java.util.concurrent.LinkedBlockingQueue.take(java.base@23.0.1/LinkedBlockingQueue.java:435)
        at org.apache.tomcat.util.threads.TaskQueue.take(TaskQueue.java:106)
        at org.apache.tomcat.util.threads.TaskQueue.take(TaskQueue.java:31)
        at org.apache.tomcat.util.threads.ThreadPoolExecutor.getTask(ThreadPoolExecutor.java:913)
        at org.apache.tomcat.util.threads.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:960)
        at org.apache.tomcat.util.threads.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:491)
        at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:63)
        at java.lang.Thread.runWith(java.base@23.0.1/Thread.java:1588)
        at java.lang.Thread.run(java.base@23.0.1/Thread.java:1575)

"http-nio-8092-exec-7" #62 [26332] daemon prio=5 os_prio=0 cpu=0.00ms elapsed=1353.76s tid=0x000001b7f4570ce0 nid=26332 waiting on condition  [0x000000a5832fe000]
   java.lang.Thread.State: WAITING (parking)
        at jdk.internal.misc.Unsafe.park(java.base@23.0.1/Native Method)
        - parking to wait for  <0x000000070f4811f0> (a java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject)
        at java.util.concurrent.locks.LockSupport.park(java.base@23.0.1/LockSupport.java:371)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionNode.block(java.base@23.0.1/AbstractQueuedSynchronizer.java:519)
        at java.util.concurrent.ForkJoinPool.unmanagedBlock(java.base@23.0.1/ForkJoinPool.java:4021)
        at java.util.concurrent.ForkJoinPool.managedBlock(java.base@23.0.1/ForkJoinPool.java:3967)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.await(java.base@23.0.1/AbstractQueuedSynchronizer.java:1712)
        at java.util.concurrent.LinkedBlockingQueue.take(java.base@23.0.1/LinkedBlockingQueue.java:435)
        at org.apache.tomcat.util.threads.TaskQueue.take(TaskQueue.java:106)
        at org.apache.tomcat.util.threads.TaskQueue.take(TaskQueue.java:31)
        at org.apache.tomcat.util.threads.ThreadPoolExecutor.getTask(ThreadPoolExecutor.java:913)
        at org.apache.tomcat.util.threads.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:960)
        at org.apache.tomcat.util.threads.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:491)
        at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:63)
        at java.lang.Thread.runWith(java.base@23.0.1/Thread.java:1588)
        at java.lang.Thread.run(java.base@23.0.1/Thread.java:1575)

"http-nio-8092-exec-8" #63 [30244] daemon prio=5 os_prio=0 cpu=0.00ms elapsed=1353.76s tid=0x000001b7f4570650 nid=30244 waiting on condition  [0x000000a5833ff000]
   java.lang.Thread.State: WAITING (parking)
        at jdk.internal.misc.Unsafe.park(java.base@23.0.1/Native Method)
        - parking to wait for  <0x000000070f4811f0> (a java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject)
        at java.util.concurrent.locks.LockSupport.park(java.base@23.0.1/LockSupport.java:371)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionNode.block(java.base@23.0.1/AbstractQueuedSynchronizer.java:519)
        at java.util.concurrent.ForkJoinPool.unmanagedBlock(java.base@23.0.1/ForkJoinPool.java:4021)
        at java.util.concurrent.ForkJoinPool.managedBlock(java.base@23.0.1/ForkJoinPool.java:3967)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.await(java.base@23.0.1/AbstractQueuedSynchronizer.java:1712)
        at java.util.concurrent.LinkedBlockingQueue.take(java.base@23.0.1/LinkedBlockingQueue.java:435)
        at org.apache.tomcat.util.threads.TaskQueue.take(TaskQueue.java:106)
        at org.apache.tomcat.util.threads.TaskQueue.take(TaskQueue.java:31)
        at org.apache.tomcat.util.threads.ThreadPoolExecutor.getTask(ThreadPoolExecutor.java:913)
        at org.apache.tomcat.util.threads.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:960)
        at org.apache.tomcat.util.threads.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:491)
        at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:63)
        at java.lang.Thread.runWith(java.base@23.0.1/Thread.java:1588)
        at java.lang.Thread.run(java.base@23.0.1/Thread.java:1575)

"http-nio-8092-exec-9" #64 [31460] daemon prio=5 os_prio=0 cpu=0.00ms elapsed=1353.76s tid=0x000001b7f456f2a0 nid=31460 waiting on condition  [0x000000a5834fe000]
   java.lang.Thread.State: WAITING (parking)
        at jdk.internal.misc.Unsafe.park(java.base@23.0.1/Native Method)
        - parking to wait for  <0x000000070f4811f0> (a java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject)
        at java.util.concurrent.locks.LockSupport.park(java.base@23.0.1/LockSupport.java:371)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionNode.block(java.base@23.0.1/AbstractQueuedSynchronizer.java:519)
        at java.util.concurrent.ForkJoinPool.unmanagedBlock(java.base@23.0.1/ForkJoinPool.java:4021)
        at java.util.concurrent.ForkJoinPool.managedBlock(java.base@23.0.1/ForkJoinPool.java:3967)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.await(java.base@23.0.1/AbstractQueuedSynchronizer.java:1712)
        at java.util.concurrent.LinkedBlockingQueue.take(java.base@23.0.1/LinkedBlockingQueue.java:435)
        at org.apache.tomcat.util.threads.TaskQueue.take(TaskQueue.java:106)
        at org.apache.tomcat.util.threads.TaskQueue.take(TaskQueue.java:31)
        at org.apache.tomcat.util.threads.ThreadPoolExecutor.getTask(ThreadPoolExecutor.java:913)
        at org.apache.tomcat.util.threads.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:960)
        at org.apache.tomcat.util.threads.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:491)
        at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:63)
        at java.lang.Thread.runWith(java.base@23.0.1/Thread.java:1588)
        at java.lang.Thread.run(java.base@23.0.1/Thread.java:1575)

"http-nio-8092-exec-10" #65 [9396] daemon prio=5 os_prio=0 cpu=0.00ms elapsed=1353.76s tid=0x000001b7f4571370 nid=9396 waiting on condition  [0x000000a5835fe000]
   java.lang.Thread.State: WAITING (parking)
        at jdk.internal.misc.Unsafe.park(java.base@23.0.1/Native Method)
        - parking to wait for  <0x000000070f4811f0> (a java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject)
        at java.util.concurrent.locks.LockSupport.park(java.base@23.0.1/LockSupport.java:371)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionNode.block(java.base@23.0.1/AbstractQueuedSynchronizer.java:519)
        at java.util.concurrent.ForkJoinPool.unmanagedBlock(java.base@23.0.1/ForkJoinPool.java:4021)
        at java.util.concurrent.ForkJoinPool.managedBlock(java.base@23.0.1/ForkJoinPool.java:3967)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.await(java.base@23.0.1/AbstractQueuedSynchronizer.java:1712)
        at java.util.concurrent.LinkedBlockingQueue.take(java.base@23.0.1/LinkedBlockingQueue.java:435)
        at org.apache.tomcat.util.threads.TaskQueue.take(TaskQueue.java:106)
        at org.apache.tomcat.util.threads.TaskQueue.take(TaskQueue.java:31)
        at org.apache.tomcat.util.threads.ThreadPoolExecutor.getTask(ThreadPoolExecutor.java:913)
        at org.apache.tomcat.util.threads.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:960)
        at org.apache.tomcat.util.threads.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:491)
        at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:63)
        at java.lang.Thread.runWith(java.base@23.0.1/Thread.java:1588)
        at java.lang.Thread.run(java.base@23.0.1/Thread.java:1575)

"http-nio-8092-Poller" #66 [36596] daemon prio=5 os_prio=0 cpu=31.25ms elapsed=1353.76s tid=0x000001b7f45747f0 nid=36596 runnable  [0x000000a5836ff000]
   java.lang.Thread.State: RUNNABLE
        at sun.nio.ch.WEPoll.wait(java.base@23.0.1/Native Method)
        at sun.nio.ch.WEPollSelectorImpl.doSelect(java.base@23.0.1/WEPollSelectorImpl.java:114)
        at sun.nio.ch.SelectorImpl.lockAndDoSelect(java.base@23.0.1/SelectorImpl.java:130)
        - locked <0x000000070f484158> (a sun.nio.ch.Util$2)
        - locked <0x000000070f483ef0> (a sun.nio.ch.WEPollSelectorImpl)
        at sun.nio.ch.SelectorImpl.select(java.base@23.0.1/SelectorImpl.java:142)
        at org.apache.tomcat.util.net.NioEndpoint$Poller.run(NioEndpoint.java:773)
        at java.lang.Thread.runWith(java.base@23.0.1/Thread.java:1588)
        at java.lang.Thread.run(java.base@23.0.1/Thread.java:1575)

"http-nio-8092-Acceptor" #67 [34508] daemon prio=5 os_prio=0 cpu=0.00ms elapsed=1353.76s tid=0x000001b7f4572720 nid=34508 runnable  [0x000000a5837ff000]
   java.lang.Thread.State: RUNNABLE
        at sun.nio.ch.Net.accept(java.base@23.0.1/Native Method)
        at sun.nio.ch.ServerSocketChannelImpl.implAccept(java.base@23.0.1/ServerSocketChannelImpl.java:434)
        at sun.nio.ch.ServerSocketChannelImpl.accept(java.base@23.0.1/ServerSocketChannelImpl.java:400)
        at org.apache.tomcat.util.net.NioEndpoint.serverSocketAccept(NioEndpoint.java:535)
        at org.apache.tomcat.util.net.NioEndpoint.serverSocketAccept(NioEndpoint.java:70)
        at org.apache.tomcat.util.net.Acceptor.run(Acceptor.java:127)
        at java.lang.Thread.runWith(java.base@23.0.1/Thread.java:1588)
        at java.lang.Thread.run(java.base@23.0.1/Thread.java:1575)

"DestroyJavaVM" #68 [34788] prio=5 os_prio=0 cpu=4234.38ms elapsed=1353.75s tid=0x000001b7f4572db0 nid=34788 waiting on condition  [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"G1 Conc#2" os_prio=2 cpu=0.00ms elapsed=1357.96s tid=0x000001b7f2d8db10 nid=32520 runnable

"G1 Conc#1" os_prio=2 cpu=0.00ms elapsed=1357.96s tid=0x000001b7f2d8d3b0 nid=21468 runnable

"GC Thread#12" os_prio=2 cpu=31.25ms elapsed=1357.99s tid=0x000001b7f2d8cc50 nid=36132 runnable

"GC Thread#11" os_prio=2 cpu=15.62ms elapsed=1357.99s tid=0x000001b7f2d8c140 nid=36356 runnable

"GC Thread#10" os_prio=2 cpu=15.62ms elapsed=1357.99s tid=0x000001b7f2d8f130 nid=36360 runnable

"GC Thread#9" os_prio=2 cpu=31.25ms elapsed=1357.99s tid=0x000001b7f2d8dec0 nid=36364 runnable

"GC Thread#8" os_prio=2 cpu=46.88ms elapsed=1358.43s tid=0x000001b7f2747ec0 nid=34372 runnable

"GC Thread#7" os_prio=2 cpu=62.50ms elapsed=1358.43s tid=0x000001b7f2747b10 nid=10508 runnable

"GC Thread#6" os_prio=2 cpu=46.88ms elapsed=1358.68s tid=0x000001b7f2747000 nid=30228 runnable

"GC Thread#5" os_prio=2 cpu=46.88ms elapsed=1358.68s tid=0x000001b7f27468a0 nid=34220 runnable

"GC Thread#4" os_prio=2 cpu=62.50ms elapsed=1358.69s tid=0x000001b7f272bf80 nid=12568 runnable

"GC Thread#3" os_prio=2 cpu=15.62ms elapsed=1358.69s tid=0x000001b7f26fd700 nid=32572 runnable

"GC Thread#2" os_prio=2 cpu=78.12ms elapsed=1358.69s tid=0x000001b7f26fcf40 nid=5372 runnable

"GC Thread#1" os_prio=2 cpu=31.25ms elapsed=1358.69s tid=0x000001b7f2729bc0 nid=3036 runnable

"VM Thread" os_prio=2 cpu=93.75ms elapsed=1359.07s tid=0x000001b7ac462a00 nid=36072 runnable

"VM Periodic Task Thread" os_prio=2 cpu=109.38ms elapsed=1359.07s tid=0x000001b7ac44f410 nid=36244 waiting on condition

"G1 Service" os_prio=2 cpu=0.00ms elapsed=1359.08s tid=0x000001b7ac321ae0 nid=22928 runnable

"G1 Refine#0" os_prio=2 cpu=140.62ms elapsed=1359.08s tid=0x000001b7ac31c380 nid=35896 runnable

"G1 Conc#0" os_prio=2 cpu=0.00ms elapsed=1359.08s tid=0x000001b798bf89e0 nid=36248 runnable

"G1 Main Marker" os_prio=2 cpu=0.00ms elapsed=1359.08s tid=0x000001b798bf7ed0 nid=36252 runnable

"GC Thread#0" os_prio=2 cpu=46.88ms elapsed=1359.08s tid=0x000001b798be3d00 nid=18208 runnable

JNI global refs: 28, weak refs: 0
```

C1, C2 Compilation - потоки, относящиеся к JIT.


----
#### [[Thread commands - jcmd - Flashcards|Link to flashcards]]



---
### References:

