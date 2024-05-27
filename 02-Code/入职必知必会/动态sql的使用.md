####                                                                                                                                                                   Java入职必知必会之Mybatis中动态sql的使用

​                                                                                                                             v: nb887722             微信公众号：拙野    zy521      CSDN:拙野



##### 一、动态sql是什么？

```
动态SQL是一种在构建SQL语句时根据特定条件动态地拼接SQL字符串的技术。
动态SQL主要解决了传统JDBC编程中手动拼接复杂SQL语句的繁琐和易错问题。通过动态SQL，开发者可以根据不同的业务逻辑需求，编写更加灵活和可维护的SQL语句。
```

##### 二、mybatis中动态sql标签

######      1、if标签      

​    用于判断

   * 使用语法

     ```
     <if test="判断条件">    
     SQL语句
     </if>
     说明：
      1）if标签：判断语句，用于进行逻辑判断的。如果判断条件为true，则执行if标签的文本内容
      2）test属性：用来编写表达式；
     ```

   * 示例

     1）数值类型的判断

     ```java
     不等于的判断：
     //注意Interge类型的条件判断是否为空的时候一定不要加非空字符串判断，因为当你传的值为0的时候，mybatis会把它判断为空字符串
      <if test="equipTypeId != null">  
             AND t1.equip_type_id=#{equipTypeId}
      </if>
     等于的判断：
      <if test="equipTypeId == 2">  
             AND t1.equip_type_id=#{equipTypeId}
      </if>
     ```

     2）字符串类型的判断

     ```java
     不等于的判断：
         <if test="equipAttribute!=null and equipAttribute!=''">
             AND t1.equip_attribute=#{equipAttribute}
         </if>
       等于的判断： 
         <if test="equipAttribute == '易碎品'.toString() ">
             AND t1.equip_attribute=#{equipAttribute}
         </if>
     ```

     3）list类型的判断

```java
  不等于的判断：
    <if test="list !=null and list.size()>0">
        ........
    </if>
  等于的判断： 
    <if test="list ==null OR list.size() == 0 ">
        AND t1.equip_attribute=#{equipAttribute}
    </if>
```

​     4）布尔类型

```java
  <if test=" isdelete ">
        ..........
  </if>
```

###### 2、where标签

​     where标签一般都是和if标签配合使用。会自动添加where关键字，并且去除多余的and或者or

   * 使用语法

     ```Java
      <where>
           //多个判断条件，去除多余的and或者or
           ...........   
      </where>
     ```

     

   * 示例

     ```Java
      <where>
                 <if test="dto.strategyName != null and dto.strategyName != ''">
                    and strategy_name like concat('%', #{dto.strategyName}, '%')
                 </if>
                 <if test="dto.serviceId != null">
                     and service_id = #{dto.serviceId}
                 </if>
                 <if test="dto.status != null">
                     and status = #{dto.status}
                 </if>
      </where>
     ```
     


###### 3、set标签

在update语句中使用,可以自动添加一个set关键字，而且也会把动态sql最后多余的逗号去除。

* 使用语法

  ```
   <set >
    //多个判断条件，去除多余的逗号
        ...........  
   </set >
  ```

  

* 示例

```java
update t_alarm_info
    <set>
      <if test="item.alarmTitle != null and item.alarmTitle != ''" >
        alarm_title = #{item.alarmTitle,jdbcType=VARCHAR},
      </if>
      <if test="item.alarmLevel != null and item.alarmLevel != ''" >
        alarm_level = #{item.alarmLevel,jdbcType=INTEGER},
      </if>
      <if test="item.alarmRule != null and item.alarmRule != ''" >
        alarm_rule = #{item.alarmRule,jdbcType=VARCHAR},
      </if>
      <if test="item.lastTime != null" >
        last_time = #{item.lastTime,jdbcType=TIMESTAMP},
      </if>
      <if test="item.recoveryTime != null " >
        recovery_time = #{item.recoveryTime,jdbcType=TIMESTAMP},
      </if>
      <if test="item.status != null" >
        status = #{item.status,jdbcType=INTEGER},
      </if>
    </set>
      where alarm_id = #{item.alarmId}
```



###### 4、choose，when，otherwise标签

​    choose里面包含when、otherwise两个子标签，choose是父标签，when和otherwise必须都要写在父标签choose里面

   * 使用语法

     ```java
      <choose>
            //无论有多少个when条件，一旦其中一个条件成立，后面的when条件都不再会执行。
            <when test="判断条件">
                  SQL语句
             </when>
              <when test="判断条件">
                  SQL语句
             </when>
             //当上面所有的when条件都不满足时，才会执行该条件。
             <otherwise>
                  SQL语句
             </otherwise>
      </choose>
     ```

     

    * 代码示例

```java
  <select id="queryByUserNameOrAddress" resultType="user">
        select * from user where sex='男'
        <choose>
            <when test="userName!=null and userName.trim()!=''">
                and username like '%${userName}%'
            </when>
            <when test="address!=null and address.trim()!=''">
                and address = #{address}
            </when>
            <otherwise>
                and username='孙悟空'
            </otherwise>
        </choose>
    </select>
```



