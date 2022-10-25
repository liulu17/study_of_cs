## 字符设备驱动和块设备驱动
1. 字符设备的用户空间操作直接通过系统调用直达设备驱动
2. 块设备的操作通过文件管理子系统和块设备子系统中转，再到达块设备驱动程序。这些子系统为驱动程序准备资源（缓存），将最近的读取数据缓存在cache 缓存中，且为了考虑性能，将读写操作排序

## 主从设备号
linux中的每个设备有主从设备号。主设备号标识设备类型（IDE磁盘，SCSI磁盘，串口等），从设备号标识具体的某个类型的其中的某个设备。多数时候，主设备号标识驱动程序，从设备标记驱动程序服务的设备号。也就是说同一个主设备号是同一个驱动程序，不同的从设备号标识这些设备都由同一个驱动服务
```
ls -al /dev/ttyS* 可以查看主从设备号
cat /proc/devices 可以查看已加载的设备和主设备号
mknod /dev/mycdev c 99 0 创建一个字符设备，主从设备分别为99，0
mknod /dev/mybdev b 240 0 创建一个块设备，主从设备分别为99，0
```
## 字符设备数据结构
字符设备通过struct cdev 定义。通过这个结构在系统中注册字符设备,大多数驱动操作以下三个重要的结构
   
### struct file_operations   
   
```
#include <linux/fs.h>

struct file_operations {
    struct module *owner;
    loff_t (*llseek) (struct file *, loff_t, int);
    ssize_t (*read) (struct file *, char __user *, size_t, loff_t *);
    ssize_t (*write) (struct file *, const char __user *, size_t, loff_t *);
    [...]
    long (*unlocked_ioctl) (struct file *, unsigned int, unsigned long);
    [...]
    int (*open) (struct inode *, struct file *);
    int (*flush) (struct file *, fl_owner_t id);
    int (*release) (struct inode *, struct file *);
    [...]
```

上文提及，大多数对字符设备的操作通过系统调用（open，read，write，llseek等）来操作字符设备文件，且这些系统调用直达字符设备驱动。struct file_operations直接描述了这些操作。***这里的函数签名和用户空间的系统调用是不一样的***，要注意！这些操作大多用结构file和inode为操作，这两个结构都代表一个文件，只是从不同的角度。大多数参数都能从字面直接理解。
- file和inode表示设备文件
- size_t标识读取和写入的大小
- offset是读取和写入的位置
- user_buffer 用户空间的读取和写入的缓存区
- whence 是一种seek方式（操作的起点）
- cmd和arg是用户传递给ioctl调用(IO控制)的参数

### inode和file
inode代表文件系统角度的文件。inode属性有文件大小，权限，时间相关等。inode在文件系统中唯一标识一个文件

file是用户角度的文件数据结构。相关属性有：inode、文件名、文件打开属性、文件当前位置。在给定时刻，所有打开的文件都有个file结构。

类比面向对象的思想，inode代表一个类，file代表一个对象。inode没有状态，file有状态

struct file包含以下属性

- f_mode 标识读或写
- f_flags 读写标志（O_RDONLY、O_WDONLY、O_SYNC、O_NONBLOCK、O_TRUNC等）
- f_op 表示这个文件结构的所有操作，这是一个file_operations结构的指针
- private_data 用户程序存储的设备特定程序数据指针。数据可以被用户程序初始化到内存中
- f_pos 文件偏移

struct inode包含很多字段，其中有个i_cdev字段，这是一个指向这个设备结构 struct cdev 的指针。

## 操作实现

## 字符设备注册和取消注册
注册字符设备需要分配主从设备号。dev_t结构包含主从设备号，可以使用宏提取主从设备号
```
MAJOR(DEV) 提取主设备号
MINOR(DEV) 提取次设备号
MKDEV(MA,MI) 根据主从设备号构造dev_t结构
```

静态分配和注销设备号
```
#include <linux/fs.h>
int register_chrdev_region(dev_t first, unsigned int count, char *name);
void unregister_chrdev_region(dev_t first, unsigned int count);
```

推荐动态分配
```
alloc_chrdev_region
```

下面的示例表示从主设备号my_major和从设备号my_first_minor开始分配设备号。如果my_first_minor溢出，则从my_major+1开始分配主设备号。设备数量为my_minor_count。设备名为my_device_driver

```
#include <linux/fs.h>
...

err = register_chrdev_region(MKDEV(my_major, my_first_minor), my_minor_count,
                             "my_device_driver");
if (err != 0) {
    /* report error */
    return err;
}
...
```

