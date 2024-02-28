# Redpanda with Nginx

Load balancing the Redpanda HTTP proxy and schema registry endpoints.

## Spin up the containers

```shell
% docker compose up -d

[+] Running 6/6
 ✔ Network redpanda-http-proxy_default  Created
 ✔ Container redpanda1                  Started
 ✔ Container redpanda2                  Started
 ✔ Container redpanda3                  Started
 ✔ Container redpanda-console           Started
 ✔ Container redpanda-nginx             Started
```

## Create a topic

https://docs.redpanda.com/current/get-started/rpk-install

```shell
% rpk topic create iss -p 3 -r 3 -X brokers=localhost:19092

TOPIC  STATUS
iss    OK
```

## Send data to Redpanda via nginx

Send the current location of the International Space Station (ISS) to the nginx lod balancer, which in turn will send it to one of the Redpanda HTTP proxy endpoints. Nginx is configured to route connections based on a hash of the clients IP address, so that the same client will always connect to the same broker.

```shell
% curl -s http://api.open-notify.org/iss-now.json | \
  jq '. | {records: [{value: .}]}' | \
  curl -s -X POST "http://localhost:18082/topics/iss" -H "Content-Type: application/vnd.kafka.json.v2+json" -d @- | jq .

{
  "offsets": [
    {
      "partition": 0,
      "offset": 1
    }
  ]
}
```
