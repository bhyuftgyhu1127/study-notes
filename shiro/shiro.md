

# shiro

```java
package com.how2java.test;/**
 * @author zhangwenjie
 * @date 2021/03/23
 */

import com.how2java.User;
import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.config.IniSecurityManagerFactory;
import org.apache.shiro.mgt.SecurityManager;
import org.apache.shiro.subject.Subject;
import org.apache.shiro.util.Factory;

import java.util.ArrayList;
import java.util.List;

/**
 * @program: baoge
 * @description: 权限
 * @author: Mr.zhangwenjie
 * @create: 2021-03-23 17:10
 **/
public class TestShiro {

    public static void main(String[] args) {
        User zhangsan1 = new User();

        zhangsan1.setName("zhang3");
        zhangsan1.setPassword("12345");

        User lisi2 = new User();

        lisi2.setName("li4");
        lisi2.setPassword("abcde");

        User wangwu3 = new User();

        wangwu3.setName("wangwu5");
        wangwu3.setPassword("123456");

        List<User> users = new ArrayList<User>(3);
        users.add(zhangsan1);
        users.add(lisi2);
        users.add(wangwu3);

        // 角色
        String roleAmin = "admin";
        String userAmin = "productManager";

        ArrayList<String> roles = new ArrayList<>();
        roles.add(roleAmin);
        roles.add(userAmin);

        //权限们
        String permitAddProduct = "addProduct";
        String permitAddOrder = "addOrder";

        List<String> permits = new ArrayList<>();
        permits.add(permitAddProduct);
        permits.add(permitAddOrder);

        for (User user : users) {
            if (login(user)) {
                    System.out.println("登录成功" + user.getName() + "密码:" + user.getPassword());
            } else {
                System.out.println("登录失败" + user.getName() + "密码:" + user.getPassword());
            }
        }

        System.out.println("-------how2j 分割线------");


        //判断能够登录的用户是否拥有某个角色
        for (User user : users) {
            for (String role : roles) {
                if (login(user)){
                    if (hasRole(user,role)){
                        System.out.printf("%s\t 拥有角色: %s\t%n",user.getName(),role);
                    }else {
                        System.out.printf("%s\t 不拥有角色: %s\t%n", user.getName(), role);
                    }
                }
            }
        }

        System.out.println("-------how2j 分割线------");

        //判断能够登录的用户，是否拥有某种权限

        for (User user : users) {
            for (String quanxian : permits) {
                if (login(user)) {
                    if (quanxians(user,quanxian)){
                        System.out.printf("%s\t 拥有权限: %s\t%n",user.getName(),quanxian);
                    } else {
                        System.out.printf("%s\t 不拥有权限: %s\t%n",user.getName(),quanxian);
                    }
                }
            }
        }



    }

    private static boolean quanxians(User user, String quanxian) {
        Subject subject = getSubject(user);
        boolean permittedAll = subject.isPermittedAll(quanxian);
        return permittedAll;
    }

    private static boolean hasRole(User user, String role) {
        Subject subject = getSubject(user);
        boolean hasRole = subject.hasRole(role);
        return hasRole;
    }

    private static boolean login(User user) {
        Subject subject = getSubject(user);
        //如果已经登录过了，退出
        if (subject.isAuthenticated()) {
            subject.logout();
        }

        UsernamePasswordToken token = new UsernamePasswordToken(user.getName(), user.getPassword());
        try {
            subject.login(token);
        } catch (AuthenticationException e) {
            return false;
        }
        return subject.isAuthenticated();
    }

    private static Subject getSubject(User user) {
        //加载配置文件，并获取工厂
        Factory<SecurityManager> factory = new IniSecurityManagerFactory("classpath:shiro.ini");
        //获取安全管理者实例
        SecurityManager instance = factory.getInstance();
        //将安全管理者放入全局对象
        SecurityUtils.setSecurityManager(instance);
        //全局对象通过安全管理者生成Subject对象
        Subject subject = SecurityUtils.getSubject();

        return subject;
    }
}

```

```ini
#定义用户
[users]
#用户名 zhang3  密码是 12345， 角色是 admin
zhang3 = 12345, admin 
#用户名 li4  密码是 abcde， 角色是 产品经理
li4 = abcde,productManager
#定义角色
[roles]
#管理员什么都能做
admin = *
#产品经理只能做产品管理
productManager = addProduct,deleteProduct,editProduct,updateProduct,listProduct
#订单经理只能做产品管理
orderManager = addOrder,deleteOrder,editOrder,updateOrder,listOrder
```





## 数据库Shiro

创建数据库表

```sql
DROP DATABASE IF EXISTS shiro;
CREATE DATABASE shiro DEFAULT CHARACTER SET utf8;
USE shiro;

drop table if exists user;
drop table if exists role;
drop table if exists permission;
drop table if exists user_role;
drop table if exists role_permission;

create table user (
  id bigint auto_increment,
  name varchar(100),
  password varchar(100),
  constraint pk_users primary key(id)
) charset=utf8 ENGINE=InnoDB;

create table role (
  id bigint auto_increment,
  name varchar(100),
  constraint pk_roles primary key(id)
) charset=utf8 ENGINE=InnoDB;

create table permission (
  id bigint auto_increment,
  name varchar(100),
  constraint pk_permissions primary key(id)
) charset=utf8 ENGINE=InnoDB;

create table user_role (
  uid bigint,
  rid bigint,
  constraint pk_users_roles primary key(uid, rid)
) charset=utf8 ENGINE=InnoDB;

create table role_permission (
  rid bigint,
  pid bigint,
  constraint pk_roles_permissions primary key(rid, pid)
) charset=utf8 ENGINE=InnoDB;

```

插入数据

```sql
INSERT INTO `permission` VALUES (1,'addProduct');
INSERT INTO `permission` VALUES (2,'deleteProduct');
INSERT INTO `permission` VALUES (3,'editProduct');
INSERT INTO `permission` VALUES (4,'updateProduct');
INSERT INTO `permission` VALUES (5,'listProduct');
INSERT INTO `permission` VALUES (6,'addOrder');
INSERT INTO `permission` VALUES (7,'deleteOrder');
INSERT INTO `permission` VALUES (8,'editOrder');
INSERT INTO `permission` VALUES (9,'updateOrder');
INSERT INTO `permission` VALUES (10,'listOrder');
INSERT INTO `role` VALUES (1,'admin');
INSERT INTO `role` VALUES (2,'productManager');
INSERT INTO `role` VALUES (3,'orderManager');
INSERT INTO `role_permission` VALUES (1,1);
INSERT INTO `role_permission` VALUES (1,2);
INSERT INTO `role_permission` VALUES (1,3);
INSERT INTO `role_permission` VALUES (1,4);
INSERT INTO `role_permission` VALUES (1,5);
INSERT INTO `role_permission` VALUES (1,6);
INSERT INTO `role_permission` VALUES (1,7);
INSERT INTO `role_permission` VALUES (1,8);
INSERT INTO `role_permission` VALUES (1,9);
INSERT INTO `role_permission` VALUES (1,10);
INSERT INTO `role_permission` VALUES (2,1);
INSERT INTO `role_permission` VALUES (2,2);
INSERT INTO `role_permission` VALUES (2,3);
INSERT INTO `role_permission` VALUES (2,4);
INSERT INTO `role_permission` VALUES (2,5);
INSERT INTO `role_permission` VALUES (3,6);
INSERT INTO `role_permission` VALUES (3,7);
INSERT INTO `role_permission` VALUES (3,8);
INSERT INTO `role_permission` VALUES (3,9);
INSERT INTO `role_permission` VALUES (3,10);
INSERT INTO `user` VALUES (1,'zhang3','12345');
INSERT INTO `user` VALUES (2,'li4','abcde');
INSERT INTO `user_role` VALUES (1,1);
INSERT INTO `user_role` VALUES (2,2);

```

代码：

```
package com.cslc;/**
 * @author zhangwenjie
 * @date 2021/03/24
 */

import com.how2java.DAO;

import java.sql.*;
import java.util.HashSet;
import java.util.Set;

/**
 * @program: baoge
 * @description: 数据库
 * @author: Mr.zhangwenjie
 * @create: 2021-03-24 10:05
 **/
public class Dao {

    public Dao() {
        try {
            Class.forName("com.mysql.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }

    public Connection getConnection() throws SQLException {
        return DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/shiro?characterEncoding=UTF-8", "root",
                "root");
    }
    public String getPassword(String userName) {
        String sql = "select password from user where name = ?";
        try (Connection c = getConnection(); PreparedStatement ps = c.prepareStatement(sql);) {

            ps.setString(1, userName);

            ResultSet rs = ps.executeQuery();

            if (rs.next())
                return rs.getString("password");

        } catch (SQLException e) {

            e.printStackTrace();
        }
        return null;
    }

    public Set<String> listRoles(String userName) {

        Set<String> roles = new HashSet<>();
        String sql = "select r.name from user u "
                + "left join user_role ur on u.id = ur.uid "
                + "left join Role r on r.id = ur.rid "
                + "where u.name = ?";
        try (Connection c = getConnection(); PreparedStatement ps = c.prepareStatement(sql);) {
            ps.setString(1, userName);
            ResultSet rs = ps.executeQuery();

            while (rs.next()) {
                roles.add(rs.getString(1));
            }

        } catch (SQLException e) {

            e.printStackTrace();
        }
        return roles;
    }
    public Set<String> listPermissions(String userName) {
        Set<String> permissions = new HashSet<>();
        String sql =
                "select p.name from user u "+
                        "left join user_role ru on u.id = ru.uid "+
                        "left join role r on r.id = ru.rid "+
                        "left join role_permission rp on r.id = rp.rid "+
                        "left join permission p on p.id = rp.pid "+
                        "where u.name =?";

        try (Connection c = getConnection(); PreparedStatement ps = c.prepareStatement(sql);) {

            ps.setString(1, userName);

            ResultSet rs = ps.executeQuery();

            while (rs.next()) {
                permissions.add(rs.getString(1));
            }

        } catch (SQLException e) {

            e.printStackTrace();
        }
        return permissions;
    }
    public static void main(String[] args) {
        System.out.println(new DAO().listRoles("zhang3"));
        System.out.println(new DAO().listRoles("li4"));
        System.out.println(new DAO().listPermissions("zhang3"));
        System.out.println(new DAO().listPermissions("li4"));
    }


}

```

```java
package com.cslc;

public class User {

	private int id;
	private String name;
	private String password;
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public String getPassword() {
		return password;
	}
	public void setPassword(String password) {
		this.password = password;
	}
	public int getId() {
		return id;
	}
	public void setId(int id) {
		this.id = id;
	}
	
	
}

```

```java
package com.cslc;/**
 * @author zhangwenjie
 * @date 2021/03/24
 */

import org.apache.shiro.authc.*;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.authz.SimpleAuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;
import sun.security.util.Password;

import java.util.Set;

/**
 * @program: baoge
 * @description: 权限
 * @author: Mr.zhangwenjie
 * @create: 2021-03-24 10:09
 **/
public class DatabaseRealm extends AuthorizingRealm {


    /**
     * 授权
     *
     * @param principal
     * @return
     */
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principal) {
        //能进入到这里，表示账号已经通过验证了
        String username = (String) principal.getPrimaryPrincipal();
        //通过DAO获取角色和权限
        Set<String> permission = new Dao().listPermissions(username);
        Set<String> role = new Dao().listRoles(username);

        //授权对象
        SimpleAuthorizationInfo a = new SimpleAuthorizationInfo();
        //把通过DAO获取到的角色和权限放进去
        a.setRoles(role);
        a.setStringPermissions(permission);
        return a;
    }

    /**
     * 获取身份验证信息
     * @param token
     * @return
     * @throws AuthenticationException
     */
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        //获取账号密码
        UsernamePasswordToken usernamePasswordToken = (UsernamePasswordToken) token;
        String username = token.getPrincipal().toString();
        String password = new String(usernamePasswordToken.getPassword());
        //获取数据库中的密码
        String passwordDB = new Dao().getPassword(username);

        //如果为空就是账号不存在，如果不相同就是密码错误，但是都抛出AuthenticationException，而不是抛出具体错误原因，免得给破解者提供帮助信息
        if (null == passwordDB || passwordDB.equals(password)){
            throw new AuthenticationException();
        }

        //认证信息里存放账号密码, getName() 是当前Realm的继承方法,通常返回当前类名 :databaseRealm
        SimpleAuthenticationInfo authenticationInfo = new SimpleAuthenticationInfo(username, password, getName());
        return authenticationInfo;
    }
}

```

```java
package com.how2java;


import java.util.ArrayList;
import java.util.List;

import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.config.IniSecurityManagerFactory;
import org.apache.shiro.mgt.SecurityManager;
import org.apache.shiro.subject.Subject;
import org.apache.shiro.util.Factory;

public class TestShiro1 {
    public static void main(String[] args) {
        //用户们
        User zhang3 = new User();
        zhang3.setName("zhang3");
        zhang3.setPassword("12345");

        User li4 = new User();
        li4.setName("li4");
        li4.setPassword("abcde");


        User wang5 = new User();
        wang5.setName("wang5");
        wang5.setPassword("wrongpassword");

        List<User> users = new ArrayList<>();

        users.add(zhang3);
        users.add(li4);
        users.add(wang5);
        //角色们
        String roleAdmin = "admin";
        String roleProductManager ="productManager";

        List<String> roles = new ArrayList<>();
        roles.add(roleAdmin);
        roles.add(roleProductManager);

        //权限们
        String permitAddProduct = "addProduct";
        String permitAddOrder = "addOrder";

        List<String> permits = new ArrayList<>();
        permits.add(permitAddProduct);
        permits.add(permitAddOrder);






        //登陆每个用户
        for (User user : users) {
            if(login(user))
                System.out.printf("%s \t成功登陆，用的密码是 %s\t %n",user.getName(),user.getPassword());
            else
                System.out.printf("%s \t成功失败，用的密码是 %s\t %n",user.getName(),user.getPassword());
        }


        System.out.println("-------how2j 分割线------");

        //判断能够登录的用户是否拥有某个角色
        for (User user : users) {
            for (String role : roles) {
                if(login(user)) {
                    if(hasRole(user, role))
                        System.out.printf("%s\t 拥有角色: %s\t%n",user.getName(),role);
                    else
                        System.out.printf("%s\t 不拥有角色: %s\t%n",user.getName(),role);
                }
            }
        }
        System.out.println("-------how2j 分割线------");

        //判断能够登录的用户，是否拥有某种权限
        for (User user : users) {
            for (String permit : permits) {
                if(login(user)) {
                    if(isPermitted(user, permit))
                        System.out.printf("%s\t 拥有权限: %s\t%n",user.getName(),permit);
                    else
                        System.out.printf("%s\t 不拥有权限: %s\t%n",user.getName(),permit);
                }
            }
        }
    }

    private static boolean hasRole(User user, String role) {
        Subject subject = getSubject(user);
        return subject.hasRole(role);
    }

    private static boolean isPermitted(User user, String permit) {
        Subject subject = getSubject(user);
        return subject.isPermitted(permit);
    }

    private static Subject getSubject(User user) {
        //加载配置文件，并获取工厂
        Factory<SecurityManager> factory = new IniSecurityManagerFactory("classpath:shiro.ini");
        //获取安全管理者实例
        SecurityManager sm = factory.getInstance();
        //将安全管理者放入全局对象
        SecurityUtils.setSecurityManager(sm);
        //全局对象通过安全管理者生成Subject对象
        Subject subject = SecurityUtils.getSubject();


        return subject;
    }


    private static boolean login(User user) {
        Subject subject= getSubject(user);
        //如果已经登录过了，退出
        if(subject.isAuthenticated())
            subject.logout();

        //封装用户的数据
        UsernamePasswordToken token = new UsernamePasswordToken(user.getName(), user.getPassword());
        try {
            //将用户的数据token 最终传递到Realm中进行对比
            subject.login(token);
        } catch (AuthenticationException e) {
            //验证错误
            return false;
        }


        return subject.isAuthenticated();
    }




}

```

```ini
[main]
databaseRealm=com.cslc.DatabaseRealm
securityManager.realms=$databaseRealm
```

