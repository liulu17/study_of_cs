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