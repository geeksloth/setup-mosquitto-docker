# setup-mosquitto-docker
A short note of setting up the mosquitto MQTT broker with Docker container.



## 1. Docker installation and getting started
This step is optional if you have already installed Docker in your system.

```code
# For Ubuntu
sudo apt-get update
sudo apt-get install -y docker.io

# For macOS
brew install --cask docker
```

To pull the Mosquitto Docker image, run the following command:

```code
docker pull eclipse-mosquitto
```

To run the Mosquitto Docker container, use the following command:

```code
docker run -it -p 1883:1883 eclipse-mosquitto
```



## 2. Configuration
When running the image, the default configuration values are used. To use a custom configuration file, create your `mosquitto.conf` in `$PWD/mosquitto/config/mosquitto.conf` with following information:

```code
allow_anonymous false
listener 1883
protocol websockets
password_file /mosquitto/config/passwordfile
persistence true
persistence_location /mosquitto/data/
log_dest file /mosquitto/log/mosquitto.log
```

Create a blank file to store the useranme and password
```code
touch /mosquitto/config/passwordfile
```

Run a container using the new configuration:

```code
docker run -it -p 1883:1883 -v "$PWD/mosquitto/config:/mosquitto/config" -v "$PWD/mosquitto/data:/mosquitto/data" -v "$PWD/mosquitto/log:/mosquitto/log" eclipse-mosquitto
```

Create `username` and `password` in the passwordfile

Exec  into the mqtt container
```code
sudo docker exec -it <container-id> sh
```

Create new password file and add user and it will prompt for password
```code
mosquitto_passwd -c /mosquitto/config/passwordfile user1
```

Add additional users
```code
mosquitto_passwd /mosquitto/config/passwordfile user2
```

[Optional] To delete user:
```code
mosquitto_passwd -D /mosquitto/config/passwordfile <user-name>
```

type `exit` to exit the container



## 3. Testing the setup
You can test the Mosquitto setup using an MQTT client like `mosquitto_pub` and `mosquitto_sub`.

```code
# Open a terminal and subscribe to a topic
mosquitto_sub -h <mqtt_broker_ip> -t "test/topic" -u <user> -P <password>

# Open another terminal and publish a message to the topic
mosquitto_pub -h <mqtt_broker_ip> -t "test/topic" -m "Hello, MQTT" -u <user> -P <password>
```

This should help you set up and test the Mosquitto MQTT broker using Docker.
