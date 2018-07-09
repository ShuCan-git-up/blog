---
title: mybatis书籍观后感
date: 2018-03-26 10:07:22
tags: [mybatis, jdbc]
categories: [读书笔记]
---

一直在使用的mybatis最近感觉自己对它了解的还是不够深，以前在项目中碰到了mybatis相关的问题只是百度一下，看看博客来解决问题。但是现在发现这对于以后的深入使用是远远不够的，所以决定好好的看看书，吸收一下更深一点的关于mybatis的知识

~~~
today is monday

let's begin to study mybatis

~~~



### 自动映射

参数autoMappingBehavior,当它不设置为none的时候，Mybatis会提供自动映射的功能，只要返回的SQL列名和javabean的属性一直，mybatis就会帮助我们回填这些字段而无需任何配置，大部分的数据库规范都是要求每个单词用下划线分割，而java则是采用驼峰命名法来命名，于是使用类的别名就可以似的mybatis自动映射，或者直接在配置文件中开启驼峰命名方式

自动映射可以在settings元素中配置autoMappingBehavior属性值来设置其策略，它包含三个值

+ NONE，取消自动映射
+ PARTIAL，只会自动映射，没有定义嵌套结果集映射的结果集
+ FULL，会自动映射任意复杂的结果集（无论是否嵌套）

默认是PARTIAL,开启FULL是嵌套映射，在性能上会下降

### 多参数问题的解决方式

当我们需要传递多个参数给映射器的时候，解决方式主要有一下几种

#####javaBean

~~~Java
package com.lgeek.smartHotel.params.Role
public class Role {
    
    private Long id;
    private String roleName;
    private String note;
    
    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getRoleName() {
        return roleName;
    }

    public void setRoleName(String roleName) {
        this.roleName = roleName;
    }

    public String getNote() {
        return note;
    }

    public void setNote(String note) {
        this.note = note;
    }
}
~~~

#### 使用map传递参数

##### 映射器xml文件

~~~xml
<select id = "findRoleByMap" parameterType = "map" resultMap = "roleMap">
	select id, role_name, note from t_role where role_name like concat ('%', #{roleName}, '%') and note like concat('%', #{note}, '%')
</select>
~~~

##### 映射器接口

~~~java
public interface RoleMapper{
    /**
    *这里也可以以注解的形式来替代上面的xml文件执行相应的sql操作
    */
    public List<Role> findRoleMap(Map<String ,String> params);
}

~~~

##### 执行的java代码

~~~java
Map<String, String> paramsMap = new HashMap<String, String>
paramsMap.put("roleName", "me");
paramsMap.put("note", "he");
roleMapper.findRoleMap(paramsMap);
~~~

这总方式虽然简单易用，但是有弊端，这种参数使用了map，map是键对值得形式，业务的关联性不强，你需要深入到程序中看代码，造成可读性下降

####使用注解传递参数

需要使用MyBatis的参数注解@Param(org.apache.ibatis.annotations.Param)

##### 映射器接口

~~~~~Java
public interface RoleMapper{
    /**
    *这里也可以以注解的形式来替代上面的xml文件执行相应的sql操作
    */
    public List<Role> findRoleMap(@Param("roleName") String roleName, @Param("note") String note);
}
~~~~~

##### 映射器xml文件

此种方式无需定义参数类型在xml文件里面

~~~xml
<select id = "findRoleByMap" resultMap = "roleMap">
	select id, role_name, note from t_role where role_name like concat ('%', #{roleName}, '%') and note like concat('%', #{note}, '%')
</select>
~~~

这种方式可读性比较高，但是当参数比较多的情况下，参数就十分复杂了，可读性依旧也不高。这种情况下，mybatis为我们提供了使用JavaBean来定义参数

#### 使用JavaBean来传递参数

##### 映射器接口

~~~java
public List<Role> findRoleMap(Role role);
~~~

##### 映射器xml文件

此处的parameterType也可以使用别名，在mybatis上下文种配置的使用名称

~~~xml
<select id = "findRoleByMap" parameterType = “com.lgeek.smartHotel.params.Role” resultMap = "roleMap">
	select id, role_name, note from t_role where role_name like concat ('%', #{roleName}, '%') and note like concat('%', #{note}, '%')
</select>
~~~



总结:map传递参数，造成了业务可读性的丧失，而且不好扩展，也不好维护，当参数大于五个的时候，建议采用JavaBean的方式传递参数

#### resultMap结果映射集简单了解

其本身是一个很强大的自定义映射的工具

##### 映射器xml文件中定义resultMap

~~~xml
<resultMap id = "roleMap" type = "com.lgeek.smartHotel.params.Role">
    <id property = "id", column = "id"/>
    <result property = "roleName", column = "role_name"/>
    <result property = "note", column = "note"/>
</resultMap>

<select id = "findRoleByMap" parameterType = “com.lgeek.smartHotel.params.Role” resultMap = "roleMap">
	select id, role_name, note from t_role where role_name like concat ('%', #{roleName}, '%') and note like concat('%', #{note}, '%')
</select>
~~~

##### resultMap配置详解

+ type也可是使用别名（在mybatis上下文种定义的一个代表Role.java文件的名称）
+ id元素代表着这个对象哪个属性代表主键，result元素定义普通列的映射关系，例如：把SQL结果返回的列role_name和javaBean的属性roleName定义起来
+ 这样select语句就不需要再使用自动映射的规则，这样mybatis就会使用我们自己定义的映射规则

resultMap是映射器里面最为复杂的元素，他一般用于复杂，级联这些配置，后续博客会对此深入讲解。

### typeHandler详解

mybatis为我们注册了多个typeHander

org.apache.ibatis.type.TypeHandlerRegistry

| 类型处理器         | java类型                  | JDBC类型                          |
| ------------------ | ------------------------- | --------------------------------- |
| BooleanTypeHandler | Java.lang.Boolean,boolean | 数据库兼容的BOOLEAN               |
| ByteTypeHandler    | Java.lang.Byte,byte       | 数据库兼容的NUMERIC,BYTE          |
| ShortTypeHandler   | Java.lang.Short,short     | 数据库兼容的NUMERIC,SHORT INTEGER |
| …….                | …….                       | …….                               |

> 具体详细typeHander对照表网上有很多，可以自行搜索

有以下细节需要注意

+ 数值类型的精度，数据库int,double,decimal这些类型和java的精度，长度都是不一样的
+ 时间精度，取数据到日用DateOnlyTypeHandler即可，用到精度为秒的用SqlTimestampTypeHandler等

##### typeHandler

~~~java
public class StringTypeHandler extends BaseTypeHandler<String> {
    @Override
    public void setNonNullParameter(PreparedStatemenet ps, int i, String parameter, JdbcType jdbcType) throws SQLException {
        ps.setString(i ,parameter);
    }
    
    @Override
    public void getNullableResult(ResultSet rs, String columnName) throws SQLException {
        return rs.getString(columnName);
    }
    
    @Override
    public void getNullableResult(ResultSet rs, int columnIndex) throws SQLException {
        return rs.getString(columnIndex);
    }
    
    @Override
    public void getNullableResult(CallableStatement cs, int columnIndex) throws SQLException {
        return cs.getString(columnIndex);
    }
}
~~~

StringTypeHandler继承BaseTypeHandler实现TypeHandler(自己定义了四个抽象方法)