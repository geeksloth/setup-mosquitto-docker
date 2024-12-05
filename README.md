# setup-mosquitto-docker
A short note of setting up the mosquitto MQTT broker with Docker container.



## 1. Docker installation
> [!TIP]
> This step is optional. If you have already installed Docker in your system and pulled the image, skip to the next step.

For Debian-based, ie. Ubuntu, Raspberry Pi, ...
```bash
sudo apt-get update && sudo apt-get install -y docker.io
```
```bash
sudo usermod -aG docker $USER
```
For macOS
```bash
brew install --cask docker
```

To pull the Mosquitto Docker image, run the following command:

```bash
docker pull eclipse-mosquitto
```


## 2. Configuration
Create necessary directories:
```bash
mkdir ~/mosquitto && mkdir ~/mosquitto/config && mkdir ~/mosquitto/data && mkdir ~/mosquitto/log
```
Download configuration template file: [mosquitto.conf](https://github.com/geeksloth/setup-mosquitto-docker/blob/main/mosquitto.conf), and save to `~/mosquitto/config/mosquitto.conf`, then modify it with following information:
```conf
allow_anonymous false
listener 1883
#protocol websockets
password_file /mosquitto/config/passwordfile
persistence true
persistence_location /mosquitto/data/
log_dest file /mosquitto/log/mosquitto.log
```
> [!NOTE]
> `/mosquitto` in this file is not the same as `~/mosquitto` from the previous step. These directory will be mounted to a Docker container and working inside there.

Create a `passwordfile` to store user and password for the service.
```bash
sudo touch ~/mosquitto/config/passwordfile
```

Run a container using the new configuration:
```bash
docker run -it -d -p 1883:1883 -v "$HOME/mosquitto/config:/mosquitto/config" -v "$HOME/mosquitto/data:/mosquitto/data" -v "$HOME/mosquitto/log:/mosquitto/log" --name mqtt-broker eclipse-mosquitto
```


## 3. Username and password
Exec  into the mqtt container
```bash
docker exec -it mqtt-broker sh
```

Create new password file and add user and it will prompt for password
```bash
mosquitto_passwd /mosquitto/config/passwordfile user1
```

Add additional users
```bash
mosquitto_passwd /mosquitto/config/passwordfile user2
```

To delete user:
```bash
mosquitto_passwd -D /mosquitto/config/passwordfile <user-name>
```

> [!TIP]
> type `chmod 0700 /mosquitto/config/passwordfile` to change the permission, and `chown root /mosquitto/config/passwordfile` to change the owner.
> type `exit` to exit the container



## 4. Testing the setup
You can test the Mosquitto setup using an MQTT client like `mosquitto_pub` and `mosquitto_sub`.

To subscribe the topic:
```bash
mosquitto_sub -h localhost -t "test/topic" -u <user> -P <password>
```

To publish a message to the topic:
```bash
mosquitto_pub -h localhost -t "test/topic" -m "Hello, MQTT" -u <user> -P <password>
```

This should help you set up and test the Mosquitto MQTT broker using Docker.
