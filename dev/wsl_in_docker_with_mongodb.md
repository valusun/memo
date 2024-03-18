# 雑にwsl環境にdockerを入れてmongoDBを動かす

下記の通りAlmaLinux環境  

```text
[valusun@:~]$ cat /etc/os-release
NAME="AlmaLinux"
VERSION="9.3 (Shamrock Pampas Cat)"
```

## Dockerのインストール

[この手順](https://docs.docker.com/engine/install/rhel/)にしたがってインストールする  
`sudo docker run hello-world`で`Hello from Docker!`が出ればOK  

### Dockerインストール時のエラー対応

1. `sudo yum install ...` で下記のエラーが出た場合(都合により所々省いている)
    ```text
    Docker CE Stable - x86_64
    Errors during downloading metadata for repository 'docker-ce-stable':
        - Status code: 404 for https://download.docker.com/linux/rhel/9/x86_64/stable/repodata/repomd.xml 
    Error: Failed to download metadata for repo 'docker-ce-stable':
    ```

    下記のコマンドを叩いてリポジトリの登録を行う
    ```text
    sudo yum config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo Adding repo from: https://download.docker.com/linux/centos/docker-ce.repo
    ```

2. `sudo systemctl start docker`で下記のエラーが出た場合
    ```text
    System has not been booted with systemd as init system (PID 1). Can't operate.
    Failed to connect to bus: Host is down
    ```

    `sudo vi /etc/wsl.conf`に下記の設定を追加する(別にvimである必要はない)
    ```txt
    [boot]
    systemd=true
    ```

    設定後にwslを再起動させる

### Dockerをrootユーザで動かしたくない(いちいち`sudo`を叩きたくない)場合の設定

[この手順](https://docs.docker.com/engine/install/linux-postinstall/)に従う  

## mongoDBをdocker環境に構築する

`docker-compose.yml`を作成する。  

```yml
version: '3.1'

services:
  mongo:
    image: mongo
    container_name: mongo_db
    restart: always
    environment:
      MONGO_INITDB_ROOT_USERNAME: root
      MONGO_INITDB_ROOT_PASSWORD: password
    ports:
      - 27017:27017
    volumes:
      - ./mongodb_data:/data/db
      - ./configdb:/data/configdb

  mongo-express:
    image: mongo-express
    container_name: mongo_express
    restart: always
    ports:
      - 8081:8081
    environment:
      ME_CONFIG_MONGODB_ADMINUSERNAME: root
      ME_CONFIG_MONGODB_ADMINPASSWORD: password
      ME_CONFIG_MONGODB_SERVER: mongo
    depends_on:
      - mongo
```

作成したymlが存在するディレクトリで、 `docker compose up -d`を叩く。  
その後に、localhostの8081にアクセスして、mongodb-expressが動くことを確認  
