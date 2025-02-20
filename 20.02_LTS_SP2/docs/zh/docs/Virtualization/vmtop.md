# 工具使用指南

- [vmtop使用指南](#vmtop使用指南)

## vmtop使用指南

### 概述
vmtop 是运行在宿主机host上的用户态工具。使用vmtop可以实时动态地查看虚拟机资源的使用情况，例如CPU占用率、内存占用率、vCPU陷入陷出次数等。因此，可以使用vmtop作为虚拟化问题定位和性能调优的工具。

#### 多架构支持
当前vmtop支持AArch64和x86_64处理器架构。

#### 显示项说明
不同处理器架构的操作系统，vmtop的显示项存在差异，这里给出各个显示项的含义及其是否在对应架构呈现。
说明：以下采样差是指指定时间间隔内获取的两次数据的差值。

##### **AArch64和x86_64架构共有显示项**
- VM/task-name: 虚拟机/进程名称
- DID: 虚拟机id
- PID: 虚拟机qemu进程的pid
- %CPU: 进程的CPU占用率
- EXTsum: kvm exit总次数（采样差）
- S: 进程状态
- P: 进程所占用的物理CPU号
- %ST: 被抢占时间与cpu运行时间的比
- %GUE: 虚拟机内部占用时间与CPU运行时间的比
- %HYP: 虚拟化开销占比

##### 仅AArch64架构的显示项
- EXThvc: hvc-exit次数（采样差）
- EXTwfe: wfe-exit次数（采样差）
- EXTwfi: wfi-exit次数（采样差）
- EXTmmioU: mmioU-exit次数（采样差）
- EXTmmioK: mmioK-exit次数（采样差）
- EXTfp: fp-exit次数（采样差）
- EXTirq: irq-exit次数（采样差）
- EXTsys64: sys64 exit次数（采样差）
- EXTmabt: mem abort exit次数（采样差）


##### 仅x86_64架构的显示项
- PFfix: 缺页次数（采样差）
- PFgu: 向guest OS注入缺页次数（采样差）
- INvlpg: 冲刷tlb某项次数(tlb其中一项，并不固定)
- EXTio: io VM-exit次数（采样差）
- EXTmmio: mmio VM-exit次数（采样差）
- EXThalt: halt VM-exit次数（采样差）
- EXTsig: 信号处理引起的VM-exit次数（采样差）
- EXTirq: 中断引起的VM-exit次数（采样差）
- EXTnmiW: 处理不可屏蔽中断引起的VM-exit次数（采样差）
- EXTirqW: interruptwindow机制，开启中断使能时exit，以便注入中断（采样差）
- IrqIn: 注入irq中断次数（采样差）
- NmiIn: 注入nmi中断次数（采样差）
- TLBfl: 冲刷整个tlb次数（采样差）
- HostReL: 重载主机状态次数（采样差）
- Hyperv: 模拟Guest操作系统辅助虚拟化调用hypercall的处理次数（采样差）
- EXTcr: 访问CR寄存器退出次数（采样差）
- EXTrmsr: 读msr退出次数（采样差）
- EXTwmsr: 写msr退出次数（采样差）
- EXTapic: 写apic次数（采样差）
- EXTeptv: Ept缺页退出次数（采样差）
- EXTeptm: Ept错误退出次数（采样差）
- EXTpau: Vcpu暂停退出次数（采样差）

### 使用方法
vmtop是一款命令行工具，直接以命令行的方式运行 vmtop 即可。
另外，vmtop还提供了不同可选选项，用于查询不同信息。

#### 语法格式
```sh
vmtop [选项]
```

#### 选项说明
- -d: 设置显示刷新的时间间隔，单位：秒
- -H: 显示虚拟机的线程信息
- -n: 设置显示刷新的次数，刷新完成后退出
- -b: Batch模式显示，可以用于重定向到文件
- -h: 显示帮助信息
- -v: 显示版本
- -p: 监控指定id的虚拟机

#### 快捷键
在vmtop运行状态下使用的快捷键
- H: 显示或关闭虚拟机线程信息，默认显示该信息
- up/down: 向上/向下移动显示的虚拟机列表
- left/right: 向左/向右移动显示的信息，从而显示因屏幕宽度被隐藏的列
- f: 进入监控项编辑模式，选择要开启的监控项
- q: 退出vmtop进程

### 示例
在host上直接以命令行的方式运行vmtop
```sh
vmtop
```
输出如下:
```sh
vmtop - 2020-09-14 09:54:48 - 1.0
Domains: 1 running

  DID  VM/task-name     PID  %CPU    EXThvc    EXTwfe    EXTwfi  EXTmmioU  EXTmmioK     EXTfp    EXTirq  EXTsys64   EXTmabt    EXTsum    S    P   %ST  %GUE  %HYP
    2       example 4054916  13.0         0         0      1206        10         0       144        62       174         0      1452    S  106   0.0  99.7  16.0
```
可以看到，host上只有一台名称为“example”的虚拟机，ID为2，CPU占用率是13.0%，在1秒内的陷入陷出总次数是1452，虚拟机进程占用的物理CPU为106号CPU，虚拟机内部占用时间与CPU运行时间的比是99.7%。

1.显示虚拟机线程信息
按下‘H’后可以显示线程信息：
```sh
vmtop - 2020-09-14 10:11:27 - 1.0
Domains: 1 running

  DID  VM/task-name     PID  %CPU    EXThvc    EXTwfe    EXTwfi  EXTmmioU  EXTmmioK     EXTfp    EXTirq  EXTsys64   EXTmabt    EXTsum    S    P   %ST  %GUE  %HYP
    2       example 4054916  13.0         0         0	   1191        17         4       120        76       147         0      1435    S  119   0.0 123.7   4.0
   |_      qemu-kvm 4054916   0.0         0         0         0         0         0         0         0         0         0         0    S  119   0.0   0.0   0.0
   |_      qemu-kvm 4054928   0.0         0         0         0         0         0         0         0         0         0         0    S  119   0.0   0.0   0.0
   |_  signalfd_com 4054929   0.0         0         0         0         0         0         0         0         0         0         0    S  120   0.0   0.0   0.0
   |_  IO mon_iothr 4054932   0.0         0         0         0         0         0         0         0         0         0         0    S  117   0.0   0.0   0.0
   |_     CPU 0/KVM 4054933   3.0         0         0       280         6         4        28        19        41         0       350    S  105   0.0  27.9   0.0
   |_     CPU 1/KVM 4054934   3.0         0         0       260         0         0        16        12        36         0       308    S   31   0.0  20.0   0.0
   |_     CPU 2/KVM 4054935   3.0         0         0       341         0         0        44        20        26         0       387    R  108   0.0  27.9   4.0
   |_     CPU 3/KVM 4054936   5.0         0         0       310        11         0        32        25        44         0       390    S  103   0.0  47.9   0.0
   |_   memory_lock 4054940   0.0         0         0         0         0         0         0         0         0         0         0    S  126   0.0   0.0   0.0
   |_    vnc_worker 4054944   0.0         0         0         0         0         0         0         0         0         0         0    S  118   0.0   0.0   0.0
   |_        worker 4143738   0.0         0         0         0         0         0         0         0         0         0         0    S  120   0.0   0.0   0.0
```
example虚拟机有11个线程，其中包括vCPU线程、vnc_worker、IO mon_iotreads等等，每个线程同样会显示详细CPU占用、陷入陷出等信息。

2.选择监控项
按下‘f’进入监控项编辑模式：
```sh
field filter - select which field to be showed
Use up/down to navigate, use space to set whether chosen filed to be showed
'q' to quit to normal display

 * DID
 * VM/task-name
 * PID
 * %CPU
 * EXThvc
 * EXTwfe
 * EXTwfi
 * EXTmmioU
 * EXTmmioK
 * EXTfp
 * EXTirq
 * EXTsys64
 * EXTmabt
 * EXTsum
 * S
 * P
 * %ST
 * %GUE
 * %HYP
```
当前所有监控项都默认显示，通过up/down键选择，用space键来设置对应显示项是否显示/隐藏，按‘q’键退出。
将%ST、%GUE、%HYP设置为隐藏后，输出如下:
```sh
vmtop - 2020-09-14 10:23:25 - 1.0
Domains: 1 running

  DID  VM/task-name     PID  %CPU    EXThvc    EXTwfe    EXTwfi  EXTmmioU  EXTmmioK     EXTfp    EXTirq  EXTsys64   EXTmabt    EXTsum    S    P
    2       example 4054916  12.0         0         0	   1213        14         1       144        68       168         0      1464    S  125
   |_	   qemu-kvm 4054916   0.0         0         0         0         0         0         0         0         0         0         0    S  125
   |_	   qemu-kvm 4054928   0.0         0         0         0         0         0         0         0         0         0         0    S  119
   |_  signalfd_com 4054929   0.0         0         0         0         0         0         0         0         0         0         0    S  120
   |_  IO mon_iothr 4054932   0.0         0         0         0         0         0         0         0         0         0         0    S  117
   |_     CPU 0/KVM 4054933   2.0         0         0       303         6         0        29        10        35         0       354    S   98
   |_     CPU 1/KVM 4054934   4.0         0         0       279         0         0        39        17        49         0       345    S    1
   |_     CPU 2/KVM 4054935   3.0         0         0       283         0         0        33        20        40         0       343    S  122
   |_     CPU 3/KVM 4054936   3.0         0         0       348         8         1        43        21        44         0       422    S  110
   |_   memory_lock 4054940   0.0         0         0         0         0         0         0         0         0         0         0    S  126
   |_    vnc_worker 4054944   0.0         0         0         0         0         0         0         0         0         0         0    S  118
   |_        worker    1794   0.0         0         0         0         0         0         0         0         0         0         0    S  126
```
%ST、%GUE、%HYP将不会出现在显示界面上。
