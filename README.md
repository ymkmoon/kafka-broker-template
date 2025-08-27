# kafka-template

카프카 설치 및 테스트 명령어

## 빌드

### 1. Kafka 이미지 빌드 및 컨테이너 실행 

```bash
docker-compose up -d
```

### 2. 토픽 생성
	
#### 컨테이너 접속

```bash
docker exec -it 컨테이너이름 bash

docker exec -it kafka-template bash
```

#### 토픽 생성

```bash
kafka-topics --create \
	--topic 토픽이름 \
	--bootstrap-server kafka:9092 \
	--partitions 1 \
	--replication-factor 1

kafka-topics --create \
	--topic vehicle-data \
	--bootstrap-server kafka:9092 \
	--partitions 1 \
	--replication-factor 1
```


#### 토픽 목록 확인

```bash
kafka-topics --list --bootstrap-server 서버주소

kafka-topics --list --bootstrap-server localhost:9092
```


### 3. 메세지 발행

```bash
kafka-console-producer --topic 토픽이름 --bootstrap-server 카프카주소


kafka-console-producer --topic vehicle-data --bootstrap-server localhost:9092

> {"imei": "1234", "speed": 80, "time": "2025-08-27 10:00:00"}
> {"imei": "1234", "time": "2025-08-27 11:00:00"}
> {"imei": "1234", "speed": 80, "time": "2025-08-27 12:00:00", "location": {"lat": 36.123, "lng": 127.123}}
> {"imei": "1234", "speed": 80, "location": {"lat": 36.123, "lng": 127.123}}

```