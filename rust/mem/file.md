### MannulDrop<T>
这是T的一个包装器，主要原理是通过MannulDrop::new(value:T)获取T的所有权。但是MannualDrop本身没有实现Drop trait。这样T就不会被自动
drop。这是一个小技巧。
1. MannulDrop::into_inner(slot: ManuallyDrop<T>) -> T
    这个函数又将所有权释放出来
    ```
    let t1 = MannulDrop::new(Box::new(()));
    let t2:Box<()> = MannulDrop::into_inner(t1); // t2 获得所有权,后面又会被drop
    ```


### fn drop<T>(_x:T);
这个函数拿走x的所有权，是的rustc自动在drop代码内部结束处插入x.drop()代码。从而释放x。如果x实现了Copy trait则不会释放x
```
let x = 10;
drop(x);
println!("{}",x);
```
上面可以正常执行。
```
let x = String::from("test");
drop(x);
println!("{}",x);
```
上面不可以正常执行。rustc只要注入了drop代码，就对后面的借用报错。
