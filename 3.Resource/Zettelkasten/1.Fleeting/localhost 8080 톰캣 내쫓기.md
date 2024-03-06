스프링 부트로 프로젝트 만들 때마다 localhost:8080이 이미 점유되어 있는 걸로 뜸...
`lsof -i :8080`으로 나오는 친구들을 `kill -9 [PID]`로 전부 지워도 좀비처럼 부활한다.

브라우저로 접속해보니 톰캣이 뜨는게, 나도 모르게 깔아뒀던 적이 있는 것 같아서 아래와 같이 제거해주었다.
```bash
[~] brew remove tomcat
Uninstalling /opt/homebrew/Cellar/tomcat/10.1.12... (1,003 files, 17.2MB)

Warning: The following tomcat configuration files have not been removed!
If desired, remove them manually with `rm -rf`:
  /opt/homebrew/etc/tomcat
  # ...생략
[~] # 이제 다시 lsof로 찾아 kill 해주면 부활하지 않는다!  
```