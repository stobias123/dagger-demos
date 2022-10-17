## Dagger Parallelism Test

I'd expect the included `main.go` to exec the `golang` and `golang2` steps in parallel, but it does not. 

### In dagger.

Run the included dagger `ci/main.go`.

Note that `out2` is _after_ `out1`, and during the dagger run, you can see execution paused for `out1` to be completed.
```
go run ci/main.go

#17 date
#0 0.088 Mon Oct 17 19:30:36 UTC 2022
INFO[0045] out1 Mon Oct 17 19:30:35 UTC 2022            
INFO[0045] out2 Mon Oct 17 19:30:36 UTC 2022   
```

### In Docker multistage...

Build the dockerfile then run it.

Note that step `one` reports 10 seconds later than step `2`
```
# Build the dockerfile
docker build --no-cache -t local .
➜  getting-started git:(main) ✗ docker build --no-cache -t local .
[+] Building 10.9s (11/11) FINISHED                                                                                                                                                                                                       
 => [internal] load build definition from Dockerfile                                                                                                                                                                                 0.0s
 => => transferring dockerfile: 37B                                                                                                                                                                                                  0.0s
 => [internal] load .dockerignore                                                                                                                                                                                                    0.0s
 => => transferring context: 2B                                                                                                                                                                                                      0.0s
 => [internal] load metadata for docker.io/library/golang:1.18                                                                                                                                                                       0.4s
 => [internal] load metadata for docker.io/library/ubuntu:latest                                                                                                                                                                     0.0s
 => CACHED [main 1/3] FROM docker.io/library/ubuntu                                                                                                                                                                                  0.0s
 => CACHED [two 1/2] FROM docker.io/library/golang:1.18@sha256:b0462607b5b5c28a1166c421b3c84e36c0d61b0890372bcf7a2dbc24db0aa615                                                                                                      0.0s
 => [two 2/2] RUN echo "two reporing in $(date)" > /tmp/two                                                                                                                                                                          0.2s
 => [one 2/2] RUN sleep 10 && echo "one reporing $(date)" > /tmp/one                                                                                                                                                                10.2s
 => [main 2/3] COPY --from=one /tmp/one /tmp/one                                                                                                                                                                                     0.0s
 => [main 3/3] COPY --from=two /tmp/two /tmp/two                                                                                                                                                                                     0.0s
 => exporting to image                                                                                                                                                                                                               0.0s
 => => exporting layers                                                                                                                                                                                                              0.0s
 => => writing image sha256:3fa80e3979042d4f6e7de32478ddbe822e04b3daa796b515f42bd9e87ab7f70e                                                                                                                                         0.0s
 => => naming to docker.io/library/local                                                                                                                                                                                             0.0s
 
 ## then run
 ➜  getting-started git:(main) ✗ docker run local
one reporing Mon Oct 17 19:10:48 UTC 2022
two reporing in Mon Oct 17 19:10:38 UTC 2022
```