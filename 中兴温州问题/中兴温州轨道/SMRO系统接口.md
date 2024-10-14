### 1 org.springframework.http.converter.HttpMessageNotReadableException: JSON parse error: Cannot deserialize value of type `java.time.LocalDateTime` from String "2024:10:08 14:53:12": Failed to deserialize java.time.LocalDateTime: (java.time.format.DateTimeParseException) Text '2024:10:08 14:53:12' could not be parsed at index 4; nested exception is com.fasterxml.jackson.databind.exc.InvalidFormatException: Cannot deserialize value of type `java.time.LocalDateTime` from String "2024:10:08 14:53:12": Failed to deserialize java.time.LocalDateTime: (java.time.format.DateTimeParseException) Text '2024:10:08 14:53:12' could not be parsed at index 4



### 2  测试系统的地址


禅道地址: http://192.168.30.158:18080/


行车调度 : http://192.168.30.140:18088/#/user/login?redirect=%2FdepartManage%2FexecuteDepartList%2Flist


施工系统:  
http://192.168.30.140:8005/index.html#/account/todo

数据分别来自施工计划提报和请销点两个表，他们是通过施工作业代码关联的



### 测试数据库

![[3bb290cf23330fddcca911d19a9b61c.png]]


```
    url: jdbc:oracle:thin:@192.168.30.130:1521/xe
    username: SUBWAY
    password: SUBWAY@123
    driver-class-name: oracle.jdbc.OracleDriver


```




![[Pasted image 20241009165816.png]]
### 数据库表
CSM_CONSTRUCTPLAN 

列号:
执行地点:endstationName
工单编号:constructplanCode
工单描述（作业名称)workName 
实际开始时间
实际结束时间

员工人员 constleaderName
员工编号 去User里面去拿进行组装
施工状态planStatusName

terrtioriality
![[Pasted image 20241009153734.png]]
第二个表
id 唯一id
planType
属地 stoestationName beginStation也就是清点车站
planbeginTime计划开始时间
planbeginTime计划结束时间
beginApproveTime
endApproveTime



### 根据列的名字反向推表
这是oracle的
```sql
SELECT table_name 
FROM all_tab_columns 
WHERE column_name = 'BEGINAPPROVE_TIME';
```


获取施工负责人


![[Pasted image 20241009160317.png]]

拿到这个baseInfoDTO 有我需要的消息
baseInfoDTO

![[Pasted image 20241009160538.png]]



可以根据constructplanId 进行管理



两张表：csmWorkPlaceApply去拿数据


id workplaceapplyId 
beginStationName 属地
planType 计划类型
beginTime 计划开始时间
endTime 计划结束时间
bApproveTime 清点批准时间
eApproveTime  消点批准时间


例子  以c3a4cd128f7d4b379d6201d645b6156f为例
ConstructionPLAN_ID 为例

表CSM_CONSTRUCTPLAN中

和表CSM_WORKPLACEAPPLY进行关系ConstructionPLAN_ID



plan 存储


trainNo 存在 CSM_WORKCONTENT 中的VEHICLE
Location 存在 CSM_WORKCONTENT 中的 PRIMARYSTATION_CODE
work_order_num 存在 CSM_CONSTRUCTIONPLAN EAM_CODE
work_order_desc 存在  CSM_WORKCONTENT WORK_NAME
Plan_start_Time 存在 CSM_CONSTRUCTIONPLAN  PLANBEGIN_TIME
Plan_end_Time 存在  CSM_CONSTRUCTIONPLAN PLANEND_TIME
workerNum 存在 CSM_CONSTRUCTIONPLAN  CONSTLEADER_ID
worker_NAME 存在 CSM_CONSTRUCTIONPLAN CONSTLEADER_NAME

![[Pasted image 20241011112225.png]]



#### 插入的时候一个List能不能有其中的几个字段插入到一个表中从插入的表中获取到id ，然后其他的插入到另外的一个表



1. **`RETURNING INTO` 不支持多个返回值**：`RETURNING INTO` 只能对单条插入操作返回一个值，无法批量返回多个值。Oracle 不支持 `RETURNING INTO` 和批量插入一起使用。因此，批量插入时，你不能直接在 SQL 语句中使用 `RETURNING INTO` 获取多个 ID。
    
2. **`foreach` 在 `RETURNING INTO` 中的用法不正确**：`RETURNING INTO` 语句期望一个单一的目标，而不是 `foreach` 语句列表。你不能像使用 `VALUES` 那样在 `RETURNING INTO` 中使用 `foreach`。



