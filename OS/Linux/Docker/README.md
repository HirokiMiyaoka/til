# Docker周り色々

頑張ってdocxekrをインストールする。

# 大雑把な挙動とか関係

今のところの理解

```
+-----------------+
| Dockerのイメージ | 何処かにあるやつ
+-----------------+
 |
 |---------- ローカル
 |
 v
[docker pull](イメージのダウンロード)
 |
 v
+--------------------+
| Dockerのイメージたち |---->[docker images](表示)
+--------------------+
 |                ^
 v                |
[docker run]     [docker commit]
(イメージから     (加工したコンテナの
コンテナを作って   差分を新しいイメージ
起動)             として保存)
 |                |
 v                |
+-------------------+
| コンテナたち       |---->[docker ps](表示)
+-------------------+
 |                |
 v                v
[docker start]   [docker rm](削除)
(起動)
```

# コマンド

## イメージのダウンロード

```
docker pull IMAGE
```

## イメージの一覧

```
docker images
```

## イメージに名前をつけて実行

```
docker run -i -t --name NAME -p CONTPORT:HOSTPORT -h HOSTNAME IMAGE /bin/bash
```

`-i`をつけると中に入って作業ができる。後述の`docker start`でも同様。

実行したら色々インストールしたりして、それをイメージとして保存可能。

## 今動いてるコンテナの一覧

```
docker ps
```

## 最後に実行したコンテナ情報の表示及びIDだけ表示

```
docker ps -l
docker ps -l -q
```

## コンテナの状態をイメージに保存

```
docker commit ID IMAGE
```

## コンテナを起動

```
docker start -i IDとか名前
```
