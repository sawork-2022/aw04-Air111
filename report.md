# aw04

**本次作业在WSL上部署**

## task1

`docker run -d --name web-pos-0.5 --cpus=0.5 -p 18080:8080 web-pos`

注：在load testing之前首先在浏览器上访问了一次以防止被东哥ban，可能会影响测试时的response time，下同

使用gatling进行load testing

```shell
---- Global Information --------------------------------------------------------
> request count                                        100 (OK=100    KO=0     )
> min response time                                   2386 (OK=2386   KO=-     )
> max response time                                  11549 (OK=11549  KO=-     )
> mean response time                                  8389 (OK=8389   KO=-     )
> std deviation                                       2827 (OK=2827   KO=-     )
> response time 50th percentile                       9572 (OK=9572   KO=-     )
> response time 75th percentile                      10856 (OK=10856  KO=-     )
> response time 95th percentile                      11366 (OK=11366  KO=-     )
> response time 99th percentile                      11539 (OK=11539  KO=-     )
> mean requests/sec                                  8.333 (OK=8.333  KO=-     )
```

## task2

1. 在docker上运行四个0.5cpu的容器
2. 运行haproxy
3. 运行gatling test

```shell
---- Global Information --------------------------------------------------------
> request count                                        100 (OK=100    KO=0     )
> min response time                                   1069 (OK=1069   KO=-     )
> max response time                                   7110 (OK=7110   KO=-     )
> mean response time                                  5146 (OK=5146   KO=-     )
> std deviation                                       1772 (OK=1772   KO=-     )
> response time 50th percentile                       5979 (OK=5979   KO=-     )
> response time 75th percentile                       6437 (OK=6437   KO=-     )
> response time 95th percentile                       6840 (OK=6840   KO=-     )
> response time 99th percentile                       7045 (OK=7045   KO=-     )
> mean requests/sec                                   12.5 (OK=12.5   KO=-     )
```

虽然没有明显提升，甚至不足两倍，但是还是有提升的。原因除了提前访问过一次网站以外可能还有使用WSL套娃的缘故

## task3

给`getProducts`加上cache后（在手动访问过一次网站了之后）进行load testing，可以看到时间明显缩短。

```shell
---- Global Information --------------------------------------------------------
> request count                                        100 (OK=100    KO=0     )
> min response time                                    225 (OK=225    KO=-     )
> max response time                                   1032 (OK=1032   KO=-     )
> mean response time                                   798 (OK=798    KO=-     )
> std deviation                                        135 (OK=135    KO=-     )
> response time 50th percentile                        818 (OK=818    KO=-     )
> response time 75th percentile                        888 (OK=888    KO=-     )
> response time 95th percentile                        991 (OK=991    KO=-     )
> response time 99th percentile                       1014 (OK=1014   KO=-     )
> mean requests/sec                                     50 (OK=50     KO=-     )
---- Response Time Distribution ------------------------------------------------
> t < 800 ms                                            43 ( 43%)
> 800 ms < t < 1200 ms                                  57 ( 57%)
> t > 1200 ms                                            0 (  0%)
> failed                                                 0 (  0%)
```

session with redis整了好久，主要是我坚持autowire一个Cart（因为我觉得这才符合IoC原则），导致了未完全弄明白的`Field injection is not recommended`问题。

仅测试了session的有效性，用Edge和Chrome分别打开会话，证明确实Cart不同。