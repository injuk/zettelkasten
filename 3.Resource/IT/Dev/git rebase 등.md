#git
#### git rebase 관련
[요 글](https://wonyong-jang.github.io/git/2021/02/05/Github-Rebase.html)이 유용하다!
##### 브랜치 한줄로 관리하기
- 브랜치 `a`에서 `development`로 리베이스하여 브랜치를 한줄로 묶고자 하는 경우, 아래와 같이 입력
```bash
# 현재 브랜치가 a라고 가정
git rebase development
git checkout development
git merge a
```
##### 여러 커밋 합치기
```bash
git rebase -i HEAD~[커밋개수]
```
위 명령어를 입력하면 나오는 interactive 편집기에서 합칠 커밋만 `pick`을 `s`로 변경 후 저장(s=squash)

> [!warning] git rebase --continue
> `git rebase --continue` 입력하라고 뜨면 그거 입력하고 커밋 메시지 수정해주면 됨!

#### git commit 관련
##### 커밋 메시지 변경하기
```bash
git commit --amend
```
변경 내용을 원격 저장소에도 푸시하고자 하는 경우, `git push -f origin [브랜치명]` 명령어를 활용한다.
