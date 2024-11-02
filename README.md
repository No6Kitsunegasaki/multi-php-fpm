# これはなんですか
Docker上で、CentOS 8 + nginx + 複数のバージョンのphp-fpmを動作させたサンプル

# どうやりますか
```
$ cd docker/centos84-systemd 
$ bash build.sh 
$ cd ../../
$ docker compose up -d
```

## 使用PHPバージョン確認
```
$ curl localhost/index.php
7.4.33%
```

## 使用PHPバージョンを8.1に切り替え
```
$ docker exec multi-php-fpm-main-1 bash /scripts/use_php81.sh 

$ curl localhost/index.php
8.1.30%
```