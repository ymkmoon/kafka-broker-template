# kafka-broker-template

Kafka Broker 역할

카프카 설치 및 테스트 명령어

MQTT Publisher -> **EMQX 클러스터 (MQTT Broker)** -> Kafka Broker -> Consumer

- MQTT Publisher : 추가예정
- MQTT Broker : https://github.com/ymkmoon/kafka-broker-template
- Kafka Broker : https://github.com/ymkmoon/kafka-broker-template
- Consumer : https://github.com/ymkmoon/springboot-consumer-template

## 빌드

### 1. Kafka 이미지 빌드 및 컨테이너 실행 

```bash
# 로컬 환경
docker compose -p kafka-cluster --env-file kafka.local.env up -d

# 로컬 환경
docker compose -p kafka-cluster --env-file kafka.dev.env up -d

```

### 2. 토픽 생성
	
#### 컨테이너 접속

```bash
docker exec -it 컨테이너이름 bash

docker exec -it kafka1 bash
```

#### 토픽 생성

```
kafka-topics --create \
  --topic 토픽이름 \
  --bootstrap-server 브로커주소 \
  --partitions 3 \
  --replication-factor 3

kafka-topics --create \
  --bootstrap-server kafka1:9092 \
  --replication-factor 3 \
  --partitions 3 \
  --topic vehicle-data
	
```


#### 토픽 목록 확인

```bash
kafka-topics --list --bootstrap-server 브로커주소

kafka-topics --list --bootstrap-server kafka1:9092,kafka2:9092,kafka3:9092
```


### 3. 메세지 발행

```bash
kafka-console-producer --topic 토픽이름 --bootstrap-server 브로커주소


kafka-console-producer --topic vehicle-data --bootstrap-server localhost:9092

kafka-console-producer --bootstrap-server kafka1:9092 --topic vehicle-data
kafka-console-producer --bootstrap-server kafka2:9092 --topic vehicle-data
kafka-console-producer --bootstrap-server kafka3:9092 --topic vehicle-data

> {"imei": "1234", "speed": 80, "time": "2025-08-27 10:00:00"}
> {"imei": "1234", "time": "2025-08-27 11:00:00"}
> {"imei": "1234", "speed": 80, "time": "2025-08-27 12:00:00", "location": {"lat": 36.12311111, "lng": 127.12311111}}
> {"imei": "1234", "speed": 80, "location": {"lat": 36.12322222, "lng": 127.12322222}}

```

## ETC

- Zookeeper
  - Kafka 클러스터의 메타데이터 관리
- 카프카 브로커
  - KAFKA_BROKER_ID : 각 브로커 ID를 다르게 지정
  - KAFKA_ADVERTISED_LISTENERS : 호스트별로 포트를 다르게 지정 (localhost:9092, 9093, 9094)
  - KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR : 복제 개수를 3으로 지정 (멀티 브로커 환경)
- 포트
  - 각 브로커 컨테이너의 9092 포트를 로컬에서 각각 9092, 9093, 9094로 바인딩
- 네트워크 공유
  - EMQX 와 Kafka 는 별도 Compose 이기 때문에 Docker 네트워크 공유

```bash
# 네트워크 생성
docker network create iot-net

# Kafka Compose
docker compose -p kafka-cluster --env-file kafka.env up -d
# EMQX Compose
docker compose -p emqx-cluster --env-file emqx.env up -d

# 두 Compose 모두 iot-net 네트워크에 연결
docker network connect iot-net kafka1
docker network connect iot-net kafka2
docker network connect iot-net kafka3

docker network connect iot-net emqx1
docker network connect iot-net emqx2
docker network connect iot-net emqx3
```