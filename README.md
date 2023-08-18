# Sources Demo

This repository is meant to demonstrate the sources feature of Polar Signals.

## Prerequisites

* Go compiler
* llvm-dwarfdump
* parca-debuginfo
* (optional) parca-push

## Demo

First we need a binary, let's take the example `main.go`:

```
go build main.go
```

And we generate a source archive for it (gzip and zstd are supported compression mechanisms):

> Note: This command looks at debuginfo in the binary and finds all files on disk and archives them. For this to work, virtual files may have be ignored, like `grep` does in this one-liner.

```
llvm-dwarfdump --show-sources main | grep -v "./<autogenerated>" | tar -I zstd -cf source.tar.zstd -T -
```

Then upload the source archive to Polar Signals Cloud with the associated build ID of the binary:

```
parca-debuginfo upload --build-id="$(parca-debuginfo buildid ./main)" --type=sources source.tar.zstd --store-address=grpc.polarsignals.com:443 --bearer-token=<your token>
```

Now you could either run your binary in production and profile it with Parca Agent, or to quickly test out the demo with this example application, take the example profile in this repository and push it to Polar Signals Cloud using `parca-push`:

```
parca-push --remote-store-address=grpc.polarsignals.com:443 --remote-store-bearer-token=<your token> --labels=__name__=parca_agent_cpu pprof.pb.gz
```