``` XML
<insert id="insertWorkContent"  parameterType="com.interfaces.springbootinit.model.dto.construction.ConstructionPlanCsmDTO">  
    INSERT INTO CSM_WORKCONTENT (CONSTRUCTPLAN_ID, VEHICLE, PRIMARYSTATION_CODE, WORK_NAME, WORKCONCENT_ID)  
    VALUES    <foreach collection="list" item="item" index="index" separator="UNION ALL">  
        SELECT #{item.id}, #{item.trainNo}, #{item.location}, #{item.workOrderDesc}, #{item.workId}  
    </foreach>  
</insert>
```


``` XML
<insert id="insertWorkContent" parameterType="java.util.List">
    INSERT ALL
    <foreach collection="list" item="item" index="index">
        INTO CSM_WORKCONTENT (CONSTRUCTPLAN_ID, VEHICLE, PRIMARYSTATION_CODE, WORK_NAME, WORKCONCENT_ID)
        VALUES (#{item.id}, #{item.trainNo}, #{item.location}, #{item.workOrderDesc}, #{item.workId})
    </foreach>
    SELECT 1 FROM DUAL
</insert>

```


注册接口返回的内容

``` json

{

    "code": 0,

    "data": {

        "token": "eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJkZjNlZTI1MDg2NTBhZDVkYjJiY2QyYzZmMmM0MzE2MCIsImlhdCI6MTcyODcxNDAzNSwiZXhwIjoxNzI4ODAwNDM1fQ.DN-jWIEOgMUwAtiT5fpYmRXUEbwqSGlUC7YAjsVHZ4qGvp6d7a6-v_uVp6Y9glZafyh9oSojGXXqRkRU98LJ9g",

        "appInfo": {

            "id": "86bdb19a-ea6e-4d24-8f67-758ba8fed713",

            "appSecret": "df3ee2508650ad5db2bcd2c6f2c43160",

            "sysName": "施工系统",

            "sort": null,

            "status": null,

            "delFlag": null,

            "createBy": "zyq",

            "createDate": null,

            "updateBy": null,

            "updateDate": null,

            "remarks": "222"

        }

    },

    "message": "ok"

}
```



### 使用TOKEN 和 拦截器遇到的问题


拦截器的代码

``` java
package com.interfaces.springbootinit.aop;  
  
import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;  
import com.interfaces.springbootinit.annotation.AuthIgnore;  
import com.interfaces.springbootinit.common.ErrorCode;  
import com.interfaces.springbootinit.exception.BusinessException;  
import com.interfaces.springbootinit.mapper.AppInfoMapper;  
import com.interfaces.springbootinit.model.entity.AppInfo;  
import com.interfaces.springbootinit.service.AppInfoService;  
import io.jsonwebtoken.Jwts;  
import org.jetbrains.annotations.NotNull;  
import org.springframework.stereotype.Component;  
import org.springframework.util.DigestUtils;  
import org.springframework.web.method.HandlerMethod;  
import org.springframework.web.servlet.HandlerInterceptor;  
  
import javax.annotation.Resource;  
import javax.servlet.http.HttpServletRequest;  
import javax.servlet.http.HttpServletResponse;  
import java.lang.reflect.Method;  
  
@Component  
public class AuthInterceptor implements HandlerInterceptor {  
  
    private static final String SALT = "SYSTEM";  
  
    @Resource  
    private AppInfoService appInfoService;  
  
    @Override  
    public boolean preHandle(HttpServletRequest request, @NotNull HttpServletResponse response, @NotNull Object handler) throws Exception {  
        // 从请求头上面获取信息  
        String token = request.getHeader("Authorization");  
        String Secret = request.getHeader("AppSecret");  
        // 进行注册的时候放弃不拦截token  
        if (token == null || token.isEmpty()) {  
            Method method = ((HandlerMethod) handler).getMethod();  
            if (method.getAnnotation(AuthIgnore.class) == null) {  
                throw new BusinessException(ErrorCode.FORBIDDEN_ERROR);  
            }  
            return true;  
        }  
        // 验证 token        try {  
            String appSecret = Jwts.parser().setSigningKey(Secret).parseClaimsJws(token).getBody().getSubject();  
            // 验证密匙是否存在数据库当中  
            if (!appInfoService.isValidAppSecret(appSecret)) {  
                throw  new BusinessException(ErrorCode.SECRET_ERROR);  
            }  
        } catch (Exception e) {  
            throw  new BusinessException(ErrorCode.TOKEN_ERROR);  
  
        }  
  
        return true;  
    }  
  
}
```




