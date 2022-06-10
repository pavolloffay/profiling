Original source: https://www.youtube.com/watch?v=0_oTkC6HYPA

## pprof from the web

After configuring your process to run the pprof http server, access it via: http://localhost:6060/debug/pprof

Values were looked up directly from the source at: https://cs.opensource.google/go/go/+/master:src/runtime/pprof/pprof.go;drc=e0844acfc8baa57541a8efef723937c2733e0c99;l=598

Other references:

- https://github.com/google/pprof/blob/master/doc/README.md

## /allocs

```
heap profile: 2: 174489600 [2: 174489600] @ heap/1048576
1: 87244800 [1: 87244800] @ 0x4632e65 0x4632e42 0x463c43f 0x4685df8 0x4900966 0x41b971d 0x41b6df0 0x49027d5 0x4902791 0x403fff6 0x40775c1

# 0x4632e64 github.com/dgraph-io/badger/v2/skl.newArena+0x44 /Users/idawson/go/pkg/mod/github.com/dgraph-io/badger/v2@v2.0.3/skl/arena.go:48
# 0x4632e41 github.com/dgraph-io/badger/v2/skl.NewSkiplist+0x21 /Users/idawson/go/pkg/mod/github.com/dgraph-io/badger/v2@v2.0.3/skl/skl.go:128
# 0x463c43e github.com/dgraph-io/badger/v2.Open+0xa3e /Users/idawson/go/pkg/mod/github.com/dgraph-io/badger/v2@v2.0.3/db.go:345
# 0x4685df7 gitlab.com/browserker/store/database.Open+0x2d7 /Users/idawson/code/gitlab/secure/browserker/store/database/database.go:39
# 0x4900965 gitlab.com/browserker/clicmds.Run+0x305 /Users/idawson/code/gitlab/secure/browserker/clicmds/runner.go:62
# 0x41b971c github.com/urfave/cli/v2.(\*Command).Run+0x4dc /Users/idawson/go/pkg/mod/github.com/urfave/cli/v2@v2.2.0/command.go:164
# 0x41b6def github.com/urfave/cli/v2.(\*App).RunContext+0x80f /Users/idawson/go/pkg/mod/github.com/urfave/cli/v2@v2.2.0/app.go:306
# 0x49027d4 github.com/urfave/cli/v2.(\*App).Run+0x794 /Users/idawson/go/pkg/mod/github.com/urfave/cli/v2@v2.2.0/app.go:215
# 0x4902790 main.main+0x750 /Users/idawson/code/gitlab/secure/browserker/main.go:49
# 0x403fff5 runtime.main+0x255 /usr/local/Cellar/go/1.16.5/libexec/src/runtime/proc.go:225
```

heap profile: 2 (**in use objects**): 174489600 (**in use bytes**) [2 (**alloc'd objects**): 174489600 (**alloc'd bytes**)] @ heap/1048576 (**MemProfileRate**)

`--- starts the profile sample ---`

1 (**in use objects**): 87244800 (**in use bytes**) [1 (**alloc'd objects**): 87244800 (**alloc'd bytes**)] @ 0x4632e65 0x4632e42 ... 0x40775c1 (**all stack pointers**)

## /heap

```
heap profile: 56: 264082120 [56269: 639368088] @ heap/1048576
1: 87244800 [1: 87244800] @ 0x4632e65 0x4632e42 0x463c43f 0x4685df8 0x4900a6a 0x41b971d 0x41b6df0 0x49027d5 0x4902791 0x403fff6 0x40775c1
#	0x4632e64	github.com/dgraph-io/badger/v2/skl.newArena+0x44	/Users/idawson/go/pkg/mod/github.com/dgraph-io/badger/v2@v2.0.3/skl/arena.go:48
#	0x4632e41	github.com/dgraph-io/badger/v2/skl.NewSkiplist+0x21	/Users/idawson/go/pkg/mod/github.com/dgraph-io/badger/v2@v2.0.3/skl/skl.go:128
#	0x463c43e	github.com/dgraph-io/badger/v2.Open+0xa3e		/Users/idawson/go/pkg/mod/github.com/dgraph-io/badger/v2@v2.0.3/db.go:345
```

heap profile: 56 (**in use objects**): 264082120 (**in use bytes**) [56269 (**alloc'd objects**): 639368088 (**alloc'd bytes**)] @ heap/1048576 (**mem profile rate**)

1 (**in use objects**): 87244800 (**in use bytes**) [1 (**alloc'd objects**): 87244800 (**alloc'd bytes**)] @ 0x4632e65 0x4632e42 ... 0x41b6df0 0x403fff6 0x40775c1 (**stack addresses**)

## /blocks

```
--- contention:
cycles/second=2400003845
8020615600374 3342 @ 0x4050cda 0x465245a 0x40775c1
# 0x4050cd9 runtime.selectgo+0x639 /usr/local/Cellar/go/1.16.5/libexec/src/runtime/select.go:492
# 0x4652459 github.com/dgraph-io/badger/v2.(\*levelsController).runWorker+0x219 /Users/idawson/go/pkg/mod/github.com/dgraph-io/badger/v2@v2.0.3/levels.go:361
```

cycles/second=2400003845 (**runtime cycles per second**)

8020615600374 (**cycles blocked --i think--**) 3342 (**count of blocking events**) @ 0x4050cda 0x465245a 0x40775c1 (**stack addrs**)

## /mutex

--- mutex:
cycles/second=2400003845 (**runtime cycles per second**)

sampling period=1

2410862 (**cycles blocked**) 51 (**count of blocking events**) @ 0x4081628 0x45f3658 0x45efd8b 0x40775c1

## /go routines

goroutine profile: total 54 (**total number of active / running go routines**)
6 (**number of go routines spawned from this stack**) @ 0x4040425 0x4051597 0x461dab9 0x40775c1

0x461dab8 github.com/dgraph-io/badger/v2/y.(\*WaterMark).process+0x2b8 /Users/idawson/go/pkg/mod/github.com/dgraph-io/badger/v2@v2.0.3/y/watermark.go:229
