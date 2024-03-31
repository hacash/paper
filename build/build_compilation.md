# Compilation instructions (linux-like platforms)

Hacash in written in golang with a crypto hash library x16rs written in C for performance and stability.
There are also some web interface and explorer functionality written in javascript, however that is
only required for the web site functionality or  if you want to implement your own block explorer.
This guide is currently only for linux-like platforms.

The golang  source is portable across a wide range of platforms and has been tested
on linux ubuntu, centos, macosx (with homebrew) and armhf (raspberry pi. ).

In principle hacash should work on any platform which supports golang, and can compile the
x16rs library.  The author has used gcc >= 6.3 and cmake >= 3.7.2 and go1.15.2 however your mileage may vary.

## Outline to compile Hacash

1. install c development compiler (gcc >=6.3 is known to be good)
2. install golang
3. download the source from github


```bash
mkdir -p ~/go/src/github.com/hacash
cd ~/go/src/github.com/hacash
for d in blockmint  chain  cmdwallet  core  explorer  miner  mint  node service wallet  web_base_framework  www x16rs \
	pcwallet jssdk channelpay paper PaymentChannelChainNetServiceNodeRoutes bitcoin web_base_framework;
do
git clone https://github.com/hacash/${d}.git
done
```

You can move the source and set your own $GOROOT and $GOPATH, however using the existing
file structure will work first time and update cleanly.

You can also update the git source at any time using the following script -

```bash
cd ~/go/src/github.com/hacash
for d in blockmint  chain  cmdwallet  core  explorer  miner  mint  node service wallet  web_base_framework  www x16rs \
	pcwallet jssdk channelpay paper PaymentChannelChainNetServiceNodeRoutes bitcoin web_base_framework;
do
cd ${d}
git pull
cd ..
done
```

## Compile the X16RS library
This step is only necessary if you are compiling on a new architecture (different from x86_64) or you want to attempt to optimize the library.


```bash
cd ~go/src/github.com/hacash/x16rs
mv libx16rs_hash.a libx16rs_hash.a-old
cd sha3
for f in *.c ; do ofile=$(basename ${f});  gcc -c -o ${ofile}.o -Ofast -march=native ${f} ; done
cd ../sha3_256
for f in *.c ; do ofile=$(basename ${f});  gcc -c -o ${ofile}.o -Ofast -march=native ${f} ; done
cd ..
ar qs libx16rs_hash.a sha3/*.o sha3_256/*.o
```

This should result in a new library ```libx16rs_hash.a``` which contains the
optimized hash functions used by hacash go executables.


## Overview of the go executables

There are 3 main executables

1. The hacash node and miner  (miner.exe)
2. The command line wallet (cmdwallet.exe)
3. The pool worker miner (poolworker.exe)

miner.exe is the hacash blockchain full node and is the core of the currency as it also
implements the p2p node interface and blockchain management, the mining application for hac and the hacd diamonds, and also a basic poolminer application with
http interface.  The miner executable also supports legacy and new rpc api's to the hacash blockchain node. (ports 3338 and 8083)

cmdwallet.exe is command line hacash blockchain wallet which allows simple creation
of addresses with public and private keys and allows transactions to be constructed
and submitted to the mempool of a hacash blockchain node, and so broadcast to the network
for mining into a block.

poolworker.exe is a pool mining worker which only connects to a hacash mining pool
and attempts to mine blocks.

Behaviour of each executable is configured by a simple config file in ini format and
normally the executables are started with config file as argument.

## Compile the go executables

Note regarding environment variable GO111MODULE, if you are running Go version greater than Go 1.17, Go will ignore environment variable GO111MODULE. If your Go version is less than Go 1.17, then you can follow the following note.

Please refer to document https://go.dev/blog/go116-module-changes for specifics regarding environment variable GO111MODULE

Note to set the GO111MODULE="auto" environment variable at compile time:

```cgo
// compile NOTE: set go module env config
GO111MODULE="auto"
```

Start to compile:

```
cd ~/go/src/github.com/hacash

1. poolworker
 go build -ldflags '-w -s' -o poolminer_2021-0410 miner/run/minerpoolworker/main.go

2. miner
go build -ldflags '-w -s' -o miner_2021_02_12 miner/run/main/main.go

3. cmdwallet
go build -ldflags '-w -s' -o cmdwallet_2021_02_12 cmdwallet/run/main/main.go

```

That's it :  see other documentation under hacash.org for further information on
how to run and configure these executables.

