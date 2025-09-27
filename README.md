# docker-comands

docker run -d --rm --name eureka-server --network jaiz-network -p 8761:8761 eureka-server

docker stop api-gateway && docker rm api-gateway
docker run -d \
  --name api-gateway \
  --network jaiz-network \
  -p 8081:8081 \
  -e EUREKA_CLIENT_SERVICEURL_DEFAULTZONE=http://eureka-server:8761/eureka/ \
  api-gateway


docker stop auth-service && docker rm auth-service
docker run -d \
  --name auth-service \
  --network jaiz-network \
  --restart=always \
  --memory="512m" \
  --memory-swap="768m" \
  -p 8082:8082 \
  -e SPRING_DATASOURCE_URL=jdbc:postgresql://jaizdb:5432/jaizdb \
  -e SPRING_DATASOURCE_USERNAME=elara \
  -e SPRING_DATASOURCE_PASSWORD=elara \
  -e EUREKA_CLIENT_SERVICEURL_DEFAULTZONE=http://eureka-server:8761/eureka/ \
  auth-service


  docker stop onboarding-service && docker rm onboarding-service
  docker run -d \
  --name onboarding-service \
  --network jaiz-network \
  -p 8083:8083 \
  -e SPRING_DATASOURCE_URL=jdbc:postgresql://jaizdb:5432/jaizdb \
  -e SPRING_DATASOURCE_USERNAME=elara \
  -e SPRING_DATASOURCE_PASSWORD=elara \
  -e EUREKA_CLIENT_SERVICEURL_DEFAULTZONE=http://eureka-server:8761/eureka/ \
  onboarding-service


 docker run -d --rm \
  --name beneficiary-service \
  --network jaiz-network \
  -p 8084:8084 \
  -e SPRING_DATASOURCE_URL=jdbc:postgresql://jaizdb:5432/jaizdb \
  -e SPRING_DATASOURCE_USERNAME=elara \
  -e SPRING_DATASOURCE_PASSWORD=elara \
  -e EUREKA_CLIENT_SERVICEURL_DEFAULTZONE=http://eureka-server:8761/eureka/ \
  beneficiary-service

docker stop transaction-service && docker rm transaction-service
docker run -d \
  --name transaction-service \
  --network jaiz-network \
  -p 8085:8085 \
  -e SPRING_DATASOURCE_URL=jdbc:postgresql://jaizdb:5432/jaizdb \
  -e SPRING_DATASOURCE_USERNAME=elara \
  -e SPRING_DATASOURCE_PASSWORD=elara \
  -e EUREKA_CLIENT_SERVICEURL_DEFAULTZONE=http://eureka-server:8761/eureka/ \
  transaction-service

docker stop notification-service && docker rm notification-service
docker run -d \
  --name notification-service \
  --network jaiz-network \
  -p 8086:8086 \
  -e SPRING_DATASOURCE_URL=jdbc:postgresql://jaizdb:5432/jaizdb \
  -e SPRING_DATASOURCE_USERNAME=elara \
  -e SPRING_DATASOURCE_PASSWORD=elara \
  -e EUREKA_CLIENT_SERVICEURL_DEFAULTZONE=http://eureka-server:8761/eureka/ \
  notification-service


docker run -d --rm \
  --name admin-service \
  --network jaiz-network \
  -p 8089:8089 \
  -e SPRING_DATASOURCE_URL=jdbc:postgresql://jaizdb:5432/jaizdb \
  -e SPRING_DATASOURCE_USERNAME=elara \
  -e SPRING_DATASOURCE_PASSWORD=elara \
  -e EUREKA_CLIENT_SERVICEURL_DEFAULTZONE=http://eureka-server:8761/eureka/ \
  admin-service



docker run -d \
  --name zookeeper \
  --restart=always \
  -p 2181:2181 \
  -e ZOOKEEPER_CLIENT_PORT=2181 \
  confluentinc/cp-zookeeper:7.4.0



