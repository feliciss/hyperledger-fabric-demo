# Hyperledger Fabric Demo Tutorial

用于展示如何搭建 Hyperledger Fabric 及 Chaincode 相关目录。

## 第一步：下载必备软件

- [Git](https://git-scm.com/downloads)
- [cURL](https://curl.haxx.se/download.html)
- [Docker](docker.com/get-started) 和 [Docker Compose](https://docs.docker.com/compose/install/)
- Nodejs 和 npm (https://nodejs.org/)
- [Go](https://golang.org/)

Note: 根据你的系统平台安装以上软件。视频教程中的命令都是通过官网给出的命令安装的。例如在Ubuntu系统可以通过apt-get安装 Git 和 cURL：
```bash
sudo apt-get install curl git
```
以及在安装 Docker 后配置 Docker 命令，例如在 Ubuntu 上运行：
```bash
sudo usermod -a -G docker $USER
sudo systemctl start docker
sudo systemctl enable docker
```

如果你是 Windows 系统，那么在运行 `git clone` 之前，你需要先运行下面两段代码：
```bash
git config --global core.autocrlf false
git config --global core.longpaths true
```

## 第二步：安装案例文件，二进制程序，以及Docker镜像

命令：
```bash
curl -sSL https://bit.ly/2ysbOFE | bash -s -- 2.1.1 1.4.7 0.4.20
```

（可选）把二进制文件加入PATH：
```bash
export PATH=<这里填入你下载的全路径文件目录，例如：/home/ubuntu/fabric-samples>/bin:$PATH
```

## 第三步：使用Fabric测试网络

1. 进入测试网络：
    ```
    cd fabric-samples/test-network
    ```

2. 启动网络：
    ```
    ./network.sh up
    ```

    如果版本号报错，指定版本号，如视频里最新版2.1.1:

    ```
    ./network.sh up -i 2.1.1
    ```

    或者遇到权限问题可以加上sudo：

    ```
    sudo ./network.sh up -i 2.1.1
    ```

3. 创建Channel：

    ```
    ./network.sh createChannel
    ```

4. 发布Chaincode（链码）到Channel：
    ```
    ./network.sh deployCC
    ```

5. 通过链码方式与网络交互：

    把二进制执行文件加入PATH：
    ```
    export PATH=${PWD}/../bin:$PATH
    ```

    设置 Fabric 配置文件路径:
    ```
    export FABRIC_CFG_PATH=$PWD/../config/
    ```

    将peer0作为Org1的代表与链交互：
    ```
    export CORE_PEER_TLS_ENABLED=true
    export CORE_PEER_LOCALMSPID="Org1MSP"
    export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
    export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
    export CORE_PEER_ADDRESS=localhost:7051
    ```

    指定参数，作为Org1查询链码：
    ```
    peer chaincode query -C mychannel -n fabcar -c '{"Args":["queryAllCars"]}'
    ```

    作为Org1调用链码：
    ```
    peer chaincode invoke -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n fabcar --peerAddresses localhost:7051 --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt --peerAddresses localhost:9051 --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt -c '{"function":"changeCarOwner","Args":["CAR9","Dave"]}'
    ```

    换成设置Org2的peer0身份与链交互：
    ```
    export CORE_PEER_TLS_ENABLED=true
    export CORE_PEER_LOCALMSPID="Org2MSP"
    export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt
    export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp
    export CORE_PEER_ADDRESS=localhost:9051
    ```

    指定参数，作为Org2查询链码：
    ```
    peer chaincode query -C mychannel -n fabcar -c '{"Args":["queryCar","CAR9"]}'
    ```

    以上针对基本的Fabric网络操作结束，可以关闭网络：
    ```
    ./network.sh down
    ```