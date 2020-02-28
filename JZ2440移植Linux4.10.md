# JZ2440移植linux4.0
## 1. 内核准备
1. 下载内核
   ```bash
    https://www.kernel.org/
   ```

2. 修改内核Makefile
   1. 目的是给ARCH, CROSS_COMPILE两个 变量赋值，这样在编译时就不用传参
   ```bash
    ARCH ?=   arm
    CROSS_COMPILE ?= arm-linux-
   ```

3. 配置内核
   1. 目的是把单板信息配置到内核中
   ```bash
    make s3c2410_defconfig          #该文件路径是：linux-4.10.8/arch/arm/configs
   ```

4. 修改时钟
   1. 把内核默认时钟和单板时钟匹配上
   ```c
    //文件路径：linux-4.10.8/arch/arm/mach-s3c24xx/mach-smdk2440.c
    static void __init smdk2440_init_time(void)
    {
        s3c2440_init_clocks(12000000);                  //修改成该函数
        samsung_timer_init();
    }
    ```

5. 修改开发板ID
   1. 在路径：linux-4.10.8/arch/arm/tools/mach-types中，把s3c2440更改为362，若已经有ID为362的，需要将其更改

6. 修改分区
   1. 官方linux内核中并不分区设置未必会和单板匹配，需要修改成自己的系统
   2. 在路径`linux-4.10.8/arch/arm/mach-s3c24xx/common-smdk.c`修改结构体`struct mtd_partition smdk_default_nand_part[]`(Line 112)
   ```c
    static struct mtd_partition smdk_default_nand_part[] = {
    [0] = {
        .name   = "bootloader",
        .size   = SZ_256K,
        .offset = 0,
    },
    [1] = {
        .name   = "params",
        .offset = MTDPART_OFS_APPEND,
        .size   = SZ_128K,
    },
    [2] = {
        .name   = "kernel",
        .offset = MTDPART_OFS_APPEND,
        .size   = SZ_4M,
    },
    [3] = {
        .name   = "rootfs",
        .offset = MTDPART_OFS_APPEND,
        .size   = MTDPART_SIZ_FULL,
        },
    };  
   ```

7. 支持网卡dm9000
   1. 增加dm9000对应的结构体，里面存储dm9000的设备信息；
   2. 再把dm9000结构体挂载到对应的信息结构体仲

8. 编译器和Busybox
   1. 编译器版本：arm-linux-gcc 4.4.3
   2. busybox 1.22.1