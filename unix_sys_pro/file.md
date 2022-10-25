# 通用IO模型
## IO打开参数
- O_TRUNC 对于读写都是有用的，将文件截断
- O_CREAT 对于读写都是有用的，创建新文件
- O_EXCL 如果O_CREAT标志存在且文件存在，则报错，此标志强制一定不能存在打开的文件，即强制新建文件
- O_APPEND 只很对写，再末尾追加文件
1. 读文件，若不存在则创建
    O_CREAT|ORDONLY
2. 写文件，不存在创建
    O_WDONLY | O_CREAT
3. 写文件，不存在创建，存在，从末尾追加
    O_WDONOY | O_CREAT | O_APPEND
- O_DIRECT 无缓冲的IO
- O_NONBLOCK 无阻塞的IO
- O_SYNC 同步写入文件
- O_ASYNC 文件IO操作可行时，产生信号通知进程