## 原始类型指针 *const, T *mut T
### 创建裸指针
1. as 可以将引用转化为指针
- ***as需要先创建一个引用***
```rust
let x = 10;
let y = &x as * const i32;
let y:*const i32 = &x; // 可以直接使用*const T接受 &T类型的值

let z = Box::new(10);
let t:*const i32 = &*z; // 要获得指向 boxed 值的指针，请解引用 box

let t:*const i32 = Box::into_raw(z); // 这里获得了Box的所有权，但是*const i32不会自动释放，需要后面手动释放内存

unsafe {
    drop(Box::from_raw(z));
}
```

2. 使用ptr::addr_of!宏创建原始指针
- ***ptr::addr_of!不用创建引用***
```rust
#[derive(Debug, Default, Copy, Clone)]
#[repr(C,packed)]
struct S {
    a:u8,
    b:u32,
}

let x = S::default();
// let y = &(x.b)   //报错，因为x.b未对齐，不能创建引用
let y = ptr::addr_of!(x.b); // 正确
println!("{}",unsafe{*y});
```

3. 使用libc的c函数创建
```rust
extern crate libc;
use std::mem;

unsafe {
    let x:*mut i32 = libc::malloc(mem::size_of::<i32>()) as *mut i32;
    if x.is_null() {
        panic!("failed to allocate memory");
    }
    libc::free(x as *mut libc::c_void);
}

```

4. 直接用usize转换
```rust
let x:usize= 0xf00000000;
let y = x as *const i32;

```
5. ptr::null() 创建裸指针
   frow_raw_parts() 使用*const () 和一个元数据创建裸指针 *const T
   invalid(addr:usize) addr转化为*const T。这里有个技巧。invalid<T>,如果T没有显示指定，则为().所以invalid(0) 差不多等价于 0 as *const ().
   frow_raw_parts(invalid(0),()) --> *const T


