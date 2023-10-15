#토이프로젝트 #commentor 

### 정책
#### 공통
- 어떤 org / project / domain / resource에 대해 댓글을 생성할 수 있음
- 생성된 댓글을 조회하거나 수정, 삭제할 수 있음
- 댓글은 자식 댓글(서브코멘트)를 가질 수 있음
- 댓글 형태는 Atlassian 모델을 벤치마킹하였음
#### 삭제
- 삭제하더라도 실제로 삭제되진 않고, `is_deleted` 마킹만 함
	- 유튜브를 벤치마킹했음
	- 반면, 댓글이 연결된 리소스(`resource_id`)가 제거된 경우에는 진짜로 제거함
#### LIKE / DISLIKE
- 어떤 코멘트에 대해서는 사용자 당 최대 한 번씩만 좋아요 / 싫어요할 수 있음
- 이미 좋아요한 댓글을 다시 좋아요하거나, 싫어요한 댓글을 다시 싫어요하면 취소 동작으로 취급됨
- 기존 액션과 반대되는 액션을 취하는 경우 기존 액션을 취소하고 반대 액션을 적용함
	- 예를 들어, 좋아요한 댓글에 대해 싫어요할 경우 좋아요를 취소하고 싫어요만을 적용함

### API
#### CRUD
댓글 하나의 정보만을 볼 일이 없을 것 같아서 `[GET] /comments/{id}`는 제공하지 않음
- `[GET] /comments`
- `[POST] /comments`
- `[PATCH] /comments/{id}`
- `[DELETE] /comments/{id}`
#### 서브코멘트 생성 / 목록조회
수정과 삭제는 상술한 API를 사용하면 됨
- `[GET] /comments/{id}/sub-comments`
- `[POST] /comments/{id}/sub-comments`
#### action API
- `[POST] /comments/actions`: 리소스가 제거된 경우, 연결된 모든 댓글을 삭제하기 위해 사용
- `[POST] /comments/{id}/actions`: 임의의 댓글에 대한 LIKE / DISLIKE

### 테이블
아직 인덱싱 안했지롱
- 목록 조회를 기준으로, `org_id` / `project_id` / `domain` / `resource_id` / `created_at` 등을 인덱싱할 듯
#### comments
댓글 자체를 저장하기 위한 테이블
```
create table comments  
(  
    id            bigint generated always as identity,  
    org_id        character varying(24) default null,  
    project_id    character varying(24)  not null,  
    domain        character varying(24) default 'NONE',  
    resource_id   character varying(24)  not null,  
    is_deleted    boolean               default false,  
    like_count    bigint                default 0,  
    dislike_count bigint                default 0,  
    data          json                   not null,  
    parent_id     bigint                default null,  
    created_at    timestamp             default current_timestamp,  
    created_by_id character varying(255) not null,  
    updated_at    timestamp             default current_timestamp,  
    updated_by_id character varying(255) not null,  
  
    primary key (id)  
);
```

#### comment_interactions
개개인이 하나의 댓글에 여러 번 LIKE / DISLIKE하지 못하도록 하기 위한 테이블
```
create table comment_interactions  
(  
    id         bigint generated always as identity,  
    comment_id bigint                 not null,  
    type       character varying(24)  not null,  
    user_id    character varying(255) not null,  
  
    primary key (id),  
    constraint comment_interactions_uq_1 unique (comment_id, user_id)  
);
```
---
[[a. Commentor란?]]