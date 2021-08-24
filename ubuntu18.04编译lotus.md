# How to build lotus on ubuntu 18.04

- 所需库文件

  ```shell
  sudo apt install mesa-opencl-icd ocl-icd-opencl-dev gcc git bzr jq pkg-config curl clang build-essential hwloc libhwloc-dev wget -y
  ```

- 下载安装go

  ```shell
  # set proxy to download 
  wget -c https://golang.org/dl/go1.16.3.linux-amd64.tar.gz
  sha256sum go1.16.3.linux-amd64.tar.gz
  #951a3c7c6ce4e56ad883f97d9db74d3d6d80d5fec77455c6ada6c1f7ac4776d2
  sudo chown -R root:root ./go
  sudo mv go /usr/local
  mkdir -p ~/go_work
  vim ~/.bashrc
  #增加下面内容到bashrc
  export GOPATH=$HOME/go_work
  export PATH=$PATH:/usr/local/go/bin:$GOPATH/bin
  export GOPROXY=https://goproxy.cn
  source ~/.bashrc
  
  sudo apt-get install rust
  
  #20.04
  sudo apt install rustc
  ```

- Build source

* Get code
```shell
git clone git://github.com/filecoin-project/lotus.git
git checkout v1.11.1 -b v1.11.1


make;sudo make install

  最后运行make即可生成lotus，lotus-miner，lotus-miner

- 创建钱包

  ```shell
  lotus daemon
  # 生成secp256k1 椭圆加密算法钱包,bitcoin上应用的算法
  lotus wallet new
  
  #生成BLS算法钱包
  lotus wallet new bls
  
  # 生成多重加密算法钱包
  lotus msig create address1 address2..
  
  #具体的算法请查阅相关wiki文档
  ```

Reference:

https://docs.filecoin.io/get-started/lotus/send-and-receive-fil/