AOP切面 忽略权限
``` java
package com.interfaces.springbootinit.annotation;  
  
import java.lang.annotation.ElementType;  
import java.lang.annotation.Retention;  
import java.lang.annotation.RetentionPolicy;  
import java.lang.annotation.Target;  
  
/**  
 * 权限校验  
 *  
 */
@Target(ElementType.METHOD)  
@Retention(RetentionPolicy.RUNTIME)  
public @interface AuthIgnore {  
  
  
}
```




注册接口和生成TOKEN
``` JAVA
package com.interfaces.springbootinit.controller;  
  
  
import com.interfaces.springbootinit.annotation.AuthIgnore;  
import com.interfaces.springbootinit.common.AuthResponse;  
import com.interfaces.springbootinit.common.BaseResponse;  
import com.interfaces.springbootinit.common.ErrorCode;  
import com.interfaces.springbootinit.common.ResultUtils;  
import com.interfaces.springbootinit.exception.BusinessException;  
import com.interfaces.springbootinit.model.dto.auth.AuthMessageDTO;  
import com.interfaces.springbootinit.model.entity.AppInfo;  
import com.interfaces.springbootinit.service.AppInfoService;  
import io.jsonwebtoken.Jwts;  
import io.jsonwebtoken.SignatureAlgorithm;  
import org.springframework.beans.factory.annotation.Value;  
import org.springframework.web.bind.annotation.PostMapping;  
import org.springframework.web.bind.annotation.RequestBody;  
import org.springframework.web.bind.annotation.RequestMapping;  
import org.springframework.web.bind.annotation.RestController;  
  
import javax.annotation.Resource;  
import java.util.Date;  
  
@RestController  
@RequestMapping("/auth")  
public class AuthController {  
  
  
    @Value("${jwt.expireTime}")  
    private long expireTime;  
  
    @Resource  
    private AppInfoService appInfoService;  
  
  
  
    @AuthIgnore  
    @PostMapping("/register")  
    public BaseResponse<AuthResponse> register(@RequestBody AuthMessageDTO authMessageDTO) {  
        String sysName = authMessageDTO.getSysName();  
        if (sysName == null) {  
            throw new BusinessException(ErrorCode.PARAMS_ERROR);  
        }  
        String appSecret = appInfoService.authRegister(authMessageDTO);  
        // 生成 token        String token = generateToken(appSecret);  
        // 创建 AuthResponse 对象返回 token 和 appInfo        AuthResponse authResponse = new AuthResponse(token, appSecret);  
        return ResultUtils.success(authResponse);  
    }  
  
  
  
  
  
    private String generateToken(String appSecret) {  
        // 生成 JWT Token        return Jwts.builder()  
                .setSubject(appSecret)  
                .setIssuedAt(new Date())  
                .setExpiration(new Date(System.currentTimeMillis() + expireTime))  
                .signWith(SignatureAlgorithm.HS512, appSecret)  
                .compact();  
    }  
  
  
}
```



