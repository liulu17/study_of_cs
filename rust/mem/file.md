# RUST标准库内存模块
模块代码位置为 
- core/src/alloc*.*
- core/src/ptr*.*
- core/src/mem*.*
- core/src/intrinsic.rs
- alloc/src/alloc.rs

因为core库可以构建完全能在裸机运行的程序，所以rust需要对内存完全的控制。这需要讲内存管理系统与rust的语言
类型相关联，即一段内存需要对应rust的一个基础类型

## intrinsic 模块
### 内存申请
intrinsics::forget<T:Sized?> (_:T) 代码结束时不会调用drop函数
```RUST{.line-numbers}
#![feature(core_intrinsics)]

struct test;

impl Drop for test {
    fn drop(&mut self) {
        println!("drop test...");
    }
}
fn main() {
    let t = test;
    core::intrinsics::forget(t);
    
}

```

intrinsics::drop_in_place<T:Sized?>(to_drop: * mut T) 在某个位置手动唤起drop函数
```rust{.line-numbers}
#![feature(core_intrinsics)]

struct test;

impl Drop for test {
    fn drop(&mut self) {
        println!("drop test...");
    }
}
fn main() {
    let mut t = test;
    let t_ptr = &mut t as *mut test;
    core::intrinsics::forget(t);
    unsafe {
        core::intrinsics::drop_in_place(t_ptr);
    }
    
}

```

### 内存块内容修改
intrinsics::write_bytes(dst: *mut T, val:u8, count:usize) C语言的memset的RUST实现
```rust
fn main(){
    let mut a = [0u8;10];
    let a_ptr = &mut a as *mut u8;
    unsafe{
        core::intrinsics::write_bytes(a_ptr, 10, 10);
    }
    println!("{:?}",a);

}
```

intrinsics::copy<T>(src:*const T, dst: *mut T, count:usize) 
```rust
fn main(){
    let  a = [100u8;10];
    let mut b:[u8;10] = [0u8;10];

    let a_ptr = &a as *const u8;
    let b_ptr = &mut b as *mut u8;
    unsafe{
        core::intrinsics::copy(a_ptr, b_ptr, 10);
    }
    println!("{:?}",b);

}
```
### MannulDrop<T>
这是T的一个包装器，主要原理是通过MannulDrop::new(value:T)获取T的所有权。但是MannualDrop本身没有实现Drop trait。这样T就不会被自动
drop。这是一个小技巧。
1. MannulDrop::new(value:T) -> MannulDrop<T> 获取value的所有权，且不会被自动释放
    ```
        let x = String::from("ll");
        let y = MannualDrop::new(x); // x的Drop实现drop不会被调用

    ```
2. MannulDrop::into_inner(slot: ManuallyDrop<T>) -> T
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
