# 2. 描述物理布局
每个物理布局使用 pg_data_t 描述
```
typedef struct pglist_data {
    ....
} pg_data_t
```
NUMA有多个布局，而UMA只有一个内存布局。每个pd_data_t通过链表连起来。
```
pg_data_t *pg_dat_list[MAX_NUMNODES]
```

在NUM上只有一个静态的contig_page_data
```
extern struct pglist_data contig_page_data;
```

一个node由多个zone组成，zone由struct zone表示,有zone_t引用

zonelist_t 为NULL结尾的zone指针链表

```
typedef struct zone (
    //...
) zone_t

typedef struct zonelist_struct {
	zone_t * zones [MAX_NR_ZONES+1]; // NULL delimited
} zonelist_t;
```


## 3.页表管理
### 多级页表项
pte、pmd、pgd
```C
#if CONFIG_X86_PAE
typedef struct { unsigned long pte_low, pte_high; } pte_t;
typedef struct { unsigned long long pmd; } pmd_t;
typedef struct { unsigned long long pgd; } pgd_t;
#define pte_val(x)	((x).pte_low | ((unsigned long long)(x).pte_high << 32))
#else
typedef struct { unsigned long pte_low; } pte_t;
typedef struct { unsigned long pmd; } pmd_t;
typedef struct { unsigned long pgd; } pgd_t;
```

### 3.4 转换和设置页表项

构造pte
```C
#define mk_pte(page, pgprot)	__mk_pte((page) - mem_map, (pgprot)) ; 这里的page是个page结构的指针
#define mk_pte_phys(physpage, pgprot)	__mk_pte((physpage) >> PAGE_SHIFT, pgprot) ;physpage是物理页的地址
#define __mk_pte(page_nr,pgprot) __pte(((page_nr) << PAGE_SHIFT) | pgprot_val(pgprot))

#define __pte(x) ((pte_t) { (x) } )
```

返回pte对应的page
```C
#define pte_page(x)		(mem_map+((unsigned long)(((x).pte_low >> PAGE_SHIFT)))) ;返回mem_mep数组中的偏移，结果为page_t *
```

将pte设置成某个值
```C
#define set_pte(pteptr, pteval) (*(pteptr) = pteval)
#define set_pte_atomic(pteptr, pteval) (*(pteptr) = pteval)
```

### 3.5 分配和释放页表