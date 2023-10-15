#토이프로젝트 #commentor 

 - [x] like / dislike는 하나로 퉁치지 말고 나누기
 - [x] comment_interaction에 upsert 쓰지 말고 그냥 update로 수정하기
 - [x] ~~이미 좋아요 된 녀석 체크할 때에는 early return을 쓰기~~
	 - 이게 더 가독성이 떨어지는 것 같음
 - [x] actions controller 수정하기
	 - [x] [POST] /comments/action { type: BULK_DELETE }
	 - [x] [POST] /comments/{id}/action { type: LIKE | DISLIKE }
- [x] create sub comment는 별도 커맨드를 만들고, parent_id를 검증해야 함
- [ ] usecase 별 테스트 작성하기
- [ ] 리팩토링하기
	- [ ] 모든 의존성이 밖에서 안으로 향하는지 확인하기
	- [ ] dataAccess는 테스트를 모두 작성한 후에 리팩토링하기
- [ ] 데이터 왕창 밀어넣고 DB 인덱싱하기
---
[[a. Commentor란?]]