###### 5、foreach标签

​      遍历集合或者数组，一般都是用于批量修改或者批量新增

* 使用语法

  ```java
  <foreach collection="集合名或者数组名" item="元素" separator="标签分隔符" open="以什么开始" close="以什么结束">
     #{元素}
  </foreach>
  ```

* 示例

  1)新增

  ```Java 
  方法：void insertBatch(@Param("dtoList") List<ThirdCloudAlarm> addList); 
  
  <insert id="insertBatch"  >
      insert into t_alarm_info (alarm_id, instance_id,
        instance_name, instance_group, product_name,
        alarm_title, alarm_level, alarm_rule, 
        occur_time, last_time, recovery_time, 
        status, cr_time, up_time)
      values
      <foreach collection="dtoList" item="item" separator=",">
      ( #{item.alarmId,jdbcType=VARCHAR},
        #{item.instanceId,jdbcType=VARCHAR},
        #{item.instanceName,jdbcType=VARCHAR}, #{item.instanceGroup,jdbcType=VARCHAR}, #{item.productName},
        #{item.alarmTitle,jdbcType=VARCHAR}, #{item.alarmLevel,jdbcType=INTEGER}, #{item.alarmRule,jdbcType=VARCHAR},
        #{item.occurTime,jdbcType=TIMESTAMP}, #{item.lastTime,jdbcType=TIMESTAMP}, #{item.recoveryTime,jdbcType=TIMESTAMP},
        #{item.status,jdbcType=INTEGER},now(), now()
        )
      </foreach>
    </insert>
  ```

  2）更新

  ```java
  方法：void updateBatch(@Param("dtoList") List<ThirdCloudAlarmDto> updateList);
  
  <update id="updateBatch"  >
      <foreach collection="dtoList" separator=";" item="item" >
      update t_alarm_info 
      <set >
        <if test="item.alarmTitle != null and item.alarmTitle != ''" >
          alarm_title = #{item.alarmTitle,jdbcType=VARCHAR},
        </if>
        <if test="item.alarmLevel != null and item.alarmLevel != ''" >
          alarm_level = #{item.alarmLevel,jdbcType=INTEGER},
        </if>
        <if test="item.alarmRule != null and item.alarmRule != ''" >
          alarm_rule = #{item.alarmRule,jdbcType=VARCHAR},
        </if>
        <if test="item.lastTime != null" >
          last_time = #{item.lastTime,jdbcType=TIMESTAMP},
        </if>
        <if test="item.recoveryTime != null " >
          recovery_time = #{item.recoveryTime,jdbcType=TIMESTAMP},
        </if>
        <if test="item.status != null" >
          status = #{item.status,jdbcType=INTEGER},
        </if>
      </set>
        where alarm_id = #{item.alarmId}
      </foreach>
    </update>
  ```

  ```java
  方法： void updateAutomaticIsInform(@Param("alarmIds") List<String> alarmIds);
  <update id="updateIsInform" parameterType="java.util.List">
          update alarm
          set unprocessed_is_inform = 1
          where id in 
          <foreach collection="alarmIds" item="item" separator=","  open="(" close=")">
              #{item}
          </foreach>
  </update>
  ```

  

###### 6、trim标签

​      trim标签可以去除sql语句中多余的and关键字，逗号等。

* 使用语法

  ```
  <trim prefix="sql语句需要加的前缀" suffix="sql语句需要加后缀" suffixOverrides="指定去除多余的后缀内容" prefixOverrides="指定去除多余的前缀内容">
  sql语句
  </trim>
  ```

* 示例

​       1）根据条件判断新增内容

```java
<insert id="insertSelective" parameterType="com.zcloud.domain.warehousemanage.dto.WmEquipInfoDto">
        insert into t_wm_equip_info
        <trim prefix="(" suffix=")" suffixOverrides=",">
            <if test="equipCode != null">
                equip_code,
            </if>
            <if test="equipAttribute != null">
                equip_attribute,
            </if>
            <if test="equipName != null">
                equip_name,
            </if>
        </trim>
        <trim prefix="values (" suffix=")" suffixOverrides=",">
            <if test="equipCode != null">
                #{equipCode,jdbcType=VARCHAR},
            </if>
            <if test="equipAttribute != null">
                #{equipAttribute,jdbcType=VARCHAR},
            </if>
            <if test="equipName != null">
                #{equipName,jdbcType=VARCHAR},
            </if>
    
        </trim>
    </insert>
```

​    2)去除多余的and关键字

```java
<trim prefix="WHERE" prefixOverrides="AND">
            <if test="dto.strategyName != null and dto.strategyName != ''">
              and  strategy_name like concat('%', #{dto.strategyName}, '%')
            </if>
            <if test="dto.serviceId != null">
                and service_id = #{dto.serviceId}
            </if>
            <if test="dto.status != null">
                and status = #{dto.status}
            </if>
 </trim>
```



