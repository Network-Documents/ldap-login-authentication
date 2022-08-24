# ldap-login-authentication
### راه اندازی یک ldap server و لاگ‌این کردن در آن با ماشین مجازی دیگر
ابتدا ابزار های زیر را نصب میکنیم
 ``` 
 sudo apt install slapd ldap-utils
 ```   
   سپس در صفحه باز شده رمز دلخواه را وارد کرده   
      
         
           
           

![image](https://user-images.githubusercontent.com/88885103/186495468-9ac0f27d-32d4-44a7-b334-85c74ae7a349.png)   
سپس یک 
*domain*
میسازیم تا ساختار دیتا رو وارد تشکیل دهیم

```
dpkg-reconfigure slapd 
```
 ![image](https://user-images.githubusercontent.com/88885103/186496920-2246db6a-876b-4c2a-ad6f-9c7671aba962.png)   
 بعد از وارد کردن
 *domain DNS*
 و 
 *organization name*
 برای مثال از 
 **example.org**
 استفاده شده   
 
 سپس فایل یوزر رو میسازیم

 
```
 nano user.ldif
```   
سپس اطلاعات زیر را در آن وارد میکنیم
```
dn: cn=ldapusers,ou=groups,dc=example,dc=org
objectClass: posixGroup
cn: ldapusers
gidNumber: 4000

dn: uid=mike,ou=people,dc=example,dc=org
objectClass: inetOrgPerson
objectClass: posixAccount
objectClass: shadowAccount
uid: mike
sn: smith
givenName: mike
cn: mike
uidNumebr: 4000
gidNumber: 4000
userPassword: mike123
loginShell: /bin/bashl
homeDirectory: /home/mike
```   
سپس فایل گروه رو میسازیم
سپس 
```
 nano group.ldif
```   
سپس اطلاعات زیر را در آن وارد میکنیم


```
dn: ou=people,dc=example,dc=org
objectClass: organizationalUnit
ou: people

dn: ou=groups,dc=example,dc=org
objectClass: organizationalUnit
ou: groups
```
 
*user.ldif*
و
*group.ldif*
رو به دیتابیس 
*ldap* 
اضافه میکنیم
```
ldapadd -x -D cn=admin,dc=example,dc=org -W -f group.ldif 
``` 
سپس در 
*client*
``` 
sudo apt install libnss-ldap libpam-ldap ldap-utils
```
در صفحه باز شده 
اطلاعات زیر رو وارد میکنیم

```
LDAP Sever Uniform Resource Identifier: 
ldap://ip-server/
Distinguished name of the search base: 
dc=example,dc=org
LDAP version: 3
Make local root database admin: yes
Does the LDAP database require login? no
LDAP account for root: 
cn=admin,dc=example,dc=org
```

‍‍سپس فایل زیر  را ادیت کرده 
```
vim /usr/share/pam-configs/mkhomedir
```
و اطلاعات آن را تغییر میدهیم

> Name: activate mkhomedir   
Default: yes    
Priority: 900    
Session-Type: Additional    
Session:     
  required pam_mkhomedir.so umask=0022 skel=/etc/skel     
  
  
سپس   
 

```
pam-auth-update
```   
![image](https://user-images.githubusercontent.com/88885103/186518515-d073e80a-6ff3-4048-8d8d-8081e00de96f.png)
  
  
  ```
  systemctl restart nscd.service
  ```  
  برای برسی اضافه شدن یوزر به دیتا بیس میتوان
  ```
  getent passwd  
 ```   
   برای لاگ‌این کردن در یوزر ساخته شده 
   
   
 ``` 
 su - [username]
``` 
  

