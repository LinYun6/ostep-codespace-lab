## Q1
- Prediction / 预测: Total Time 10, CPU Busy 10 (100%), IO Busy 0 (0%) / 总时间10，CPU利用率100%，I/O利用率0%
- Reasoning / 理由: Since both processes consist entirely of CPU operations (5:100, 5:100) and perform no I/O, the CPU remains busy throughout the entire execution. PID 0 completes its 5 instructions first, followed by PID 1 completing its 5 instructions, resulting in a total of 10 ticks. / 两个进程都只执行 CPU 操作（5:100, 5:100），没有进行任何 I/O 操作，因此 CPU 在整个执行过程中始终保持运行状态。PID 0 先完成 5 条指令，随后 PID 1 完成 5 条指令，总共需要 10 个 tick。
- Verified result / 验证结果:
- Analysis / 分析:

## Q2
- Prediction / 预测: Total Time 11, CPU Busy 6 (54.55%), IO Busy 5 (45.45%) / 总时间11，CPU利用率54.55%，I/O利用率45.45%
- Reasoning / 理由: PID 0 executes all 4 of its CPU instructions first (ticks 1–4), as there is no I/O operation to cause a context switch. After that, PID 1 begins its I/O operation: it takes 1 tick to issue the I/O request (RUN), remains BLOCKED for 5 ticks while the device processes the request, and then spends 1 tick handling the completion (RUN). Because PID 0 has already completed, the CPU remains idle during those 5 blocked ticks. / PID 0 首先连续执行完4条 CPU 指令（tick 1–4），因为没有 I/O 操作触发进程切换。之后 PID 1 开始执行 I/O：花费1个 tick 发起 I/O 请求（RUN），设备处理期间 BLOCKED 5 个 tick，最后花费1个 tick 处理 I/O 完成事件（RUN）。由于 PID 0 已经执行结束，因此在这5个 BLOCKED 的 tick 期间，CPU 处于空闲状态。
- Verified result / 验证结果:
- Analysis / 分析:

## Q3
- Prediction / 预测:Total Time 7, CPU Busy 6 (85.71%), IO Busy 5 (71.43%) / 总时间7，CPU利用率85.71%，I/O利用率71.43%
- Reasoning / 理由: PID 0 starts I/O at tick 1 and then switches to PID 1 because of SWITCH_ON_IO. PID 1 runs its 4 CPU instructions during ticks 2–5. Tick 6 is idle because PID 0's I/O is still not finished. At tick 7, PID 0 handles the I/O completion. Compared with Q2, the CPU is only idle for 1 tick because PID 1 uses most of the I/O waiting time. / PID 0 在 tick 1 发起 I/O，然后因为 SWITCH_ON_IO 切换到 PID 1。PID 1 在 ticks 2–5 执行完 4 条 CPU 指令。tick 6 时因为 PID 0 的 I/O 还没完成，所以 CPU 空闲。tick 7 PID 0 处理 I/O 完成。相比 Q2，这里 CPU 只空闲了 1 个 tick，因为 PID 1 利用了大部分 I/O 等待时间。
- Verified result / 验证结果:
- Analysis / 分析:

## Q4
- Prediction / 预测: Total Time 11, CPU Busy 6 (54.55%), IO Busy 5 (45.45%) / 总时间11，CPU利用率54.55%，I/O利用率45.45%
- Reasoning / 理由: With SWITCH_ON_END, the CPU does not switch to PID 1 until PID 0 is completely finished. Therefore, the CPU stays idle while PID 0 is BLOCKED for 5 ticks (ticks 2–6). After PID 0 finishes at tick 7, PID 1 runs its 4 instructions from ticks 8–11. / 在 SWITCH_ON_END 下，CPU 要等 PID 0 完全结束后才会切换到 PID 1。所以 PID 0 在 ticks 2–6 处于 BLOCKED 时，CPU 都是空闲的。PID 0 在 tick 7 完成后，PID 1 才在 ticks 8–11 执行 4 条指令。
- Verified result / 验证结果:
- Analysis / 分析:

## Q5
- Prediction / 预测: Total Time 7, CPU Busy 6 (85.71%), IO Busy 5 (71.43%) / 总时间7，CPU利用率85.71%，I/O利用率71.43%
- Reasoning / 理由: This is the same setup as Q3 (1:0, 4:100), with SWITCH_ON_IO explicitly enabled. Since this is also the default setting, the result is the same as Q3. PID 0 starts I/O and switches to PID 1, which uses most of the waiting time. As a result, only 1 tick is idle. / 这和 Q3 的设置相同（1:0, 4:100），只是明确设置了 SWITCH_ON_IO。因为它本身就是默认设置，所以结果和 Q3 一样。PID 0 发起 I/O 后切换到 PID 1，PID 1 利用了大部分等待时间，最后只有 1 个 tick 是空闲的。
- Verified result / 验证结果:
- Analysis / 分析:

