# 이게 뭐임?
> 뭘까요

## TODO
- [ ] like dislike는 하나로 퉁치지 말고 아래와 같이 나누기
```
data class UpdateRequest(
  val parts: List<T>,
  val likeCount: Int,
  val dislikeCount: Int,
)
```

 - [x] upsert 쓰지 말고 그냥 update로 수정하기
 - [x] 이미 좋아요 된 녀석 체크할 때에는 early return을 쓰기