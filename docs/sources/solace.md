## Solace source (AMQP)

This event source is meant to be used as a Container Source with a Knative cluster to consume messages from [Solace pubsub+](https://solace.com/) and send them to a Knative service/function.

### Local Usage

1. Setup local Solace Server in Docker with the following command: 

```
sudo docker create \
--privileged=true \
--network=host \
--uts=host \
--shm-size=2g \
--ulimit core=-1 \
--ulimit memlock=-1 \
--ulimit nofile=2448:42192 \
--env 'username_admin_globalaccesslevel=admin' \
--env 'username_admin_password=admin' \
--name=solace solace/solace-pubsub-standard:latest
```

2. Create a queue 
- Navigate to `http://localhost:8080` to see Solace landing page where you can add credentials listed above
- Navigate to `default` message VPN
- Select `Queues` form the navbar
- Create new queue with any name, enter the queue and add a topic in the `Subscriptions` section 

3. Put messages in a queue
- cd into `producer` folder
- run  `go run main.go` to put a message to a queue

4. Consume messages from a queue
- From project root run `go run main.go` with all env vars set. It has default values otherwise
- Put messages to a queue as in step 3 in separate terminal. View received messages. 
- If no go environment installed, use instructions from `local Docker build` below

For more information about Solace and documentation, visit Solace [website](https://solace.com/) and [documentation](https://docs.solace.com/) sections


### Local Docker build

If you don't have a local Go environment, use Docker:

```
docker build -t solace-amqp-consumer .
```

```
docker run -ti -e URL="amqp://127.0.0.1:5672" \
               -e USER="admin" \
               -e PASSWORD="admin" \
               -e QUEUE_NAME="test" \
               --network=host \
               solace-amqp-consumer:latest
```

FYI `--network=host` is used to place solace-amqp-consumer:latest to the same network as solace client container created in the first step. Make changes to the networks accordingly, if you change local Solace Client network.