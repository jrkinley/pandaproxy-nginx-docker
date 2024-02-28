# Panda Proxy with Nginx

```shell
% docker compose up -d

[+] Running 6/6
 ✔ Network redpanda-http-proxy_default  Created
 ✔ Container redpanda1                  Started
 ✔ Container redpanda2                  Started
 ✔ Container redpanda3                  Started
 ✔ Container redpanda-console           Started
 ✔ Container redpanda-nginx             Started

% rpk topic create iss -p 3 -r 3 -X brokers=localhost:19092

TOPIC  STATUS
iss    OK

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