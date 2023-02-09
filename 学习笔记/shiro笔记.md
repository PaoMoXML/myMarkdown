## shiro笔记

### 1. pom

```xml
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-spring</artifactId>
    <version>1.7.1</version>
</dependency>
```

### 2. 用到的表结构

- 用户信息表
```mysql
create table user
(
	id varchar(50) not null comment '用户主键'
		primary key,
	username varchar(20) null comment '用户名',
	password varchar(20) null comment '密码',
	role_id varchar(50) null comment '外键关联role表'
)
charset=utf8;

create index role_id
	on user (role_id);
```
- 用户角色表
```mysql
create table role
(
	id varchar(50) not null comment '主键'
		primary key,
	rolename varchar(20) null comment '角色名称'
)
charset=utf8;
```

- 角色权限表
```mysql
create table permission
(
	id varchar(50) not null comment '主键'
		primary key,
	permissionname varchar(50) null comment '权限名',
	role_id varchar(50) null comment '外键关联role'
)
charset=utf8;

create index role_id
	on permission (role_id);
```

   

### 3. 配置自定义的Realm

```java
package com.xml.filemanager.shiro;

import com.baomidou.mybatisplus.core.conditions.Wrapper;
import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
import com.xml.filemanager.dao.UserMapper;
import com.xml.filemanager.entity.User;
import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.AuthenticationInfo;
import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.authc.SimpleAuthenticationInfo;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.authz.SimpleAuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;

import javax.annotation.Resource;

/**
 * 自定义权限认证
 *
 * @author xml00
 */
public class MyRealm extends AuthorizingRealm
{

    @Resource
    private UserMapper userService;

    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        // 获取用户名
        String username = (String) principalCollection.getPrimaryPrincipal();
        SimpleAuthorizationInfo authorizationInfo = new SimpleAuthorizationInfo();
        // 给该用户设置角色，角色信息存在 t_role 表中取，结果为该用户的角色名称，如：admin，teacher...
        authorizationInfo.setRoles(userService.getRoleByUserName(username));
        // 给该用户设置权限，权限信息存在 t_permission 表中取
        authorizationInfo.setStringPermissions(userService.getPermissionsByuserName(username));
        return authorizationInfo;
    }

    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        // 根据 Token 获取用户名
        String username = (String) authenticationToken.getPrincipal();
        // 根据用户名从数据库中查询该用户
        Wrapper<User> condition = new QueryWrapper<User>().eq("username", username);
        //用户信息
        User user = userService.selectOne(condition);
        if (user != null) {
            // 把当前用户存到 Session 中
            SecurityUtils.getSubject().getSession().setAttribute("user", user);
            // 传入用户名和密码进行身份认证，并返回认证信息
            return new SimpleAuthenticationInfo(user.getUsername(), user.getPassword(), "myRealm");
        }
        else {
            return null;
        }
    }

}

```

### 4. 配置ShiroConfig

