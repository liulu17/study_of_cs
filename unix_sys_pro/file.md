# 通用IO模型
## IO打开参数
- O_TRUNC 对于读写都是有用的，将文件截断
- O_CREAT 对于读写都是有用的，创建新文件
- O_APPEND 只很对写，再末尾追加文件
1. 读文件，若不存在则创建
    O_CREAT|ORDONLY
2. 写文件，不存在创建
    O_WDONLY | O_CREAT
3. 写文件，不存在创建，存在，从末尾追加
    O_WDONOY | O_CREAT | O_APPEND