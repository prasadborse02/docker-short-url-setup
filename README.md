# Local Development Setup Guide

## Prerequisites Installation

### 1. Install Docker
```bash
# Update package index
sudo apt-get update

# Install required packages
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

# Add Docker's official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Set up stable repository
echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker Engine
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io

# Verify installation
sudo docker --version
```

### 2. Install Docker Compose
```bash
# Download Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/download/v2.18.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# Apply executable permissions
sudo chmod +x /usr/local/bin/docker-compose

# Verify installation
docker-compose --version
```

## Setting Up the Project

1. Clone the repository:
```bash
git clone https://github.com/prasadborse02/docker-short-url-setup.git
cd docker-short-url-setup
```

2. Verify the following files are present in the directory:
   - `docker-compose.yml`
   - `init.sql`

If files are missing, you can create them manually and copy the configurations from the repository.

## Running the Services

1. Start all services:
```bash
docker-compose up -d
```

2. Verify services are running:
```bash
docker-compose ps
```

## Service Access Details

- **Kafka**: Available at `localhost:9092`
- **Zookeeper**: Available at `localhost:2181`
- **MySQL**:
  - Host: `localhost`
  - Port: `3307`
  - Database: `url_shortener`
  - Username: `urlshortener_app`
  - Password: `password`
  - Root Password: `rootpassword`
- **Redis**: Available at `localhost:6379`

## Connecting to Services

### MySQL Connection
```bash
# Connect using MySQL client
mysql -h localhost -P 3307 -u urlshortener_app -p
# Enter password: password

# Or connect using Docker container
docker exec -it mysql mysql -u urlshortener_app -p
# Enter password: password
```

### Redis Connection
```bash
# Connect using redis-cli
redis-cli -h localhost -p 6379

# Or connect using Docker container
docker exec -it redis redis-cli
```

### Kafka Commands
```bash
# Enter Kafka container
docker exec -it kafka-setup_kafka_1 bash

# Create a topic
kafka-topics --create --topic my-topic --bootstrap-server localhost:9092 --partitions 1 --replication-factor 1

# List topics
kafka-topics --list --bootstrap-server localhost:9092

# Produce messages
kafka-console-producer --broker-list localhost:9092 --topic my-topic

# Consume messages
kafka-console-consumer --bootstrap-server localhost:9092 --topic my-topic --from-beginning
```

### Zookeeper Commands
```bash
# Connect to Zookeeper shell
docker exec -it kafka-setup_zookeeper_1 bash
zookeeper-shell localhost:2181

# Basic Zookeeper commands
ls /
get /brokers/ids/1
```

### Using Docker Compose Logs
```bash
# View logs for all services
docker-compose logs

# View logs for specific service
docker-compose logs kafka
docker-compose logs mysql
docker-compose logs redis
docker-compose logs zookeeper
```

## Stopping the Services

To stop all services:
```bash
docker-compose down
```

To stop and remove all data volumes:
```bash
docker-compose down -v
```

## Troubleshooting

1. If ports are already in use, modify the port mappings in `docker-compose.yml`
2. Ensure Docker daemon is running: `sudo systemctl status docker`
3. Check service logs: `docker-compose logs [service_name]`
