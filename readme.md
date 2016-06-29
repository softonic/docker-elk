# docker-elk

[Version]: https://github.com/softonic/docker-elk/releases

The **ELK** stack makes searching and analyzing data easier than ever before. Using ELK you can gain insights in real-time from the log data from around the company.

<img src="https://raw.githubusercontent.com/softonic/docker-elk/master/imgs/elk.png"
 alt="Elk logo" title="Elk" align="center"/>

This elk is prepared to be used with marathon or docker-compose.

## Use with Docker-compose

To send logs from your own containers to this elk, you should configure logging driver as gelf pointing to elk stack machine in your stack docker-compose

```
services:
  <container>:
    driver: gelf
      options:
          gelf-address: "udp://${NO_PROXY}:12201"
          tag: "<container-name>"
```

###### NO_PROXY env variable is machine IP, in mac -> docker-machine env --no-proxy 

## Use with Marathon

To send logs from your own containers to this elk, you should configure logging driver as gelf pointing to elk stack machine in your stack marathon.json

```
{
  "id": "<container>",
  "container": {
    "docker": {
      "parameters": [
        { "key": "log-driver", "value": "gelf" },
        { "key": "log-opt", "value": "gelf-address=udp://logstash-elk-sys.marathon:12201" },
        { "key": "log-opt", "value": "tag=<container-name>" }
      ]
    }
  }
}

```
