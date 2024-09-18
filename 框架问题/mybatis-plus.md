
##### 1 自己建立文件

*一个select语句*
```xml
    <select id = "listTopInvokeInterfaceInfo" resultType="com.zyq.zyqapicommon.model.entity.UserInterfaceInfo"  >  
--   编写SQL语句  
        select interfaceInfoId, sum(totalNum) as totalNum from user_interface_info group by interfaceInfoId order by totalNum desc  
        limit #{limit};    </select>
```