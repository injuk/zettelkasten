#git

- 브랜치 `a`에서 `development`로 리베이스하여 브랜치를 일자로 관리하고자 하는 경우, 아래와 같이 입력
```bash
# 현재 브랜치가 a라고 가정
git rebase development
git checkout development
git merge a
```