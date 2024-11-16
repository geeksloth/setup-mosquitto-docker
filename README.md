# setup-mosquitto-docker
A short note of setting up the mosquitto MQTT broker with Docker container.


## 1. Docker installation
This step is optional if you have already installed Docker in your system.

```code
# For Ubuntu
sudo apt-get update
sudo apt-get install -y docker.io

# For macOS
brew install --cask docker
```

## 2. Pull the Mosquitto Docker image
To pull the Mosquitto Docker image, run the following command:

```code
docker pull eclipse-mosquitto
```

## 3. Run the Mosquitto Docker container
To run the Mosquitto Docker container, use the following command:

```code
docker run -it -p 1883:1883 -p 9001:9001 -n mqtt_broker eclipse-mosquitto
```

To stop the container for further configuration, use the following command:

```code
docker stop mqtt_broker
```


## 4. Configuration
When running the image, the default configuration values are used. To use a custom configuration file, create your `mosquitto.conf` in `$PWD/mosquitto/config/mosquitto.conf`, then mount the config directory to `/mosquitto/config`.

```code
docker run -it -p 1883:1883 -v "$PWD/mosquitto/config:/mosquitto/config" eclipse-mosquitto
```

Configuration can be changed to:

- Persist data to `/mosquitto/data`
- Log to `/mosquitto/log/mosquitto.log`

i.e. add the following to `mosquitto.conf`:

```code
persistence true
persistence_location /mosquitto/data/
log_dest file /mosquitto/log/mosquitto.log
```

Note: If a volume is used, the data will persist between containers.

## 5. Run
Run a container using the new image:

```code
docker run -it -p 1883:1883 -v "$PWD/mosquitto/config:/mosquitto/config" -v /mosquitto/data -v /mosquitto/log eclipse-mosquitto
```

or:

```code
docker run -it -p 1883:1883 -v "$PWD/mosquitto/config:/mosquitto/config" -v "$PWD/mosquitto/data:/mosquitto/data" -v "$PWD/mosquitto/log:/mosquitto/log" eclipse-mosquitto
```

Note: if the Mosquitto configuration (`mosquitto.conf`) was modified to use non-default ports, the `docker run` command will need to be updated to expose the ports that have been configured.

For example, if you use port 1883 and port 8080:

```code
docker run -it -p 1883:1883 -p 8080:8080 -v "$PWD/mosquitto/config:/mosquitto/config" eclipse-mosquitto
```

## 6. Testing the setup
You can test the Mosquitto setup using an MQTT client like `mosquitto_pub` and `mosquitto_sub`.

```code
# Open a terminal and subscribe to a topic
mosquitto_sub -h localhost -t test/topic

# Open another terminal and publish a message to the topic
mosquitto_pub -h localhost -t test/topic -m "Hello, MQTT"
```

This should help you set up and test the Mosquitto MQTT broker using Docker.