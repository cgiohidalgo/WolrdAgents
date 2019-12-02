# CRUD
Application programming interface (API) Restful web-service library written in C++11  based on boost.ASIO and CRUD handlers

This library supports persistent connections send from Linux-console to show in frontend in HTML 

[![build ](https://travis-ci.org/venediktov/CRUD.svg?branch=master)](https://travis-ci.org/venediktov/CRUD)

### Installing from GitHub
```bash
git clone https://github.com/cgiohidalgo/WolrdAgents
```

### Building on Linux 
```bash
$ mkdir Release
$ cd Release
$ cmake -DCMAKE_BUILD_TYPE=Release -DCRUD_WITH_EXAMPLES=1 .. -G "Unix Makefiles"
$ make -j $(nproc) install

```

### Building on  Mac OS 
```bash
$ mkdir Release
$ cd Release
$ cmake -DCMAKE_BUILD_TYPE=Release -DCRUD_WITH_EXAMPLES=1 .. -G "Unix Makefiles"
$ make -j $(sysctl -n hw.physicalcpu) install

```



### Running examples 
```bash
$ cd Release/examples
$ ./webserver 0.0.0.0 8080 . & 
$ ./simple_restful_service 0.0.0.0 8081 . & 
$ ./regex_restful_service 0.0.0.0 8082 . & 
$ ./persisted_regex_restful_service 0.0.0.0 8083 . &

```


### Testing output and benchmarks
```bash
curl localhost:8080
curl localhost:8081/venue_handler/RTB
curl localhost:8082/venue_handler/ANY/123
curl localhost:8083/venue_handler/ANY/123
ab -k -n 100000 -c 30 http://localhost:8081/RTB
ab -k -n 100000 -c 30 http://localhost:8082/ANY/123
ab -k -n 100000 -c 30 http://localhost:8083/ANY/123

```

### Stop all background examples
```bash
pkill -9 "persisted|restful|webserver"
```


## Utilizing API

### REST handler with regex

```C++
    using regex_restful_dispatcher_t =  http::crud::crud_dispatcher<http::server::request, http::server::reply>;
    regex_restful_dispatcher_t regex_handler(".") ; //root is irrelavant for REST only used for web-server
    regex_handler.crud_match(boost::regex("/venue_handler/(\w+)") )
                 .post([](http::server::reply & r, const http::crud::crud_match<boost::cmatch> & match) {
                    r << "{}" << http::server::reply::flush("json") ;
                    std::cout << "POST group_1_match=[ << match[1] << "], request_data=" << match.data << std::endl;
                 });
```

### simple REST handler

```C++
   // SIMPLE NO REGEX MATCH FOR POST "/venue_handler/RTB"
    using simple_restful_dispatcher_t = http::crud::crud_dispatcher<http::server::request, http::server::reply, std::string, std::string>;
    simple_restful_dispatcher_t simple_handler(".") ; //root is irrelavant for REST only used for web-server
    simple_handler.crud_match(std::string("/venue_handler/RTB") )
                  .post([](http::server::reply & r, const http::crud::crud_match<std::string> & match) {
                     r << "{}" << http::server::reply::flush("json") ;
                     std::cout << "POST request_data=" << match.data << std::endl;
                   });
```
