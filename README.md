# Profiling application

### Golang pprof

Profiling periodically (e.g. 1ms) stops the process and gets stack traces from all goroutines. "The one function that shows up the most usually is taking the most time."

#### Getting profile from HTTP

An application can expose HTTP pprof APIs:

* `/debug/pprof/goroutine` - tracks all current goroutines
* `/debug/pprof/profile` - pprof-formatted cpu profile
* `/debug/pprof/heap` - pprof-formatted heap profile. The stack traces in the heap profile are stack trace at the time of allocation. For instance a function could allocate memory, return and other function that is freeing the memory is misbehaving but the function in the profile is the one that allocated the memory.
* `/debug/pprof/cmdline` - responds with the running program's

All profiles are just collections of stacktraces, maybe with some additional metadata attached. For instance in heap profile the stacktrace has a number of bytes of memory attached to it. 

```bash
go tool pprof -http=:/tmp/go-build1799250859/b001/exe/main http://localhost:14269/debug/pprof/profile?seconds=30
```

The path to binary is needed to capture source code.


```bash
go tool pprof http://localhost:14269/debug/pprof/profile?seconds=30
top 10 -cum # to get top 10 slow functions
list .funcName # shows function source code
```

`top -` parameters
* `cum` - total time need to execute the whole function
* `flat` - total time needed to execute the function excluding external function
* `sum` - coverage percentage from each line