docker run -d \
  --name kafka \
  --memory="1g" \
  --memory-swap="1.5g" \
  --restart=always \
  -p 9092:9092 \
  -e KAFKA_BROKER_ID=1 \
  -e KAFKA_ZOOKEEPER_CONNECT=zookeeper:2181 \
  -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://137.184.65.91:9092 \
  -e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092 \
  -e KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR=1 \
  -e KAFKA_HEAP_OPTS="-Xmx512M -Xms512M" \
  --link zookeeper \
  confluentinc/cp-kafka:7.4.0


docker run -d --name redis \
  -p 6379:6379 \
  --network jaiz-network \
  -v redis_data:/data \
  redis:7.2.4


======= CREATE KAFKA TOPICS ==========
docker run --rm --network jaiz-network confluentinc/cp-kafka \
  kafka-topics.sh \
  --create \
  --topic bvn-account-opening-topic \
  --bootstrap-server 137.184.65.91:9092 \
  --replication-factor 1 \
  --partitions 1








==================================================================================================

docker volume create keycloak_data


docker run -d \
  --name keycloak \
  --memory="1g" \
  --memory-swap="1.5g" \
  --restart=always \
  -p 8080:8080 \
  -v keycloak_data:/opt/keycloak/data \
  -e KEYCLOAK_ADMIN=admin \
  -e KEYCLOAK_ADMIN_PASSWORD=admin123 \
  -e KC_PROXY=edge \
  -e KC_HOSTNAME=jaiz.elara-solutions.com \
  -e KC_HOSTNAME_STRICT=false \
  -e KC_HTTP_ENABLED=true \
  -e KC_HTTP_PORT=8080 \
  -e KC_HTTPS_PORT=8443 \
  -e KC_HEAP=512m \
  quay.io/keycloak/keycloak:24.0.4 \
  start-dev

=================================================================================================


# CREATE MSSQL DATABASE 

  docker run -e "ACCEPT_EULA=Y" \
  -e "SA_PASSWORD=Elara!Passw0rd" \
  -p 1433:1433 \
  --name mssql \
  -d mcr.microsoft.com/mssql/server:2022-latest

  docker exec -it mssql /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Elara!Passw0rd'

  | Parameter    | Value                                      
| ------------ | ------------------------------------------ |
| **Server**   | `http://137.184.65.91,1433`            |
| **Username** | `SA`                                       |
| **Password** | `Elara!Passw0rd` (or your custom password) |
| **Port**     | `1433`                                     |
| **Protocol** | TCP/IP                                     |



# POSTGRES

docker pull postgres:15

docker run -d \
  --name jaizdb \
  -e POSTGRES_USER=elara \
  -e POSTGRES_PASSWORD=elara \
  -e POSTGRES_DB=jaizdb \
  -p 5432:5432 \
  -v pgdata:/var/lib/postgresql/data \
  postgres:15


docker stop jaizdb && docker rm jaizdb
docker volume create pgdata
docker run -d \
  --name jaizdb \
  --network jaiz-network \
  --restart=always \
  --memory="768m" \
  --memory-swap="1g" \
  -e POSTGRES_DB=elara \
  -e POSTGRES_USER=elara \
  -e POSTGRES_PASSWORD=elara \
  -p 5432:5432 \
  -v pgdata:/var/lib/postgresql/data \
  postgres:15



  docker run -d \
  --name fiduciadb \
  --network jaiz-network \
  -e POSTGRES_DB=elara \
  -e POSTGRES_USER=elara \
  -e POSTGRES_PASSWORD=jaizdb \
  -p 5433:5433 \
  -v pgdata:/var/lib/postgresql/data \
  postgres:15


  richardsdiejomaoh@ETZ-0762-TECH DEPLOYMENT-JARS % scp middleware-integration-lib-1.0.0.jar root@137.184.65.91:/home/projects/libs/




docker run -d -p 9000:9000 \
  -e KAFKA_BROKERCONNECT=137.184.65.91:9092 \
  obsidiandynamics/kafdrop




# LOGIN TO DIGITAL OCEAN
ssh -i ~/.ssh/digital_ocean_key -L 8080:172.21.19.190:8080 root@137.184.65.91







