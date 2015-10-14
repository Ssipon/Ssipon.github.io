---
layout: post
title: Java 基于 AtomicLong 高并发计数器
date: 2015-10-13 17:31:54
categories: blog
tags: [Java,高并发,计数器]
description:  使用AtomicLong实现的高并发计数器（[LongAdder 更高效](http://ifeve.com/atomiclong-and-longadder/)）
---


## 示例代码

AtomicLong 实现的计数器，Java 8 LongAdder更高效！ [详情](http://ifeve.com/atomiclong-and-longadder/)

```@Service```
```public class EnterpriseNameSuffixService implements InitializingBean { ```

    @Autowired
    private EnterpriseNameSuffixRepository enterpriseNameSuffixRepository;
    private AtomicLong atomicLong = new AtomicLong();
    private static int SUFFIX_SPAN_SIZE = 50;
    @Override
    public void afterPropertiesSet() throws Exception {
        //获取当前记录计数
        long currentSuffix = getCurrentSuffix();
        //添加到当前计数器
        atomicLong.set(currentSuffix);
        //重启服务后，更新后缀，防止服务在 SUFFIX_SPAN_SIZE 内被重启
        updateEnterpriseNameSuffix(currentSuffix + SUFFIX_SPAN_SIZE);
    }
    //获取下一个计数后缀
    public String getNextSuffix() {
        long counterSuffix = atomicLong.getAndIncrement();
        if (counterSuffix % 50 == 0) {
           updateEnterpriseNameSuffix(counterSuffix + SUFFIX_SPAN_SIZE);
        }
        NumberFormat numberFormat = new DecimalFormat("000000");
        return numberFormat.format(atomicLong.get());
    }
    //从数据库中获取当前记录计数
    private long getCurrentSuffix() {
        Sort sort = new Sort("create_time", "desc");
        List<EnterpriseNameSuffix> enterpriseNameSuffixs = enterpriseNameSuffixRepository.findAll(sort);
        for (EnterpriseNameSuffix enterpriseNameSuffix : enterpriseNameSuffixs) {
            return enterpriseNameSuffix.getSuffix();
        }
        return 0;
    }
    private void updateEnterpriseNameSuffix(long suffix) {
        EnterpriseNameSuffix enterpriseNameSuffix = new EnterpriseNameSuffix();
        Sort sort = new Sort("create_time", "desc");
        List<EnterpriseNameSuffix> enterpriseNameSuffixs = enterpriseNameSuffixRepository.findAll(sort);
        for (EnterpriseNameSuffix tEnterpriseNameSuffix : enterpriseNameSuffixs) {
            enterpriseNameSuffix = tEnterpriseNameSuffix;
            break;
        }
        enterpriseNameSuffix.setSuffix(suffix);
        enterpriseNameSuffix.setCreateTime(System.currentTimeMillis());
        enterpriseNameSuffixRepository.save(enterpriseNameSuffix);
    }
}

