# docker_network

none：對於此容器，禁用所有網絡。通常與自定義網絡驅動程序一起使用。none不適用於群組服務。

host：對於獨立容器，刪除容器和Docker主機之間的網絡隔離，並直接使用主機的網絡。host 僅適用於Docker 17.06及更高版本的swarm服務。

bridge：默認網絡驅動程序。如果未指定驅動程序，則這是您要創建的網絡類型。當您的應用程序在需要通信的獨立容器中運行時，通常會使用橋接網絡。

overlay：覆蓋網絡將多個Docker守護程序連接在一起，並使群集服務能夠相互通信。您還可以使用覆蓋網絡來促進群集服務和獨立容器之間的通信，或者在不同Docker守護程序上的兩個獨立容器之間進行通信。此策略消除了在這些容器之間執行OS級別路由的需要。

macvlan：Macvlan網絡允許您為容器分配MAC地址，使其顯示為網絡上的物理設備。Docker守護程序通過其MAC地址將流量路由到容器。macvlan 在處理期望直接連接到物理網絡的傳統應用程序時，使用驅動程序有時是最佳選擇，而不是通過Docker主機的網絡堆棧進行路由。

網絡插件：您可以使用Docker安裝和使用第三方網絡插件。這些插件可從 Docker Hub 或第三方供應商處獲得。

## 測試none

none网络意味着没有网络，所创建的容器只有lo，没有其他的网卡。

none网络没有办法联网，封闭的网络能很好的保证容器的安全性，

为用户提供了最大的灵活性，使得用户可以创建并使用自己的复杂网络。

參考網址 https://www.jianshu.com/p/c461c7867de0

```
docker run -it --net=none joffotron/docker-net-tools

查看網路
ifconfig
```

![](none-network)

## 測試host

host网络意味着容器与宿主机共用一套网络，也就是说容器使用的网络就是宿主机的网络。

使用--network=host指定使用host网络。

參考網址 https://www.jianshu.com/p/c461c7867de0

```
docker run -it --net=host joffotron/docker-net-tools

查看網路
ifconfig
```

![](host-network)

## 測試bridge

預設就是使用此網路模式，這種網路模式就像是 NAT 的網路模式，

例如實體主機的 IP 是 192.168.1.10 它會對應到 Container 裡面的 172.17.0.2

在啟動 Docker 的 service 時會有一個 docker0 的網路卡就是在做此網路的橋接

參考網址 https://kknews.cc/other/6bm9gpl.html

先看docker的ifconfig

```
進入docker
docker-machine ssh

查看網路
ifconfig
```

![](docker-network)

```
兩句相等(因為預設)
docker run -it --net=bridge joffotron/docker-net-tools
docker run -it  joffotron/docker-net-tools

查看網路
ifconfig
```

![](bridge-network)

## 測試overlay

Host1 實體主機裡面有 Container1，然後 Host2 實體主機裡面有 Container2，可以透過 Docker Overlay 的 Network 的模式

將 Container1 和 Container2 連接起來做溝通。

另外 Consol 是一個存放連線資訊的資料庫，在使用 overlay 時必需要在 Docker 設定，

這樣才能存放 overlay 網路模式的連線資訊。

## 卡關 ~~~

```
vi /etc/docker/daemon.json
```

設定

```
{
  "live-restore": true,
  "group": "dockerroot",
  "hosts": [
     "unix:///var/run/docker.sock",
     "tcp://10.1.0.221:2375"
  ],
  "cluster-store": "consul://10.1.0.221:8500",
  "cluster-advertise": "enp8s0:2375"
}
```
docker 無法重啟

```
systemctl restart docker
```
## docker Swarm & docker k8s


參考網址 https://ithelp.ithome.com.tw/articles/10193457
