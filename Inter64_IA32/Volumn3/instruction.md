# test 
#### test ax,bx 
将ax，bx 做and运算，并影响相应flag位。不影响ax和bx的值
```
test al,00000101b 测试第0、2位是否为1
jnz .... 如果第0、2位是都为1，则跳转
```

#### BTS ax,0 
位测试和设置
将ax的第0位送达CF标志位，并将ax的第0位置1.
```
;自旋锁的实现
spin_lock:
    lock bts ax,0
    jc spin_lock
;ax的第0位为1(锁被别人占有)则一直测试 
```

