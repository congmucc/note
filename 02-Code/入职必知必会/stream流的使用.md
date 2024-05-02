#####                                                                                                                                                                                                                                                                java入职必知必会之stream流的使用

​                                                                                                                                 v: nb887722          公众号： 拙野   关注回复zy521领取视频资料        CSDN:拙野

##### 1、对map集合的操作            

* map集合value总和   

  ```
    long sum = datas.entrySet().stream().filter(entry -> !entry.getKey().equals("1") && !entry.getKey().equals("3")
                          && !entry.getKey().equals("6") && !entry.getKey().equals("7") && !entry.getKey().equals("8")
                          && !entry.getKey().equals("10"))
                          .mapToLong(entry -> Long.parseLong(entry.getValue())).sum();
  ```

* 合并两个map,key相同的value进行加和

```
map1.forEach((key, value) -> map2.merge(key, value, Integer::sum));//map1合并到map2,map类型<String,Integer>
```

##### 2、对list集合的操作

   * 根据时间对list集合的对象进行升序排序

     ```java
      List<ThirdCloudAlarmDto> dtoList = collect.get(key).stream()
                         .sorted(Comparator.comparing(ThirdCloudAlarmDto::getOccurTime)).collect(Collectors.toList());
     ```

   * stream流根据时间筛选所需数据

     ```
     List<CameraAlarmDto> collect = cameraAlarms.stream().
                             filter(e -> (e.getTimeLast().after(startDate) &&
                                     e.getTimeLast().before(endDate))).collect(Collectors.toList());
     ```

   * 对list集合根据某一个元素进行过滤，并选取过滤后的集合对象中的某些属性组合成新的集合

     ```
      groups.forEach(group -> {
                 List<DceEquipGroupInfoVo> dceEquipGroupInfoVos = dceEquipInfoDtos.stream().
                         filter((DceEquipGroupInfoDto d) -> group.getNodeName().equals(d.getNodeName())).
                         map(item -> {
                             DceEquipGroupInfoVo equipGroupInfoVo = new DceEquipGroupInfoVo();
                             equipGroupInfoVo.setEquipKey(item.getEquipKey()).setEquipName(item.getEquipName());
                             return equipGroupInfoVo;
                         }).distinct().collect(Collectors.toList());
           }
     ```
     
   * 对List集合使用分页查询

     ```
     thirdCloudHostDtos = thirdCloudHostDtos.stream().skip((pageNum - 1) * pageSize)
                         .limit(pageSize).collect(Collectors.toList());
     ```

   * 对list集合中对象中属性为字符串数字比较倒叙排序

```
 List<MetricVO> listTop5 = list.stream().sorted(Comparator.comparing(MetricVO::getValue,
                Comparator.comparingInt(Integer::parseInt)).reversed()).limit(5).collect(Collectors.toList());
```

   * 使用stream流进行模糊查询

     ```
     keyWordsList = dtoList.stream().filter(e -> Boolean.FALSE ? e.getServerIp().equals(keyWords)
                          || e.getServerPort().equals(keyWords) || e.getClientIp().equals(keyWords)
                          || e.getClientPort().equals(keyWords) || e.getAppProgram().equals(keyWords)
                          || e.getProtocol().equals(keyWords) :
                          (e.getServerIp().contains(keyWords)) || e.getServerPort().contains(keyWords) || e.getClientIp().contains(keyWords)
                                  || e.getClientPort().contains(keyWords) || e.getAppProgram().contains(keyWords)
                                  || e.getProtocol().contains(keyWords)).collect(Collectors.toList());
     ```

   * 取多个list集合的交集

```
dealedList = keyWordsList.stream().filter(appList::contains).filter(protocolList::contains).collect(Collectors.toList());
```

   * 根据对象某一字段组成类型集合 

     ```
      List<String> appTypes = dtoList.stream().filter(item ->item.getAppProgram() != null)
                             .map(HistoricalFlowDto::getAppProgram).distinct().collect(Collectors.toList());  
```
     
   * 求list属性中某一属性的和

```

Integer totalOrders = usedStatisticsVoList.stream().mapToInt(ServiceUsedStatisticsVo :: getWorkOrderNum).sum();  //list中存储的是对象
 
 Long totalOrders = mapList.stream().mapToLong((s) -> Long.valueOf(String.valueOf(s.get("num")))).sum();  //list中存储的是map集合
```

   * 根据list中对象的某一属性组合成新的集合

```
List<String> instanceIds = instances.stream().map(ThirdCloudInstance::getInstanceId).collect(Collectors.toList());
```

   * 按照list对象中的某一属性进行分组

```
Map<String, List<MonitorBusyDegreeResult>> collect = list.stream()
        .collect(Collectors.groupingBy(MonitorBusyDegreeResult::getDataDesc));
```

   * list中存储对象，根据对象某一属性排序

```
List< MetricVO> metricVosBusy  = new ArrayList<>();
businessSystems.stream().sorted(Comparator.comparing(MonitorBusyDegreeResult::getBusyDegree, Comparator.reverseOrder())).collect(Collectors.toList())
        .forEach(e -> {
            metricVosBusy.add(new MetricVO().setKey(e.getDataDesc()).setValue(String.valueOf(e.getBusyDegree())));
        });
```

   * list中存储map,按map中的某一key对应的value进行排序，取排行

     ```
     //正序//  
     List<Map<String, Object>> listTop9 = list.stream().sorted(Comparator.comparing(e -> org.apache.commons.collections.MapUtils.getLong(e, "count"))).limit(9).collect(Collectors.toList());
//逆序
     List<Map<String, Object>> listTop9 = list.stream().sorted((c1, c2) -> org.apache.commons.collections.MapUtils.getDouble(c2, "count")        .compareTo(org.apache.commons.collections.MapUtils.getDouble(c1, "count"))).limit(9).collect(Collectors.toList());
```
    
    
    
    注意事项：
    
    ```
    1、stream流中不要对变量进行操作，如果你需要对变量进行操作，则最好不要用stream流，避免在stream流中对变量进行操作
    2、遍历的时候需要通过条件判断跳出本次循环的时候可以使用return等同于for循环中的continue，如果有需要直接结束循环的操作就不建议使用stream流进行遍历了
    3、使用parallelStream并行流的时候，要注意线程安全问题，建议能不用并行流的时候就不要用并行流。
    ```
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    