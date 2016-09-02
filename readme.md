# docker-elk

[Version]: https://github.com/softonic/docker-elk/releases

The **ELK** stack makes searching and analyzing data easier than ever before. Using ELK you can gain insights in real-time from the log data from around the company.

<img src="https://raw.githubusercontent.com/softonic/docker-elk/master/imgs/elk.png"
 alt="Elk logo" title="Elk" align="center"/>

This elk is prepared to be used with marathon or docker-compose.

This stack contains 3 different containers. Elasticsearch, Logstash and Kibana. Each one is using official repository except by logstash, having preconfigured gelf input driver.

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

## GELF Configs

#### PHP-FPM
#####Access logs
Will parse a custom log to add some valuable information:
 * The real client ip & the internal docker IP.
 * Time spent (ms)
 * Memory peak reached (KB)
 * Percentage of CPU used(%)

Custom format in <code>/usr/local/etc/php-fpm.d/www.conf</code>
 ```
access.format = "%{REMOTE_ADDR}e %R - %t \"%m %r%Q%q\" %s %{mili}dms %{kilo}MKB %C%%"
 ```
Example:
 ```
192.168.99.1 172.23.0.3 - 31/Aug/2016:15:22:36 +0200 "GET /index.php" 500 268.042ms 2048KB 37.31%
 ```
#####Error logs
Will parse the default output format of the access logs. The error logs will be dropped. Example:
```
[31-Aug-2016 15:28:47] WARNING: [pool www] child 7 said into stderr: "NOTICE: PHP message: [2016-08-31 13:28:47] dev.ERROR: ErrorException: Undefined variable: data in /var/www/html/routes/web.php:15"
[31-Aug-2016 15:28:47] WARNING: [pool www] child 7 said into stderr: "Stack trace:"
[31-Aug-2016 15:28:47] WARNING: [pool www] child 7 said into stderr: "#0 /var/www/html/routes/web.php(15): Illuminate\Foundation\Bootstrap\HandleExceptions->handleError(8, 'Undefined varia...', '/var/www/html/r...', 15, Array)"
[31-Aug-2016 15:28:47] WARNING: [pool www] child 7 said into stderr: "#1 /var/www/html/vendor/laravel/framework/src/Illuminate/Routing/Route.php(176): App\Providers\RouteServiceProvider->{closure}()"
[31-Aug-2016 15:28:47] WARNING: [pool www] child 7 said into stderr: "#2 /var/www/html/vendor/laravel/framework/src/Illuminate/Routing/Route.php(147): Illuminate\Routing\Route->runCallable()"
[31-Aug-2016 15:28:47] WARNING: [pool www] child 7 said into stderr: "#3 /var/www/html/vendor/laravel/framework/src/Illuminate/Routing/Router.php(642): Illuminate\Routing\Route->run(Object(Illuminate\Http\Request))"
[31-Aug-2016 15:28:47] WARNING: [pool www] child 7 said into stderr: "#4 /var/www/html/vendor/laravel/framework/src/Illuminate/Routing/Pipeline.php(53): Illuminate\Routing\Router->Illuminate\Routing\{closure}(Object(Illuminate\Http\Request))"
[31-Aug-2016 15:28:47] WARNING: [pool www] child 7 said into stderr: "#5 /var/www/html/vendor/laravel/framework/src/Illuminate/Routing/Middleware/SubstituteBindings.php(41): Illuminate\Routing\Pipeline->Illuminate\Routing\{..."
```


#### NGINX
#####Access logs
Will parse the default output format of the access logs. Example:
```
192.168.99.1 - - [31/Aug/2016:13:28:47 +0000] "GET / HTTP/1.1" 500 19237 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/52.0.2743.116 Safari/537.36"
```
#####Error logs
The error logs will be dropped.
