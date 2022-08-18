This is an example of how to use [Filebeat](https://www.elastic.co/beats/filebeat) [dissect](https://www.elastic.co/guide/en/beats/filebeat/current/dissect.html) processor 

**k8s/random-app-deployment** consists of one container and a sidecar.
* **random-app** is not a real application. It is just a random log generator. I am using [mingrammer/flog](https://hub.docker.com/r/mingrammer/flog) for that purpose
* **my-filebeat** is a sidecar container that reads logs generated from **ramdom-app** and sends them to a Redis output

You can use any output processor , but I am familiar with Redis.

Both container shares an empty dir to read/write log files

You can deploy all into your kubernetes cluster using `skaffold deploy` or whatever tool of your choice.


Once everything is running you can check dissected messages like this.
```
kubectl exec --stdin --tty redis -- redis-cli lindex filebeat 0
```
```
{

    "message": "60.83.13.111 - little3235 [18/Aug/2022:11:12:23 +0000] " GET /envisioneer/distributed/technologies HTTP/2.0" 204 1238",
   
    "request": {
        "ip": "60.83.13.111",
        "user": "little3235",
        "date": "18/Aug/2022:11:12:23 +0000",
        "method": "GET",
        "url": "/envisioneer/distributed/technologies",
        "http_version": "2.0",
        "code": 204,
        "latency": 1238
    },
    ...
}
```
## Interesting links

* [Online dissect tester](https://dissect-tester.jorgelbg.me)

* [dissect origins](https://www.elastic.co/blog/logstash-dude-wheres-my-chainsaw-i-need-to-dissect-my-logs)
