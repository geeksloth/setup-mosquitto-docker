# setup-mosquitto-docker
A short note of setting up the mosquitto MQTT broker with Docker container.



## 1. Docker installation
> [!NOTE]
> This step is optional. If you have already installed Docker in your system and pulled the image, skip to the next step.

```bash
# For Debian-based, ie. Ubuntu, Raspberry Pi, ...
sudo apt-get update
sudo apt-get install -y docker.io

# For macOS
brew install --cask docker
```

To pull the Mosquitto Docker image, run the following command:

```bash
docker pull eclipse-mosquitto
```


## 2. Configuration
Create necessary directories:
```bash
/mosquitto/config
/mosquitto/data
/mosquitto/log
```

To use a custom configuration file, create your `mosquitto.conf` in `/mosquitto/config/mosquitto.conf` with following information:
```conf
allow_anonymous false
listener 1883
protocol websockets
password_file /mosquitto/config/passwordfile
persistence true
persistence_location /mosquitto/data
log_dest file /mosquitto/log/mosquitto.log
```

Run a container using the new configuration:
```bash
docker run -it -p 1883:1883 -v "/mosquitto/config:/mosquitto/config" -v "/mosquitto/data:/mosquitto/data" -v "/mosquitto/log:/mosquitto/log" eclipse-mosquitto
```


## 3. Username and password
Exec  into the mqtt container
```bash
sudo docker exec -it <container-id> sh
```

Create new password file and add user and it will prompt for password
```bash
mosquitto_passwd -c /mosquitto/config/passwordfile user1
```
> [!NOTE]
> `-c` means create the password file, just do it at the first time.
> You may change the `passwordfile` to any file name you want.

Add additional users
```bash
mosquitto_passwd /mosquitto/config/passwordfile user2
```

To delete user:
```bash
mosquitto_passwd -D /mosquitto/config/passwordfile <user-name>
```

> [!NOTE]
> type `exit` to exit the container



## 4. Testing the setup
You can test the Mosquitto setup using an MQTT client like `mosquitto_pub` and `mosquitto_sub`.

To subscribe the topic:
```bash
mosquitto_sub -h <mqtt_broker_ip> -t "test/topic" -u <user> -P <password>
```

To publish a message to the topic:
```bash
mosquitto_pub -h <mqtt_broker_ip> -t "test/topic" -m "Hello, MQTT" -u <user> -P <password>
```

This should help you set up and test the Mosquitto MQTT broker using Docker.
