---
title: '通过Hudi学Flink-Hudi源码解析:HoodieTableSink'
date: 2022-01-09 16:49:21
tags:
---



首先，我们来想象一下，但我们通过Flink SQL执行下面这段SQL向Hudi Table写入数据时，会发生什么？
```
INSERT INTO default_catalog.default_database.t_ciam_event_cow
SELECT
       userId AS user_id,
       traits.dob AS dob,
       traits.emailVerified AS email_verified,
       traits.gender AS gender,
       traits.phoneNumberVerified AS phone_number_verified,
       traits.appPromotionNotificationPreference AS app_promotion_np,
       traits.appUpdateNotificationPreference AS app_update_np,
       traits.emailPromotionNotificationPreference AS email_promotion_np,
       traits.emailUpdateNotificationPreference AS email_update_np,
       traits.privacyPolicy AS privacy_policy,
       traits.smsPromotionNotificationPreference as sms_promotion_np,
       traits.terms AS terms,
       `timestamp`,
       DATE_FORMAT(`timestamp`, 'yyyy-MM-dd') as dt
FROM default_catalog.default_database.t_ciam_event;
```
在Flink SQL Client端：
1. 首先，Flink执行引擎会调用DynamicTableSinkFactory的createDynamicTableSink创建对应的DynamicTableSink接口的实现类， HoodieTableFactory实现了createDynamicTableSink方法，创建实现了DynamicTableSink接口的HoodieTableSink类，并从Flink Context中获取对应Sink Table的Schema，并注入到HoodieTableSink中。
2. 接着，Flink执行引擎调用实现了HoodieTableSink的getSinkRuntimeProvider方法获取一个实现了SinkRuntimeProvider接口的匿名类，改匿名类通过consumeDataStream方法接受RowData类型的DataStream，构建对应的流处理Pipeline Operator，从而构建对应的StreamGraph。
3. 在构建好StreamGraph之后， Flink SQL Cient将任务提交到JobManager进行调度执行。

我们再来看看HoodieTableSink实现的SinkRuntimeProvider细节，大致流程包含如下几步：
- 基本信息配置，如获取Checkpoint的timeout时间，作为Hudi write commit ack的timeout值
- 