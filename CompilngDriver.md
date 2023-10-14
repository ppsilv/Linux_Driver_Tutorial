# To compile linux drivers

1 - You must have a correct environment with the linux headers for your kernel or for the kernel you are compiling the driver maybe you are crosscompiling the driver
	thing that I don't know it is possible.
	* For orangepi 5 plus the kernel and ubuntu 22.04 jammy header are in /opt
	  when I am writing this the package is: /opt/linux-headers-legacy-rockchip-rk3588_1.0.6_arm64.deb for the kernel 5.10.110-rockchip-rk3588, use uname -r to get your 
	  kernel version.
	* Some distributions you can use apt-get install linux-headers$('shell uname -r')
	* Unforunately you will must discover how to get your linux kernel headers.

2 - The header home is: /usr/src/linux-headers-5.10.110-rockchip-rk3588
	* This is the path header must reside.
	
3 - The modules home is: /lib/modules/5.10.110-rockchip-rk3588/kernel		

4 - You can compile you driver in your projects directory since you have a Makefile
	like this:
	Makefile
	+---------------------------------------------------------------------------+
	|	obj-m += hello.o														|	
	|																			|
	|	all:																	|
	|		make -C /lib/modules/$(shell uname -r)/build M=$(PWD) modules		|
	|																			|
	|	clean:																	|
	|		make -C /lib/modules/$(shell uname -r)/build M=$(PWD) clean			|
	+---------------------------------------------------------------------------+
	
	hello.c
	+---------------------------------------------------------------------------+
	|#include <linux/module.h>													|
	|#include <linux/init.h>													|
	|																			|
	|/*Meta information*/														|
	|MODULE_LICENSE("GPL");														|
	|MODULE_AUTHOR("Paulo da Silva");											|
	|MODULE_DESCRIPTION("My first driver in orange pi");						|
	|MODULE_INFO(intree, "Y");													|
	|																			|
	|static int __init ModuleInit(void)											|
	|{																			|
	|	printk("hello: Input - This is my first Kernel driver PAULOSILVA");		|
	|	return(0);																|
	|}																			|
	|																			|
	|static void __exit ModuleExit(void)										|
	|{																			|
	|	printk("hello: Output - Module PAULOSILVA exiting...");					|
	|}																			|
	|																			|
	|module_init(ModuleInit);													|
	|module_exit(ModuleExit);													|
	|																			|
	+---------------------------------------------------------------------------+
		
5 - To compile: make all
	* - When compiled copy you driver in this case hello.ko to: 
		cd /lib/modules/5.10.110-rockchip-rk3588/kernel/drivers
		mkdir hello
		cd hello
		cp <your project dir/hello.ko> .
		
6 - After your driver is compiled you must run the command depmod as root in 
	directory: /lib/modules/5.10.110-rockchip-rk3588
	
The Linux kernel contains data structures whose layout varies not only from version to version but also depending on the compilation options. As a consequence, when you compile a kernel module, you need to have not only the header files from the kernel source, but also some header files that are generated during the kernel compilation. Merely unpacking the kernel source isn't enough.

With kernels built with the CONFIG_MODVERSIONS, the version number can differ, but the layout of the data structures must be the same. This option is activated in the Ubuntu kernels. With this option, in addition to the headers, modules need to be compiled against the proper Module.symvers file. Ubuntu, like most distributions, includes this file in the same package as the kernel headers resulting from the compilation. The Ubuntu kernel header package is called linux-headers-VERSION-VARIANT, e.g. linux-headers-3.8.0-38-generic.

With kernels built without the CONFIG_MODVERSIONS (which may be the case if you compiled your own kernel), the check when loading a module is a simple version check. You can take the unpacked kernel source, copy the .config that was used during the compilation of your running kernel, and run make modules_prepare. The onus is on you to verify that any patch you've made to the kernel doesn't affect binary compatibility.

	
