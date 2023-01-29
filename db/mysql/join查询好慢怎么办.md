### step1
explain 查看执行计划,是否存在"Using join buffer(Block Nested loop)",BNL算法会导致被驱动表多次扫描,占用I/O资源;还要在join buffer中进行M*N次的比对计算,占用cpu资源;如果被驱动表是一张很大的冷数据表,还有可能导致Buffer pool的热数据被淘汰,影响内存命中率

### step2
如果存在BNL的情况,我们要想办法将其转BAK算法,通常是在被驱动表的join字段上加索引.若被驱动表很大且sql执行频率很低,加索引太浪费,那就可以创建一张与被驱动表schema相同的临时表,并在join字段上加上索引,再将符合判断条件的数据行插入临时表,与临时表进行join操作,性能也可以得到很大的提升