# docker-mirakurun-chinachu
Mirakurun と Chinachu をDockerコンテナに閉じ込めました。

[公式実装](https://github.com/Chinachu/docker-mirakurun-chinachu) をベースに、
2018年8月30日現在 CentOS 6 しかサポートOSがない PX-W3PE を利用することを念頭に、
CentOS 6 上 で動くように改変しています。

## Constitution
### Mirakurun
- Alpine Linux 3.6(node:8.9-alpine)
- [Mirakurun](https://github.com/kanreisa/Mirakurun)
  - branch: master

### Chinachu
- Alpine Linux 3.6(node:8.9-alpine)
- [Chinachu](https://github.com/kanreisa/Chinachu)
  - branch: gamma

## 動作確認環境
> OS
>>CentOS Linux release 6.10 (Core)  
>> Linux 2.6.32-754.3.5.el6.x86_64

>Docker
>>version 1.7.1, build 786b29d

>Tuner
>>ISDB-S, ISDB-T Tuner PX-W3PE rev.1

>Smart card reader
>>USB SmartCard Reader NTT Communications Corp. SCR3310-NTTCom  

## 利用方法
- CentOS 6 サポートの最後のバージョンの docker & docker-compose がインストール済
- SELinuxの無効化推奨
- ホストマシンにPX-W3PE Linux Driverがインストール済
```
$ ls -l /dev/asv*
crwxr-xr-x 1 root root 244, 0  8月 30 22:19 2018 /dev/asv52200
crwxr-xr-x 1 root root 244, 1  8月 30 22:19 2018 /dev/asv52201
crwxr-xr-x 1 root root 244, 2  8月 30 22:19 2018 /dev/asv52202
crwxr-xr-x 1 root root 244, 3  8月 30 22:19 2018 /dev/asv52203
```
- B-CAS 用に利用するスマートカードリーダーはMirakurunコンテナ内で管理しますので  
ホストマシン上のpcscdは停止してください
```
sudo service pcscd stop
sudo chkconfig pcscd off
```

- docker-composeを利用しておりますので、プロジェクトディレクトリ内で下記コマンドを実行してください  
プロジェクトディレクトリ名はビルド時のレポジトリ名になりますので、適当に短いフォルダ名が推奨です

### 取得例
```shell
git clone https://github.com/yanbe/docker-mirakurun-chinachu.git tvs
cd tvs
```
### 起動
```shell
docker-compose up -d
```
### 停止
```shell
docker-compose down
```

### デーモン化(SysV Init)
```shell
cp docker-mirakurun-chinachu /etc/init.d
## 永続化(次回OS起動時に自動で起動)
chkconfig --add docker-mirakurun-chinachu

# 手動起動
service docker-mirakurun-chinachu start

# 動作確認
service docker-mirakurun-chinachu status

# 手動停止
service docker-mirakurun-chinachu stop
```

## 設定
エリア、環境によって変更が必要なファイルは下記の通りとなります
### Mirakurun
- ポート番号 : 40772
- mirakurun/conf/tuners.yml  
チューナー設定
- mirakurun/conf/channels.yml  
チャンネル設定

### Chinachu
- ポート番号 : 10772, 20772(local network only), 5353/udp(mDNS)
- chinachu/conf/config.json  
チューナー設定  
チャンネル設定

### 録画ファイル保存先
また録画ファイルはプロジェクトフォルダ内の./recordedに保存されます  
> 保存先を別HDDにしたい場合は、docker-compose.ymlの
>> /var/lib/chinachu/recorded:/usr/local/chinachu/recorded
>
> の./recordedを変更することで保存先を変更可能

## License
This software is released under the MIT License, see LICENSE.
