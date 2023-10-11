#토이프로젝트
## 용도
- 여기 저기에 붙일 수 있는 댓글 서비스
- 사실 댓글 기능을 만들어보고 싶은건 아니고, 코프링 써보고 싶어서 적당한 주제를 선정하였음
	- 때문에 인증은 안붙일 예정

## 스택
- Kotlin + Spring Boot + Jooq
- PostgreSQL
- opeAPIGenerator

## 기간
- 넉넉히 `2023-08-21 ~ 2023-10-31`으로 잡음

## 목표
- [x] 헥사고널 아키텍쳐 적용해보기
- [x] 커서 기반 페이지네이션 구현해보기
	- 요거는 근데 좀 고도화해야할 듯
- [ ] Kotlin으로 완성된 프로젝트 만들어보기

## TODO
- [x] like dislike는 하나로 퉁치지 말고 아래와 같이 나누기
```
data class UpdateRequest(
  val parts: List<T>,
  val likeCount: Int,
  val dislikeCount: Int,
)
```

 - [x] comment_interaction에 upsert 쓰지 말고 그냥 update로 수정하기
 - [x] ~~이미 좋아요 된 녀석 체크할 때에는 early return을 쓰기~~
	 - 이게 더 가독성이 떨어지는 것 같음
 - [ ] actions controller 수정하기
	 - [ ] [POST] /comments/action { type: BULK_DELETE }
	 - [ ] [POST] /comments/{id}/action { type: LIKE | DISLIKE }

## 회고
### 좋았던 것
- 무엇이 있을까

### 아쉬웠던 것
- 어떤게 있을까