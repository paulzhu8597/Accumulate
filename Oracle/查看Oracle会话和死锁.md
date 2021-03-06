参考文章：http://zyjustin9.iteye.com/blog/2165797  
1. 查看当前总会话数和进程数
```SQL
    select count(*) from v$session;   
    select count(*) from v$process; 
```

2. 查询那些应用的连接数此时是多少
```SQL
    select b.MACHINE, b.PROGRAM, count(*)  
      from v$process a, v$session b  
     where a.ADDR = b.PADDR  
       and b.USERNAME is not null  
     group by b.MACHINE, b.PROGRAM  
     order by count(*) desc; 
```
 
3. 查询是否有死锁：
```SQL
    select * from v$locked_object; 
```

4. 查找死锁
```SQL
    select * from v$locked_object;  
```
    
5. 以Oralce管理员权限用户登录Oracle数据，查询到被锁的对象
```SQL
    SELECT a.object_id, a.session_id, b.object_name, c.*  
      FROM v$locked_object a, dba_objects b, v$session c  
     WHERE a.object_id = b.object_id  
       AND a.SESSION_ID = c.sid(+);
 ```
       
6. kill session
```SQL
    alter system kill session 'sid, serial#';  
    #例如：查出来的一条记录的sid是149, serial#为4153,就执行以下的语句：
        alter system kill session '149,4153' ;
    #如果要kill很多session可以用下面这条语句生成SQL：
    SELECT 'alter system kill session ''' || c.sid || '' || ',' || c.serial# ||''';'
	FROM v$locked_object a, dba_objects b, v$session c
 	WHERE a.object_id = b.object_id AND a.SESSION_ID = c.sid(+) ORDER BY logon_time;
```  
