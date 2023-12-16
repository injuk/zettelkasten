```yml
version: '3.8'  
services:  
  mongodb:  
    image: mongo  
    container_name: mongodb  
    restart: always  
    ports:  
      - 27017:27017  
    volumes:  
      - /tmp/mongodb:/data/db  
    environment:  
      - MONGO_INITDB_ROOT_USERNAME=root  
      - MONGO_INITDB_ROOT_PASSWORD=my-mongo-pw
      - MONGO_INITDB_DATABASE=stock  
  
  mongo-express:  
    image: mongo-express  
    restart: always  
    ports:  
      - 8081:8081  
    environment:  
      ME_CONFIG_MONGODB_ADMINUSERNAME: root  
      ME_CONFIG_MONGODB_ADMINPASSWORD: 1234  
      ME_CONFIG_MONGODB_URL: mongodb://root:1234@mongodb:27017
```

localhost:8081에 admin / pass로 express 접속 가능!