服务类实现，数据库验证是否一致并且加了SALT进行
``` java
package com.interfaces.springbootinit.service.impl;  
  
import com.baomidou.dynamic.datasource.annotation.DS;  
import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;  
import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;  
import com.interfaces.springbootinit.common.ErrorCode;  
import com.interfaces.springbootinit.exception.BusinessException;  
import com.interfaces.springbootinit.mapper.AppInfoMapper;  
import com.interfaces.springbootinit.model.dto.auth.AuthMessageDTO;  
import com.interfaces.springbootinit.model.entity.AppInfo;  
import com.interfaces.springbootinit.service.AppInfoService;  
import org.springframework.beans.BeanUtils;  
import org.springframework.stereotype.Service;  
import org.springframework.transaction.annotation.Transactional;  
import org.springframework.util.DigestUtils;  
  
import javax.annotation.Resource;  
import java.util.UUID;  
  
/**  
* @author 114514191980  
* @description 针对表【T_APP_INFO(应用信息)】的数据库操作Service实现  
* @createDate 2024-09-27 08:45:10  
*/  
@Service  
public class AppInfoServiceImpl extends ServiceImpl<AppInfoMapper, AppInfo>  
    implements AppInfoService {  
  
    /**  
     * 注册接口  
     * @param authMessageDTO  
     * @return  
     */  
  
    @Resource  
    private AppInfoMapper appInfoMapper;  
  
    private static final String SALT = "SYSTEM";  
  
    @Transactional(rollbackFor = Exception.class)  
    @Override  
    public String authRegister(AuthMessageDTO authMessageDTO) {  
        String appSecret = UUID.randomUUID().toString();  
        String id = UUID.randomUUID().toString();  
        AppInfo appInfo = new AppInfo();  
        BeanUtils.copyProperties(authMessageDTO,appInfo);  
        String secret = DigestUtils.md5DigestAsHex((appSecret + SALT).getBytes());  
        appInfo.setId(id);  
        // 加密的数据存到数据库  
        appInfo.setAppSecret(secret);  
        boolean save = save(appInfo);  
        if (!save){  
            throw new BusinessException(ErrorCode.SYSTEM_ERROR);  
        }  
        // 没有加密的返回给服务器端  
        // appInfo.setAppSecret(appSecret);  
        return appSecret;  
    }  
  
    @Override  
    public boolean isValidAppSecret(String appSecret) {  
        appSecret = appSecret + SALT;  
        QueryWrapper<AppInfo> queryWrapper = new QueryWrapper<>();  
        queryWrapper.eq("APP_SECRET",DigestUtils.md5DigestAsHex(appSecret.getBytes()));  
        // 表示启用  
        queryWrapper.eq("STATUS",0);  
        // 表示未删除  
        queryWrapper.eq("DEL_FLAG",0);  
        Long count = appInfoMapper.selectCount(queryWrapper);  
        return count > 0;  
    }  
}
```



### 推送的xml
``` xml
<select id="selectWithCombineTable" resultType="com.interfaces.springbootinit.model.dto.construction.ConstructionFeedbackCsmDTO">  
    SELECT  
    f.CONSTRUCTPLAN_ID AS id,    f.CONSTLEADER_NAME AS workerName,    f.PLAN_STATUS AS status,    f.EMA_WORKNO AS workOrderNum,    t.WORK_NAME AS workOrderDesc,    f.PLANBEGIN_TIME AS planStartTime,    f.PLANEND_TIME AS planEndTime,    f.PLAN_TYPE AS planType,    s.BEGINSTATION_CODE AS location,    s.BEGINAPPROVE_TIME AS actualStartTime,    s.ENDAPPROVE_TIME AS actualEndTime,    t.VEHICLE AS trainNo,    f.CONSTLEADER_ID AS workerNum,    m.STATION_NAME AS terrtioriality    FROM Csm_Constructplan f    LEFT JOIN Csm_Workplaceapply s ON f.CONSTRUCTPLAN_ID = s.CONSTRUCTPLAN_ID    LEFT JOIN (    SELECT t.CONSTRUCTPLAN_ID, t.WORK_NAME, t.VEHICLE,    ROW_NUMBER() OVER (PARTITION BY t.CONSTRUCTPLAN_ID ORDER BY t.SAFEGUARD DESC) AS rn    FROM CSM_WORKCONTENT t    ) t ON f.CONSTRUCTPLAN_ID = t.CONSTRUCTPLAN_ID AND t.rn = 1    LEFT JOIN MST_STATION m ON s.BEGINSTATION_CODE = m.STATION_CODE    <where>  
        <!-- 动态查询条件 -->  
        <if test="query.planBeginDate != null">  
            f.PLANBEGIN_TIME >= #{query.planBeginDate}  
        </if>  
        <if test="query.planEndDate != null">  
            AND f.PLANEND_TIME &lt;= #{query.planEndDate}  
        </if>  
        <if test="query.location != null and query.location != ''">  
            AND s.BEGINSTATION_CODE = #{query.location}  
        </if>  
        <if test="query.planType != null and query.planType != ''">  
            AND s.PLAN_TYPE = #{query.planType}  
        </if>  
        <if test="query.trainNo != null and query.trainNo != ''">  
            AND t.VEHICLE = #{query.trainNo}  
        </if>  
        <if test="query.workOrderNum != null and query.workOrderNum != ''">  
            AND f.EMA_WORKNO = #{query.workOrderNum}  
        </if>  
        <if test="query.actualStartTime != null">  
            AND s.BEGINAPPROVE_TIME >= #{query.actualStartTime}  
        </if>  
        <if test="query.actualEndTime != null">  
            AND s.ENDAPPROVE_TIME &lt;= #{query.actualEndTime}  
        </if>  
    </where>  
</select>
```