一旦分配了设备号，字符设备将被初始化（cdev_init）,然后内核被通知（cdev_add)添加了一个字符设备。 当字符设备准备接受调用了，必须先调用cdev_add添加进内核。cdev_del移除字符设备
```
#include <linux/cdev.h>

void cdev_init(struct cdev *cdev, struct file_operations *fops);
int cdev_add(struct cdev *dev, dev_t num, unsigned int count);
void cdev_del(struct cdev *dev);
```
下列代码注册并初始化多个MY_MAX_MINORS设备
```
#include <linux/fs.h>
#include <linux/cdev.h>

#define MY_MAJOR       42
#define MY_MAX_MINORS  5

struct my_device_data {
    struct cdev cdev;
    /* my data starts here */
    //...
};

struct my_device_data devs[MY_MAX_MINORS];

const struct file_operations my_fops = {
    .owner = THIS_MODULE,
    .open = my_open,
    .read = my_read,
    .write = my_write,
    .release = my_release,
    .unlocked_ioctl = my_ioctl
};

int init_module(void)
{
    int i, err;

    err = register_chrdev_region(MKDEV(MY_MAJOR, 0), MY_MAX_MINORS,
                                 "my_device_driver");
    if (err != 0) {
        /* report error */
        return err;
    }

    for(i = 0; i < MY_MAX_MINORS; i++) {
        /* initialize devs[i] fields */
        cdev_init(&devs[i].cdev, &my_fops);
        cdev_add(&devs[i].cdev, MKDEV(MY_MAJOR, i), 1);
    }

    return 0;
}
```
而下面的代码删除并取消注册这些字符设备
```
void cleanup_module(void)
{
    int i;

    for(i = 0; i < MY_MAX_MINORS; i++) {
        /* release devs[i] fields */
        cdev_del(&devs[i].cdev);
    }
    unregister_chrdev_region(MKDEV(MY_MAJOR, 0), MY_MAX_MINORS);
}
```

struct my_fops 的初始化使用C99标准中定义的按名称初始化成员(参见指定的初始化器和 file_operation 结构)。未显式出现在此初始化中的结构成员将设置为其类型的默认值。例如，在上面的初始化之后，my_fops.mmap 将为 NULL。


## 访问用户地址空间

驱动程序工作在特权模式的内核中。驱动程序是连接用户程序和底层硬件的桥梁。因此驱动程序需要访问用户空间数据。然后直接访问（使用用户空间指针）用户空间数据会导致不正确的行为（取决于架构，可能是非法指针，或者映射到内核空间），一个内核操作（用户模式指针可以引用非驻留内存区域），或者安全问题。应该通过以下方式访问用户空间数据
```
#include <asm/uaccess.h>

put_user(type val, type *address);
get_user(type val, type *address);
unsigned long copy_to_user(void __user *to, const void *from, unsigned long n);
unsigned long copy_from_user(void *to, const void __user *from, unsigned long n);
```
所有宏/函数在成功时返回0，在出错时返回另一个值，并具有以下功能:
- put_user 将值 val 存储为用户空间地址; 类型可以是8、16、32、64位上的一个(最大支持类型取决于硬件平台) ;
- get_user 类似于前面的函数，只有 val 将被设置为与地址给定的用户空间地址的值相同的值;
- copy_to_user 从内核空间复制 n 个字节，从用户空间引用的地址复制到 to 引用的地址;
- copy_from_user 将 n 个字节从用户空间从从内核空间引用的地址复制到。
  
通常以如下方式工作
```
#include <asm/uaccess.h>

/*
 * Copy at most size bytes to user space.
 * Return ''0'' on success and some other value on error.
 */
if (copy_to_user(user_buffer, kernel_buffer, size))
    return -EFAULT;
else
    return 0;
```

## open和release

open函数执行设备的初始化。在大多数情况下，这些操作指的是初始化设备并填充特定的数据(如果是第一个打开的调用)。release函数释放设备特定的资源: 解锁特定的数据，如果最后一次调用关闭，则关闭设备
大多数情况下，openg函数有如下结构
```
static int my_open(struct inode *inode, struct file *file)
{
    struct my_device_data *my_data =
             container_of(inode->i_cdev, struct my_device_data, cdev);

    /* validate access to device */
    file->private_data = my_data;

    /* initialize device */
    ...

    return 0;
}
```
实现open函数的可能出现问题是访问控制。有时候某个设备只需要打开一次。更具体的说，在释放前，不允许第二次打开。为了实现这样的限制，对于已经打开的设备，可以选择如下方式控制二次打开
- open直接返回error（-EBUZY）
- 直到释放资源前，一直阻塞open调用
- 先关闭设备，再打开设备。

用户空间调用open和close会调用驱动对应的操作函数my_open 和my_realease
```
int fd = open("/dev/my_device", O_RDONLY);
if (fd < 0) {
    /* handle error */
}

/* do work */
//..

close(fd);
```