```bash
(pprof) top
Showing nodes accounting for 16.55s, 94.90% of 17.44s total
Dropped 201 nodes (cum <= 0.09s)
Showing top 10 nodes out of 39
      flat  flat%   sum%        cum   cum%
     6.86s 39.33% 39.33%      9.11s 52.24%  github.com/jaegertracing/jaeger/plugin/storage/memory.(*Store).validSpan
     6.74s 38.65% 77.98%     15.85s 90.88%  github.com/jaegertracing/jaeger/plugin/storage/memory.(*Store).validTrace (inline)
     1.99s 11.41% 89.39%      1.99s 11.41%  memeqbody
     0.37s  2.12% 91.51%      0.51s  2.92%  runtime.mapiternext
     0.33s  1.89% 93.41%     16.80s 96.33%  github.com/jaegertracing/jaeger/plugin/storage/memory.(*Store).FindTraces
     0.09s  0.52% 93.92%      0.10s  0.57%  time.Time.After
     0.08s  0.46% 94.38%      0.27s  1.55%  runtime.scanobject
     0.07s   0.4% 94.78%      0.09s  0.52%  time.Time.Before
     0.01s 0.057% 94.84%      0.09s  0.52%  encoding/json.sliceEncoder.encode
     0.01s 0.057% 94.90%      0.25s  1.43%  runtime.gcDrain
(pprof) top -sum
Ignore expression matched no samples
Active filters:
   ignore=sum
Showing nodes accounting for 16.55s, 94.90% of 17.44s total
Dropped 201 nodes (cum <= 0.09s)
Showing top 10 nodes out of 39
      flat  flat%   sum%        cum   cum%
     6.86s 39.33% 39.33%      9.11s 52.24%  github.com/jaegertracing/jaeger/plugin/storage/memory.(*Store).validSpan
     6.74s 38.65% 77.98%     15.85s 90.88%  github.com/jaegertracing/jaeger/plugin/storage/memory.(*Store).validTrace (inline)
     1.99s 11.41% 89.39%      1.99s 11.41%  memeqbody
     0.37s  2.12% 91.51%      0.51s  2.92%  runtime.mapiternext
     0.33s  1.89% 93.41%     16.80s 96.33%  github.com/jaegertracing/jaeger/plugin/storage/memory.(*Store).FindTraces
     0.09s  0.52% 93.92%      0.10s  0.57%  time.Time.After
     0.08s  0.46% 94.38%      0.27s  1.55%  runtime.scanobject
     0.07s   0.4% 94.78%      0.09s  0.52%  time.Time.Before
     0.01s 0.057% 94.84%      0.09s  0.52%  encoding/json.sliceEncoder.encode
     0.01s 0.057% 94.90%      0.25s  1.43%  runtime.gcDrain
(pprof) top -flat
Active filters:
   ignore=flat
Showing nodes accounting for 16.53s, 94.78% of 17.44s total
Dropped 178 nodes (cum <= 0.09s)
Showing top 10 nodes out of 35
      flat  flat%   sum%        cum   cum%
     6.86s 39.33% 39.33%      9.08s 52.06%  github.com/jaegertracing/jaeger/plugin/storage/memory.(*Store).validSpan
     6.74s 38.65% 77.98%     15.82s 90.71%  github.com/jaegertracing/jaeger/plugin/storage/memory.(*Store).validTrace (inline)
     1.99s 11.41% 89.39%      1.99s 11.41%  memeqbody
     0.37s  2.12% 91.51%      0.51s  2.92%  runtime.mapiternext
     0.33s  1.89% 93.41%     16.77s 96.16%  github.com/jaegertracing/jaeger/plugin/storage/memory.(*Store).FindTraces
     0.09s  0.52% 93.92%      0.10s  0.57%  time.Time.After
     0.07s   0.4% 94.32%      0.09s  0.52%  time.Time.Before
     0.06s  0.34% 94.67%      0.24s  1.38%  runtime.scanobject
     0.01s 0.057% 94.72%      0.09s  0.52%  encoding/json.sliceEncoder.encode
     0.01s 0.057% 94.78%      0.25s  1.43%  runtime.gcDrain
(pprof) top -cum
Showing nodes accounting for 0, 0% of 17.44s total
Dropped 201 nodes (cum <= 0.09s)
Showing top 10 nodes out of 39
      flat  flat%   sum%        cum   cum%
         0     0%     0%     17.09s 97.99%  net/http.(*conn).serve
         0     0%     0%     17.06s 97.82%  github.com/gorilla/handlers.recoveryHandler.ServeHTTP
         0     0%     0%     17.06s 97.82%  net/http.serverHandler.ServeHTTP
         0     0%     0%     17.01s 97.53%  net/http.HandlerFunc.ServeHTTP
         0     0%     0%     16.95s 97.19%  github.com/gorilla/handlers.CompressHandlerLevel.func1
         0     0%     0%     16.95s 97.19%  github.com/gorilla/mux.(*Router).ServeHTTP
         0     0%     0%     16.95s 97.19%  github.com/jaegertracing/jaeger/cmd/query/app.additionalHeadersHandler.func1
         0     0%     0%     16.94s 97.13%  github.com/jaegertracing/jaeger/cmd/query/app.(*APIHandler).search
         0     0%     0%     16.94s 97.13%  github.com/opentracing-contrib/go-stdlib/nethttp.MiddlewareFunc.func5
         0     0%     0%     16.80s 96.33%  github.com/jaegertracing/jaeger/cmd/query/app/querysvc.QueryService.FindTraces (inline)
```

#### Use `curl`

```bash
curl -s http://localhost:8080/debug/pprof/heap?seconds=20 > /tmp/heap.out
go tool pprof /tmp/heap.out
web # opens web interface
```

#### Gotool trace

```bash
wget http://localhost:14269/debug/pprof/trace
go tool trace -http=:8080 trace
```

#### Compare profiles

```
go tool pprof -http :8080 --diff_base=/main.prof /best.prof
```

### Parca

```bash
docker run --rm -it  --net=host -v $PWD:/tmp  ghcr.io/parca-dev/parca:main-4e857ab7 /parca --config-path=/tmp/parca.yaml
```
### Time

```bash
time ls
```

* real - total wall time
* sys - CPU time spent in the Kernel
* user - CPU time spent in the user space executing the code

sys+user can be higher than real on multicore systems

## Resources

* Profiling golang applications: https://www.youtube.com/watch?v=nok0aYiGiYA
* https://steveazz.xyz/blog/go-performance-tools-cheat-sheet/
* Gitlab profiling: https://www.youtube.com/watch?v=0_oTkC6HYPA
