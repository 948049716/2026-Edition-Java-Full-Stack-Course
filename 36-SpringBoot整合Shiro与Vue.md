# Spring Boot

## Spring Boot 整合 Shiro

通过过滤器和拦截器去拦截用户的各种请求，然后进行各种验证，登录、权限验证。

Shiro 的核心组件

- Token：Token 中存储用户登录信息，用户在登录时会创建 Token，Shiro 通过 Token 来判断当前用户的身份和权限。
- Subject：存储当前的用户信息，不仅可以指用户，还可以指第三方进程、后台账户或者其他正在交互的。
- SecurityManager：Shiro 的核心，Shiro 通过 SecurityManager 来管理内部的组件实例，实现安全认证和授权。
- Realm：Shiro 用 Realm 来实现数据交互，当对用户执行认证和授权验证时，Shiro 从 Realm 中查找用户信息和权限信息。

1、pom.xml

```xml
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-spring</artifactId>
    <version>1.8.0</version>
</dependency>
```

2、定义 Shiro 过滤器

```java
package com.southwind.mapper;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.southwind.entity.Account;

public interface AccountMapper extends BaseMapper<Account> {
}
```

```java
package com.southwind.service;

import com.southwind.entity.Account;

public interface AccountService {
    public Account findByUsername(String username);
}
```

```java
package com.southwind.service.impl;

import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
import com.southwind.entity.Account;
import com.southwind.mapper.AccountMapper;
import com.southwind.service.AccountService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class AccountServiceImpl implements AccountService {

    @Autowired
    private AccountMapper accountMapper;

    @Override
    public Account findByUsername(String username) {
        QueryWrapper queryWrapper = new QueryWrapper();
        queryWrapper.eq("username", username);
        return this.accountMapper.selectOne(queryWrapper);
    }
}
```

```java
package com.southwind.realm;

import com.southwind.entity.Account;
import com.southwind.service.AccountService;
import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.*;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.authz.SimpleAuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;
import org.apache.shiro.subject.Subject;
import org.springframework.beans.factory.annotation.Autowired;

import java.util.HashSet;
import java.util.Set;

public class AccountRealm extends AuthorizingRealm {

    @Autowired
    private AccountService accountService;

    @Override

    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        //获取当前登录对象
        Subject subject = SecurityUtils.getSubject();
        Account account = (Account) subject.getPrincipal();

        //设置角色
        Set<String> roles = new HashSet<>();
        roles.add(account.getRole());
        SimpleAuthorizationInfo info = new SimpleAuthorizationInfo(roles);

        //设置权限
        info.addStringPermission(account.getPerms());
        return info;
    }

    /**
     * 认证
     * @param authenticationToken
     * @return
     * @throws AuthenticationException
     */
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        UsernamePasswordToken token = (UsernamePasswordToken) authenticationToken;
        Account account = accountService.findByUsername(token.getUsername());
        if(account != null){
            //验证密码
            return new SimpleAuthenticationInfo(account, account.getPassword(),getName());
        }
        return null;
    }
}
```

3、创建 ShiroConfiguration

```java
package com.southwind.configuration;

import com.southwind.realm.AccountRealm;
import org.apache.shiro.spring.web.ShiroFilterFactoryBean;
import org.apache.shiro.web.mgt.DefaultWebSecurityManager;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class ShiroConfiguration {

    @Bean
    public ShiroFilterFactoryBean shiroFilterFactoryBean(
            @Qualifier("securityManager") DefaultWebSecurityManager securityManager
    ){
        ShiroFilterFactoryBean factoryBean = new ShiroFilterFactoryBean();
        factoryBean.setSecurityManager(securityManager);
        return factoryBean;
    }

    @Bean
    public DefaultWebSecurityManager securityManager(
            @Qualifier("accountRealm") AccountRealm accountRealm
    ){
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        securityManager.setRealm(accountRealm);
        return securityManager;
    }
    
    @Bean
    public AccountRealm accountRealm(){
        return new AccountRealm();
    }

}
```

Shiro 认证授权规则：

认证过滤器：

- anon：无需认证即可访问
- authc：必须登录才能访问
- authcBasic：需要通过 httpBasic 认证
- user：不一定已通过认证，只要曾经被 Shiro 记住过登录状态的用户可以正常发起请求

授权过滤器：

- perms：必须拥有权限才能访问
- role：必须拥有角色才能访问
- port：请求的端口必须为指定值才可访问
- res：请求必须是基于 RESTful 的才可访问
- ssl：必须是安全的 URL 请求，协议为 HTTPS

login.html

main.html、manage.html、administrator.html

要求：

1、必须是登录状态才能访问 main.html

2、用户必须拥有 manage 权限才能访问 manage.html

3、用户必须拥有 administrator 角色才能访问 aministrator.html

修改配置类

```java
@Bean
public ShiroFilterFactoryBean shiroFilterFactoryBean(
        @Qualifier("securityManager") DefaultWebSecurityManager securityManager
){
    ShiroFilterFactoryBean factoryBean = new ShiroFilterFactoryBean();
    factoryBean.setSecurityManager(securityManager);
    //权限设置
    Map<String,String> map = new HashMap<>();
    map.put("/main", "authc");
    map.put("/manage", "perms[manage]");
    map.put("/administrator","roles[administrator]");
    factoryBean.setFilterChainDefinitionMap(map);
    //设置登录页面
    factoryBean.setLoginUrl("/login");
    //设置未授权页面
    factoryBean.setUnauthorizedUrl("/unauth");
    return factoryBean;
}
```

zs 无权限 无角色

ls manage权限 无角色

ww manage权限 administrator角色

## Spring Boot + Vue

前后端分离开发

由原来的单体架构变为前后端分离的架构

前端一个服务：负责页面展示，用户交互，不需要进行业务逻辑处理，不需要访问数据库

后端一个服务：负责业务逻辑处理，访问数据库，不需要考虑页面展示，用户交互

前端开发工具：

HBuilder、VSCode、WebStorm、IDEA

Vue 本质上自动完成数据到视图的绑定