```java
package com.xml.filemanager.shiro;

import org.apache.shiro.mgt.SecurityManager;
import org.apache.shiro.spring.web.ShiroFilterFactoryBean;
import org.apache.shiro.web.mgt.DefaultWebSecurityManager;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.servlet.Filter;
import java.util.LinkedHashMap;
import java.util.Map;

@Configuration
public class ShiroConfig
{
    private static final Logger logger = LoggerFactory.getLogger(ShiroConfig.class);

    /**
     * 注入自定义的 Realm
     *
     * @return MyRealm
     */
    @Bean
    public MyRealm myAuthRealm() {
        MyRealm myRealm = new MyRealm();
        logger.info("============myRealm注册完成=============");
        return myRealm;
    }

    /**
     * 注入安全管理器
     *
     * @return SecurityManager
     */

    @Bean
    public SecurityManager securityManager() {
        // 将自定义 Realm 加进来
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager(myAuthRealm());
        logger.info("============securityManager注册完成============");
        return securityManager;
    }

    /**
     * 注入 Shiro 过滤器
     *
     * @param securityManager 安全管理器
     * @return ShiroFilterFactoryBean
     */
    @Bean
    public ShiroFilterFactoryBean shiroFilter(SecurityManager securityManager) {
        // 定义 shiroFactoryBean
        ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();
        //获取filters
        Map<String, Filter> filters = shiroFilterFactoryBean.getFilters();
        //将自定义 的FormAuthenticationFilter注入shiroFilter中
        filters.put("authc", new MyFormAuthenticationFilter());

        // 设置自定义的 securityManager
        shiroFilterFactoryBean.setSecurityManager(securityManager);

        // 设置默认登录的 URL，身份认证失败会访问该 URL
        shiroFilterFactoryBean.setLoginUrl("/login.html");

        // 设置成功之后要跳转的链接 不建议设置该字段，设置这个字段不如直接在页面写死跳转
        //        shiroFilterFactoryBean.setSuccessUrl("/logincontroller/loginsuccess");

        // 设置未授权界面，权限认证失败会访问该 URL
        shiroFilterFactoryBean.setUnauthorizedUrl("/logout.html");

        // LinkedHashMap 是有序的，进行顺序拦截器配置
        Map<String, String> filterChainMap = new LinkedHashMap<>();

        // 配置可以匿名访问的地址，可以根据实际情况自己添加，放行一些静态资源等，anon 表示放行
        filterChainMap.put("/css/**", "anon");
        filterChainMap.put("/imgs/**", "anon");
        filterChainMap.put("/js/**", "anon");
        filterChainMap.put("/icon/**", "anon");

        // 登录 URL 放行
        filterChainMap.put("/logincontroller/login", "anon");

        filterChainMap.put("/filelistredertable", "roles[admin]");
        //        // 以“/user/admin” 开头的用户需要身份认证，authc 表示要进行身份认证
        //        filterChainMap.put("/user/admin*", "authc");
        //        // “/user/student” 开头的用户需要角色认证，是“admin”才允许
        //        filterChainMap.put("/user/student*/**", "roles[admin]");
        //        // “/user/teacher” 开头的用户需要权限认证，是“user:create”才允许
        //        filterChainMap.put("/user/teacher*/**", "perms[\"user:create\"]");

        // 配置 logout 过滤器
        filterChainMap.put("/logincontroller/logout", "logout");
        //开启Http的Basic认证
        filterChainMap.put("/test/**", "authcBasic");

        //这行代码必须放在所有权限设置的最后，不然会导致所有 url 都被拦截 剩余的都需要认证
        filterChainMap.put("/**", "authc");

        // 设置 shiroFilterFactoryBean 的 FilterChainDefinitionMap
        shiroFilterFactoryBean.setFilterChainDefinitionMap(filterChainMap);
        logger.info("============shiroFilterFactoryBean注册完成============");
        return shiroFilterFactoryBean;
    }

}

```
### 5. 登录方法

```java
    @PostMapping(value = "/login")
    @ResponseBody
    public String login(@RequestParam("username") String username, @RequestParam("password") String password) {
        // 从SecurityUtils里边创建一个 subject
        Subject subject = SecurityUtils.getSubject();
        // 在认证提交前准备 token（令牌）
        UsernamePasswordToken token = new UsernamePasswordToken(username, password);
        // 执行认证登陆
        try {
            subject.login(token);
        }
        catch (UnknownAccountException uae) {
            logger.error(uae.getMessage(), uae);
            return "未知账户";
        }
        catch (IncorrectCredentialsException ice) {
            logger.error(ice.getMessage(), ice);
            return "密码不正确";
        }
        catch (LockedAccountException lae) {
            logger.error(lae.getMessage(), lae);
            return "账户已锁定";
        }
        catch (ExcessiveAttemptsException eae) {
            logger.error(eae.getMessage(), eae);
            return "用户名或密码错误次数过多";
        }
        catch (AuthenticationException ae) {
            logger.error(ae.getMessage(), ae);
            return "用户名或密码不正确！";
        }
        if (subject.isAuthenticated()) {
            return "登录成功";
        }
        else {
            token.clear();
            return "登录失败";
        }
    }
```

### 6. 参考文章

1. [Spring Boot 中集成 Shiro](https://blog.csdn.net/taojin12/article/details/88343990)

2. [SpringBoot2.0集成Shiro](https://blog.csdn.net/bicheng4769/article/details/86668209)

3. [spring boot 集成shiro和redis](https://blog.csdn.net/u010514380/article/details/82185451)
