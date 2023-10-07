---
categories:
  - UPMP
---
# 通用导出功能

## 1. 平台与文档

测试环境 #通用导出
11.91.140.68 exp.jd.com

[cf文档](https://git.jd.com/jvg/common-export/wikis/4.%E6%8E%A5%E5%85%A5%E7%AC%AC%E4%B8%89%E6%AD%A5%E2%80%94%E2%80%94%E5%AE%A2%E6%88%B7%E7%AB%AF%E6%8E%A5%E5%85%A5%E4%BB%A3%E7%A0%81) 

## 2. 使用注意

> 1.在通用导出平台设置数据源以及导出配置
>
> 2.代码处需注意调用处 以及回调处（出参List）

```java
   QueryParamDTO queryParamDTO = new QueryParamDTO();
        queryParamDTO.setActivityId(0);
        queryParamDTO.setActivityState(10);
        String param2 = "2xxx";
        // vcActivityDataGuideService.getConverterData(queryParamDTO, param2);
        //模拟 模拟 模拟 模拟  业务代码参数，你需要换成你自己的业务代码
        Title title1 = new Title("item.data==dcId","data","item.data == null ? null: item.data.dcId");
        Title title2 = new Title("dcId","data", "item.data.dcId");
        Title title2_1 = new Title("item.data==item.data.dcName","data", "item.data == null ? null: item.data.dcName");
        Title title2_2 = new Title("item.data.dcName","data", "item.data.dcName");
        Title title3 = new Title("Str","data1");
        List<Title> list = Lists.newArrayList(title1,title2,title2_1,title2_2,title3);
        ExportRequest exportRequest = JsfExportRequestBuilder.newBuilder()
                .withExportCode("vc_p009_b012_zk")  //设置导出code,你在平台上配置的 （可以在代码中写死，不用提取成变量，让用户填写一个code的目的就是简化测试和生产环境的变量区分）
                .withDynamicTitle(list)
                .withParameters(queryParamDTO,param2) //数据提供方参数(你接口怎么传就怎么写)
                .withOperator("sunya06")//操作人
                .build();//最后build对象别忘了
        //调用同步导出方法，根据你的需要选择是同步还是异步
        ExportResponse exportResponse = commonExportService.syncExport(exportRequest);
        log.info(exportResponse.getResult()+"===同步导出返回= url=================");

        ExportResponse exportResponse1 = commonExportService.asyncExport(exportRequest);
        log.info(exportResponse1.getResult()+"===异步导出返回= uuid=================");

        return "sync:" + exportResponse.getResult() + "-----async:" + exportResponse1.getResult();
```

> 3. 注册JSF服务，暴露回调接口

```java
    @Override
    public List<ConverterData> getConverterData(QueryParamDTO queryParam, String str) {
        log.info("回调开始，queryParam:{},str:{}",queryParam,str);
        List<ConverterData> converterDatas = new ArrayList<>();

        for(int i = 0; i < 5; i++){
            ConverterData converterData = new ConverterData();
            DcVO dcVO = new DcVO();
            dcVO.setDcId(i);
            dcVO.setDcName("Name-" + i);
            converterData.setData(dcVO);
            converterData.setData1("str-"+ str + (10+i));
            converterDatas.add(converterData);
        }
        log.info("回调结束，converterDatas：{}",converterDatas);
        return converterDatas;
    }
```

## 3.总结

对比EasyExcel，通用导出平台 开放复杂对象处理。valueConvert 使用SPEL处理。

EasyExcel只在实体类中简单使用导出注解（包括格式化），而通用导出可以自定义高级用法。

同时再jd内部 提供云存储，以及邮件发送功能。