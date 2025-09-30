
> 沒有使用 Spring JPA 的話，Spring Batch 會啟用一個 H2 數據庫，在這個數據庫中，Spring 會對 Batch 需要的配置進行配置。
> 使用 Spring JPA 的話，你需要 Spring Batch 幫你初始化表。解決辦法就是在項目配置文件中，設置：spring.batch.initialize-schema=ALWAYS



- [spring batch 作業流詳解](https://javamana.com/2021/12/202112282255110453.html)
- [深入了解 Spring 之 Spring Batch 框架](https://xie.infoq.cn/article/e5dbcc8108687652bd622a1c2)
- [大數據批處理框架Spring Batch 的全面解析](https://blog.csdn.net/weixin_44233163/article/details/85992105?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_title~default-1.pc_relevant_paycolumn_v2&spm=1001.2101.3001.4242.2&utm_relevant_index=3)
- [SpringBatch 系列入門之 Tasklet](https://xie.infoq.cn/article/8d306466494201ae42861cc43)
- [大量資料也不在話下，Spring Batch並行處理四種模式初探](https://www.it145.com/9/24858.html)
- [通過例子講解Spring Batch入門](https://segmentfault.com/a/1190000024439130)
- [spring batch ItemReader詳解](https://blog.csdn.net/a18792721831/article/details/110637684)
- 