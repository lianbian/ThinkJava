---
title: 大文件内容对比
tags: java 算法 algorithm 排序
categories: algorithm
---

* TOC
{:toc}

最近接到一个需求，要对两个系统的订单进行对比，找出其中的差异，对比结果有4种：一致、不一致、丢失、多余。

如果数据量少，处理起来就很简单，例如要对A,B两份数据进行对比：
1. 将数据A放入哈希表
2. 遍历数据B，依据id从A中查找对应的订单
3. 若从A中找到了对应的订单，则比较是否一致，并将此订单标记为已匹配
4. 若从A中找不到对应的订单，则表示A丢失此订单
5. 遍历A，筛选出所有未匹配的订单，这些订单都是A中多余的

但是如果数据量超大，在内存中处理就不合适了，数据需要放在磁盘上。
基于哈希的对比算法，无法避免大量对磁盘的随机访问，因而无法使用buffer，完全的io读写性能太差，所以这个方案是不行的。

如果AB两个集合是有序的，那么对比将会变得容易，一次遍历就可以对比完成，例如以id排序
1. 读取A指针对应的订单，读取B指针对应的订单
2. 若两个订单id相等，则对比内容是否一致
3. 若A指针订单id>B指针订单id，代表A中缺少B指针订单，记录结果，B指针移动到后一个
4. 若A指针订单id<B指针订单id，代表A中多余了指针订单，记录结果，A指针移动到后一个

所以，这个问题可以拆分为：对A、B文件内容进行排序，双指针遍历 A、B。

# 大文件内容排序

对大文件的内容进行排序，可以使用拆分->排序->合并的思路
1. 按行读取文件，将读到的行放入list
2. 如果已读取的行达到了1万行，对list进行排序，将排序后的list逐行写入临时文件
3. 打开多个临时文件，从每个文件的中逐行读取，选取其中订单id最小的行，写入新的临时文件
4. 循环以上步骤，直至剩下一个文件

 示例代码
 ~~~java
 while (true){
     line = br.readLine();
     if(line != null){
         chunkRows.add(line);
     }
     if(line == null || chunkRows.size() >= initialChunkSize){
         if(chunkRows.size() > 0){
             chunkRows.sort(comparator);
             Chunk chunk = initialChunk(rowNum, chunkRows, file);
             chunkList.add(chunk);
             rowNum += chunkRows.size();
             chunkRows.clear();
         }
     }
     if(line == null){
         break;
     }
 }
 ~~~
