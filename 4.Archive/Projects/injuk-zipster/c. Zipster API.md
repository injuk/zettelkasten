#토이프로젝트 #zipster 

### API
```yml
openapi: 3.0.0
info:
  title: Zipster
  version: '1.0'
  license:
    name: injuk
    url: 'https://github.com/injuk'
  contact:
    email: ingnoh1001@gmail.com
    name: injuk gmail
    url: 'https://github.com/injuk'
  description: injuk-zipster application API spec
servers:
  - url: 'http://localhost:33729/api/{basePath}'
    variables:
      basePath:
        enum:
          - v1
        default: v1
paths:
  /download:
    post:
      summary: 오브젝트 다운로드
      operationId: download
      responses:
        '201':
          description: OK
          content:
            application/octet-stream:
              schema:
                type: string
                format: byte
          headers: {}
        '400':
          description: Bad Request
      tags:
        - zipster
      description: |-
        AWS S3 등 임의의 오브젝트 스토리지로부터 다음과 같은 조건으로 파일을 다운로드합니다. 
        - 요청 대상이 1개인 경우 일반 파일로 응답합니다.
        - 요청 대상이 둘 이상인 경우 zip으로 응답합니다.
      requestBody:
        $ref: '#/components/requestBodies/DownloadRequest'
components:
  schemas:
    DownloadTarget:
      title: DownloadTarget
      type: object
      properties:
        service:
          $ref: '#/components/schemas/StorageService'
        bucketName:
          type: string
          description: 다운로드 대상 오브젝트가 포함된 버킷
        objects:
          type: array
          minItems: 1
          description: '다운로드 대상 오브젝트 목록으로, 아래와 같이 ''/'' 문자로 시작하지 않아야 함'
          items:
            type: object
            properties:
              key:
                type: string
                description: '다운로드 대상 오브젝트의 key이며, 빈 문자열인 경우 버킷에 대한 root 경로로 취급'
                pattern: '^$|^[^/].*'
                example: folderA/folderB/object.mp4 or folderA/
            required:
              - key
      required:
        - service
        - bucketName
        - objects
    StorageService:
      title: StorageService
      enum:
        - AWS_S3
        - GCP_GCS
        - AZURE_BLOB
    DownloadOption:
      title: DownloadOption
      type: object
      description: '다운로드 자체에 대한 옵션으로, 결과물 파일의 이름을 의미'
      properties:
        attachment:
          type: object
          properties:
            fileName:
              type: string
              description: 다운로드 될 파일의 이름
          required:
            - fileName
    OnError:
      title: OnError
      enum:
        - EXIT
        - IGNORE
        - COMPLETE
      description: |-
        NotFound 등 다운로드 예외가 발생한 경우에 대한 처리 방식
        - IGNORE: 무시하고 계속 진행
        - EXIT: 예외 발생 시점에 즉시 종료하고 예외로 응답
        - COMPLETE: 예외 발생 시점에 zipping을 완료하고 성공으로 응답
      default: EXIT
    Notification:
      title: Notification
      enum:
        - NOTHING
        - EMAIL
        - AWS_EVENT_BRIDGE
      description: 다운로드 완료시 이를 사용자에게 알리기 위한 구분(가능한 경우에만 작업할 예정.)
      default: NOTHING
    DownloadFilter:
      title: DownloadFilter
      type: object
      properties:
        type:
          enum:
            - EXTENSION
            - STORAGE_CLASS
            - MIME_TYPE
            - SIZE
          description: |-
            다운로드 목록으로부터 필터링할 파일의 기준을 의미
            - EXTENSION: 파일 확장자를 기준으로 필터
            - STORAGE_CLASS: 스토리지 클래스를 기준으로 필터
            - MIME_TYPE: 파일의 mimeType을 기준으로 필터
        operation:
          enum:
            - EQUAL
            - NOT_EQUAL
            - GREATER_THAN
            - LESS_THAN
          description: |-
            filter type에 대해 적용할 연산자를 의미
            - EQUAL, NOT_EQUAL: EXTENSION, STORAGE_CLASS, MIME_TYPE
            - GREATER_THAN, LESS_THAN: SIZE
        operand:
          type: string
          description: 'filter type에 대해 operand를 적용할 피연산자이며, 숫자더라도 반드시 문자열로 전달'
      required:
        - type
        - operation
        - operand
  requestBodies:
    DownloadRequest:
      content:
        application/json:
          schema:
            type: object
            description: 다운로드 요청 본문에 대한 정보
            properties:
              targets:
                type: array
                description: 버킷 별로 하나의 요소를 포함하는 다운로드 대상
                minItems: 1
                items:
                  $ref: '#/components/schemas/DownloadTarget'
              option:
                $ref: '#/components/schemas/DownloadOption'
              strategy:
                type: object
                required:
                  - onError
                description: 다운로드 시 적용될 전략
                properties:
                  onError:
                    $ref: '#/components/schemas/OnError'
              filters:
                type: array
                minItems: 0
                description: 다운로드 시 적용될 필터
                items:
                  $ref: '#/components/schemas/DownloadFilter'
              notification:
                $ref: '#/components/schemas/Notification'
            required:
              - targets
              - strategy
              - filters
              - notification
tags:
  - name: zipster
    description: zipster application
```

---
[[a. Zipster란?]]