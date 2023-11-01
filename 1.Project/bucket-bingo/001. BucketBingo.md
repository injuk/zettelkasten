# bucket-bingo
- bucket-bingo-console
- bucket-bingo-api

### 주요 기능
- 빙고판 CRUD
	- 빙고칸 RU(PUT, PATCH, GET)

### 정책
- 빙고판을 생성하고, 빙고칸은 자동 생성 된다
- 빙고판을 삭제하면 빙고칸도 전체 삭제된다
- 빙고판 크기는 5x5 ~ 10x10까지 가능하다
	- MVP는 5x5만 지원함
- 빙고판 성공 기준
- 빙고칸은 삽입시 순서대로 넣거나 / 섞어서 넣거나(FE에서 설정하고, BE)
- 빙고판을 상세조회하면 빙고칸들이 쭈루룩 딸려서 응답에 포함됨
	- (빙고칸에 대한 상세조회는 백로그)
- 빙고칸은 부분성공 개념이 있을 수 있다
	- 예시: 등산 3회하기
### 리소스들
#### 빙고판
- 식별자
- 이름
- 상태(완료 / 진행중 / )
- 성공 기준(빙고 개수)
	- 5x5 기준 5개 빙고면 성공
- 완료목표일정?
```
// BingoBoard
{
	id: number,
	name: string | null,
	status: BingoBoardStatus,
	created: {
		at: string,
		by: {
			id: number
		}
	},
	updated: {
		at: string,
		by: {
			id: number
		}
	}
}

// BoardStatus
["DRAFT", ""]
```

#### 빙고칸
- 식별자
- 빙고판 식별자
- 순서
- 목표?
- 상태(성공 / 실패)
```
{
	id: number,
	order: number,
	status: SUCCEED | FAILED | NONE,
	
	updated: {
		at: string,
		by: {
			id: number
		}
	}
}
```

---
### 10.31.
- [x] 주제 선정
- [x] 주요 기능 / 정책 설정
- [x] 모델 설계

### 11.02.
- [ ] 모델 설계
- [ ] Stoplight에 API 설계
- [ ] 모킹 서버 띄우기?