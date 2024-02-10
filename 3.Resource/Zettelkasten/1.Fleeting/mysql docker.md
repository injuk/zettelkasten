```bash
docker run -d --platform linux/amd64 -p 33306:3306 -e MYSQL_ROOT_PASSWORD=mysql-local-pw -e TZ=Asia/Seoul --name mysql-local -v /Users/injihong/workspace/study:/var/lib/mysql --restart always mysql:5.7
```

```yml
/var/lib/mysql
```


/threads?nextToken=aaa&domain=sss > query parameter


/thread/{id}/comment > path variable