## Q6
- Prediction / 预测:Since -I IO_RUN_LATER means a process that just finished an I/O goes to the back of the ready queue instead of running immediately, PID 0 will likely get delayed behind the CPU-bound processes after each I/O completes, finishing last despite having the fewest instructions. / 由于-I IO_RUN_LATER意味着刚完成I/O的进程会排到就绪队列末尾而不是立刻运行，PID0在每次I/O完成后很可能会被排在纯CPU进程后面，尽管它指令数最少，却会最后结束。
- Reasoning / 理由: PID 0 starts I/O and switches to another process because of SWITCH_ON_IO. While PID 0 is BLOCKED, the other processes use the CPU. With IO_RUN_LATER, PID 0 does not move to the front when its I/O finishes, so it waits for the other CPU-bound processes first. This happens for all 3 I/O operations, making PID 0 finish later each time. / PID 0 发起 I/O 后，因为 SWITCH_ON_IO 切换到其他进程。在 PID 0 BLOCKED 期间，其他进程使用 CPU。由于 IO_RUN_LATER，PID 0 的 I/O 完成后不会直接插队，而是要先等待其他 CPU 进程。这个过程会在 3 次 I/O 中重复，所以 PID 0 的完成时间会越来越晚。
- Verified result / 验证结果:PID 0 starts I/O and switches to another process because of SWITCH_ON_IO. While it is BLOCKED, other processes use the CPU. With IO_RUN_LATER, PID 0 waits in line after its I/O finishes instead of running immediately. This happens for all 3 I/O operations, so PID 0 finishes later each time. / PID 0 发起 I/O 后，因为 SWITCH_ON_IO 切换到其他进程。它 BLOCKED 时，其他进程继续使用 CPU。由于 IO_RUN_LATER，PID 0 的 I/O 完成后不会马上运行，而是要排队等待。3 次 I/O 都会重复这个过程，所以 PID 0 会越来越晚完成。
- Analysis / 分析:

## Q7
- Prediction / 预测: With IO_RUN_IMMEDIATE, PID 0 can issue its next I/O right after the previous one finishes, without waiting behind the other CPU-bound processes. This should keep the I/O device constantly busy and shrink the total time compared to Q6. / 使用IO_RUN_IMMEDIATE，PID0在上一次I/O完成后可以立刻发起下一次I/O，不用排在其他CPU进程后面等待。这应该能让I/O设备持续忙碌，相比Q6缩短总时间。
- Reasoning / 理由: Unlike IO_RUN_LATER in Q6, IO_RUN_IMMEDIATE lets PID 0 run again as soon as its I/O finishes. So PID 0 can start its next I/O right away instead of waiting in the queue. This keeps the 3 I/O operations closer together and keeps the I/O device busy. / 和 Q6 的 IO_RUN_LATER 不同，IO_RUN_IMMEDIATE 会让 PID 0 的 I/O 完成后马上继续运行。因此 PID 0 可以直接发起下一次 I/O，不需要重新排队。这样 3 次 I/O 会更加集中，也能让 I/O 设备更快保持忙碌。
- Verified result / 验证结果:
- Analysis / 分析:

## Q8
- Prediction / 预测:Because the instructions are randomly generated from the seed, it is difficult to know the exact result in advance. However, SWITCH_ON_END should usually be slower than the default/IO_RUN_IMMEDIATE because it cannot use other processes while waiting for I/O. / 由于指令是根据 seed 随机生成的，所以很难提前确定准确结果。不过，SWITCH_ON_END 通常会比 default/IO_RUN_IMMEDIATE 慢，因为它在等待 I/O 时不能让其他进程运行。
- Reasoning / 理由: With seed 1, PID 0 has CPU, I/O, I/O, while PID 1 has 3 CPU instructions. Under the default/IO_RUN_IMMEDIATE, PID 1 runs while PID 0 waits for I/O and finishes at tick 6. PID 0's I/O finishes at tick 8, so there is no other process waiting for the CPU. Therefore, IO_RUN_LATER and IO_RUN_IMMEDIATE give the same result here. With SWITCH_ON_END, PID 1 cannot run until PID 0 is completely done, so the CPU cannot overlap the I/O wait with other work. / 在 seed 1 下，PID 0 的指令是 CPU、I/O、I/O，而 PID 1 有 3 条 CPU 指令。在 default/IO_RUN_IMMEDIATE 下，PID 0 等待 I/O 时 PID 1 可以运行，并在 tick 6 完成。PID 0 的 I/O 到 tick 8 才完成，所以这时已经没有其他进程使用 CPU。因此，IO_RUN_LATER 和 IO_RUN_IMMEDIATE 在这里结果相同。而在 SWITCH_ON_END 下，PID 1 必须等 PID 0 完全结束后才能运行，所以无法利用 I/O 等待时间。
- Verified result / 验证结果:
- Analysis / 分析: