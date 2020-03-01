# Windows2008-R2-standard---Active-Directory-LDAP
Windows2008 R2 standard - Active Directory(LDAP) 搭建
什么是LDAP？
LDAP（Light Directory Access Portocol）是一种协议，是以树形结构来展示账户信息的一种方式。
通俗的说，就是不需要用关系型数据库自己建表的方式来管理企业各部门的用户信息。
**结构：**
|公司  | 组织 |员工|DN值 |
|--|--|--|--|
| abcdefg |  | |DC=abcdefg,DC=local|
|  | 财务部 | |OU=财务部,DC=abcdefg,DC=local |
|  |  | zhangsan|CN=zhangsan,OU=财务部,DC=abcdefg,DC=local|
|  |  | lisi|CN=lisi,OU=财务部,DC=abcdefg,DC=local|
|  | IT部 | |OU=IT部,DC=abcdefg,DC=local |
|  |  | wangyi|CN=wangyi,OU=IT部,DC=abcdefg,DC=local|
|  |  | wanger|CN=wanger,OU=IT部,DC=abcdefg,DC=local|
|  |子公司 | |DC=sub_company,DC=abcdefg,DC=local|
|  |... | |...|
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301160709247.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)

**属性：**
注:可自定义属性
|属性名称|属性值|含义|
|--|--|--|
| DC | DC=abcdefg,DC=local |域名|
| OU |OU=财务部,DC=abcdefg,DC=local  |部门或者组织名称|
| DN(distinguishedName) | CN=itadmin,CN=Users,DC=abcdefg,DC=local |用户或者组或者组织或者域在树中的绝对位置标记，**唯一值**|
| CN |CN=itadmin  |用户或者组的名称|
| userPrincipalName|userPrincipalName=itadmin@abcdefg.local  |邮件地址，唯一值|
例如：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301154708440.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
**使用场景**

 1. 与微软的office套件OSS登录，例如exchange邮件服务器，office365服务器，当前计算机在域的树下时，访问邮件，可以直接免密登录。
 2. 总公司与子公司的员工管理，域可以挂子域，将子公司的节点添加到总公司节点下，即可以直接查询任意员工的电话及邮件地址。
 3. 第三方软件的登录，大型OA系统及各种管理软件大多支持LDAP认证，即提供一套API供子系统使用，添加检索范围，即可实现快速登录，不需要再开发一套登录系统。

# Windows2008R2 standard 安装 AD 服务器
## 1.设定固定IP及DNS服务器地址
1.设定固定IP
2.DNS服务器地址设置为本机
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301105455765.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
## 2 防火墙关闭
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020030111133823.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
## 3 修改network的bios名称
1.修改计算机在网络上的名称
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020030111171010.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301111833429.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301111944174.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
2.保存设置，重启计算机
## 4 安装DNS服务
1.在服务器管理器中打开角色面板
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301112456605.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
2.添加角色
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301112557998.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
3.安装DNS服务器
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301112654651.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301112824588.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301112910766.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301113333261.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301113450873.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
## 5 安装Active Directory Lightwight server
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301113618814.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301113658484.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301113839685.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301113948792.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301114021814.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301114102274.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301114133386.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301114332666.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
## 6 创建域服务
**注：这里使用的测试公司名称为abcdefg，公司网络域名为abcdefg.local**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301114521850.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301114833931.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301114904996.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301115012851.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301115129802.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301115243474.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301115332852.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301115402956.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301115458182.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301115526183.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301115640810.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301115711407.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301115743864.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301115829202.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301115902968.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
## 7 更新组策略
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301150211340.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301150413746.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301150459735.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301150626157.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301150957212.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
## 8 添加新用户及提升用户至管理员权限
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301151213912.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
### 1.创建用户：![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301151655345.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301151853983.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301151430644.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301152008724.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301152043285.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301152104802.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
### 2 提升用户至管理员权限
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301151316178.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301152239929.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301152346737.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301152534675.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301152733987.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301152803407.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301152914461.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301153045312.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
## 9 使用 Apache Directory Studio 连接
Apache Directory Studio是Apache的LDAP的UI开发工具，可以对域上的任意节点进行增删改查。

### 1.下载
[Apache Directory Studio下载地址](http://directory.apache.org/studio/downloads.html)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301110101587.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
### 2.连接AD域服务器
1.查询itadmin用户的DN值
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301153415541.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301153533860.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020030115365069.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301153802772.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301154004988.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301154142225.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301154257516.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
## 10 OSS登录过程
LDAP的认证是通过DN加用户密码来进行登录认证的。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200301161550444.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
准备财务部门的zhangsan用户（CN=zhangsan,OU=财务部,DC=abcdefg,DC=local），并且设定密码为1
登录逻辑实现：
Step1：用户输入用户名zhangsan，密码为1进行第三方系统登录
Step2: 
-> 
Step3:
|STEP|内容|返回值|
|--|--|--|
| 1 | 用户输入用户名zhangsan，密码为1进行第三方系统登录 |连接到LDAP认证相关API|
| 2 | 使用默认管理员itadmin的DN及密码进行LDAP登录，在设定指定范围内（例如整个DC（DC=abcdefg,DC=local））进行检索sAMAccountName属性为zhangsan的用户 |返回zhangsan用的DN值（CN=zhangsan,OU=财务部,DC=abcdefg,DC=local）|
| 3| LDAP认证相关API，使用zhangsan的DN值（CN=zhangsan,OU=财务部,DC=abcdefg,DC=local）加张三的密码进行LDAP服务器登录 |登录成功，查看相关权限，跳转到指定权限画面|
