### CentOS安装支持Web界面浏览的svn版本控制

- 安装所需软件
  `yum install httpd subversion mod_dav_svn php -y`

- 修改httpd配置,支持svn
  `vim /etc/httpd/conf.d/subversion.conf`

```javascript
LoadModule dav_svn_module     modules/mod_dav_svn.so
LoadModule authz_svn_module   modules/mod_authz_svn.so
	
<Location /svn>
   DAV svn
   SVNParentPath /opt/svn
	
   # Limit write permission to list of valid users.
   <LimitExcept GET PROPFIND OPTIONS REPORT>
      # Require SSL connection for password protection.
      # SSLRequireSSL
	
      AuthType Basic
      AuthName "Authorization Realm"
      AuthBasicProvider file
      AuthUserFile /opt/svn/passwd.conf
      Require valid-user
      #SVNListParentPath on
   </LimitExcept>
</Location>
```
- 创建svn目录
	`mkdir /opt/svn/`
	`mkdir /var/www/html/svnadmin`
- 创建svn权限文件和密码文件
	`touch /opt/svn/authz.conf`
	`touch /opt/svn/passwd.conf`
- 安装iF.SVNAdmin
	- 下载 `wget https://sourceforge.net/projects/ifsvnadmin/files/svnadmin-1.6.2.zip`
	- 解压 `unzip svnadmin-1.6.2.zip`
	- 复制 `cp -r iF.SVNAdmin-stable-1.6.2/* /var/www/html/svnadmin`
	- 更改data权限 `chmod -R 777 /var/www/html/svnadmin/data`<br/>
	- 更改svnadmin权限	<br />`chown -R apache:apache /var/www/html/svnadmin/`<br />`chmod 777 /opt/svn/authz.conf`<br />`chmod 777 /opt/svn/passwd.conf`
- 启动服务
	`service httpd start`
	`svnserve -d -r /opt/svn`
- 浏览器访问
	[http://server.ip/svnadmin](http://localhost/svnadmin) 默认进入登录界面,帐号密码都是admin
	
	> [如果服务器不能访问,则有可能是防火墙设置](CentOS防火墙之iptables.md)