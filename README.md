不要点击链接，此仓库只做备份仅用于研究工作原理，请勿传播，仓库内任何有关观点的内容均非本人编撰
其中包含的任何内容产生的影响归原作者所有。
我会删除他们

## Intro

**ShadowsocksR-native** is a lightweight secured SOCKS5 proxy for embedded devices and low-end boxes.

## Features

ShadowsocksR-native is written in pure C and only depends on
[libuv](https://github.com/libuv/libuv) ,
[mbedTLS](https://github.com/ARMmbed/mbedtls) , 
[libsodium](https://github.com/jedisct1/libsodium) and
[json-c](https://github.com/json-c/json-c).

## Encrypto method
|                  |                  |                 |                 |                 |                |
| -----|-|-|-|-|-------------- | 
| none | table             |||||
| rc4 | rc4-md5-6 | rc4-md5 ||||
| aes-128-cfb | aes-192-cfb | aes-256-cfb ||||
| aes-128-ctr | aes-192-ctr | aes-256-ctr ||||
| camellia-128-cfb | camellia-192-cfb | camellia-256-cfb ||||
| bf-cfb | cast5-cfb | des-cfb | idea-cfb | rc2-cfb | seed-cfb |
| salsa20 | chacha20 | chacha20-ietf ||||
| | | | | | | 
| aes-128-gcm | aes-192-gcm | aes-256-gcm | chacha20-ietf-poly1305 | xchacha20-ietf-poly1305 ||


## Protocols & obfuscators

| Protocols | obfuscators | 
| --------- | ----------- | 
| origin | plain |
| auth_sha1_v4 | http_simple |
| auth_aes128_sha1 | http_post |
| auth_aes128_md5 | http_mix |
| auth_chain_a | tls1.2_ticket_auth |
| auth_chain_b | tls1.2_ticket_fastauth |
| auth_chain_c/d/e/f |    |

progress of data flow
```
+-----------------------------------------------------------------------------+
|                +--------------------------------------------------------+   |
|                |               +------------------------------------+   |   |
|                |               |            +-------------------+   |   |   |
|  obfuscator    |   encryptor   |  protocol  |     user data     |   |   |   |
|   |            |       |       |      |     +-------------------+   |   |   |
|   |            |       |       +------+-----------------------------+   |   |
|   |            +-------+--------------+---------------------------------+   |
+---+--------------------+--------------+-------------------------------------+
    |                    |              |                                            
    +-- server_encode    +-- encrypt    +-- server_pre_encrypt       <<<=== user data
    |                    |              |                                            
    +-- server_decode    +-- decrypt    +-- server_post_decrypt      ===>>> user data
```

## Build

### Distribution-specific guide

- [Debian & Ubuntu](#debian--ubuntu)

* * *

### Debian & Ubuntu
```bash
# Debian / Ubuntu
# sudo su                       # using root account
sudo apt-get update -y
sudo apt-get upgrade -y
sudo apt-get install --no-install-recommends build-essential autoconf libtool asciidoc xmlto -y
sudo apt-get install git gcc g++ gdb cmake automake -y
sudo apt-get -f install -y

# cd /                          # switch to root directory
git clone --recursive https://github.com/ShadowsocksR-Live/shadowsocksr-native.git ssr-n
cd ssr-n                      # enter ssr-n directory. 

# build ShadowsocksR-native
mkdir build && cd build
cmake .. && make
# make install
# /bin/cp -rfa src/ssr-* /usr/bin

# build 32-bit binary
sudo apt-get install gcc-multilib -y
rm -rf *
cmake -D CMAKE_C_FLAGS=-m32 .. && make

```

## Sample configure file

config.json
```json
{
    "password": "password",
    "method": "aes-128-ctr",
    "protocol": "auth_aes128_md5",
    "protocol_param": "",
    "obfs": "tls1.2_ticket_auth",
    "obfs_param": "",

    "udp": true,
    "idle_timeout": 300,
    "connect_timeout": 6,
    "udp_timeout": 6,

    "server_settings": {
        "listen_address": "0.0.0.0",
        "listen_port": 12475
    },

    "client_settings": {
        "server": "12.34.56.78",
        "server_port": 12475,
        "listen_address": "0.0.0.0",
        "listen_port": 1080
    },

    "over_tls_settings": {
        "enable": false,
        "server_domain": "goodsitesample.com",
        "path": "/udg151df/",
        "root_cert_file": ""
    }
}
```

## Deploy server

Supporting `CentOS 7` / `Debian` / `Ubuntu` with the following commands

```
sudo su
wget --no-check-certificate https://raw.githubusercontent.com/ShadowsocksR-Live/shadowsocksr-native/master/install/ssrn-install.sh
chmod +x ssrn-install.sh
./ssrn-install.sh 2>&1 | tee ssr-n.log
```

After installation, we can view the status with 
```
systemctl status ssr-native.service
```

And we can view or edit the configuration with `cat` or `vi` in `root` privilege
```
cat /etc/ssr-native/config.json
```

After we changed the server configuration, we must restart the service to make the changes take effect.
```
systemctl restart ssr-native.service
```

To stop the server, please run
```
systemctl stop ssr-native.service
```

To uninstall the server, use the following command
```
./ssrn-install.sh uninstall
```
      
