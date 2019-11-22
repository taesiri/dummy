# Random Commands

Some random commands accumulated form Google.

## Docker Goodies 

### Docker-Compose

```bash
curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```

### Apache

Run an Apache server on the current dir

```bash
docker run -dit --name my-apache -p 8080:80 -v "$PWD":/usr/local/apache2/htdocs/ httpd:2.4
```

**As a bash function**

```bash
ap () {docker run -dit --name $1 -p $2:80 -v "$PWD":/usr/local/apache2/htdocs/ httpd:2.4 ;}
```

Put the above line in your ``bashrc`` or ``zshrc`` file, Then run like this

```bash
ap my-apache 8800
```

### WebTorrent

Download a torrent with ``magnet`` link.

```bash
docker run --rm -it -v "$PWD":/dlbox pataquets/webtorrent-cli 'MAGNETHERE' -o /dlbox
```

**As a Bash function**:

```bash
wt () { docker run --rm -it -v "$PWD":/dlbox pataquets/webtorrent-cli $1 -o /dlbox; }
```

Put the above line in your ``bashrc`` or ``zshrc`` file, Then run like this

```bash
wt "MAGNETHERE"
```

## Proxy Goodies

### Apostle

```bash
git clone --depth=1 https://github.com/taesiri/Apostle
cd Apostle/server/shadowsocksR
docker-compose build
docker-compose up -d
```


```bash
## SERVER

docker run -d -p 47437:47437 taesiri/apostle-kcp -l 0.0.0.0:47437 -t 127.0.0.1:10003 --crypt aes-192 --key manmade

ssserver -p 10003 -k Google -m RC4-MD5

docker run -d -p 10003:10003 taesiri/apostle-ssserver -p 10003 -k Google -m RC4-MD5


## CLIENT

./client_linux_amd64 -l 0.0.0.0:47438 -r 8.9.5.239:47437 --crypt aes-192 --key manmade
sslocal -b 0.0.0.0 -l 1112 -s 127.0.0.1 -p 47438 -k Google -m RC4-MD5

## Network

docker network create -d bridge kcpbridge
docker run --rm --network=kcpbridge -p 47437:47437 taesiri/apostle-kcp -l 0.0.0.0:47437 -t 127.0.0.1:10003 --crypt aes-192 --key manmade
docker run --rm --network=kcpbridge -p 10003:10003 taesiri/apostle-ssserver -p 10003 -k Google -m RC4-MD5


## Clean UP!

docker system prune

docker run -d -p 8388:8388 -p 29900:29900/udp zetaplusae/shadowsocks-kcptun \
    -m 'aes-256-cfb' -p 'keyword' \
    -s "nano" -c 'aes'

./client_linux_amd64 -l 0.0.0.0:29900 -r 8.9.5.239:29900 --crypt aes --key nano
sslocal -b 0.0.0.0 -l 8388 -s 127.0.0.1 -p 29900 -k keyword -m aes-256-cfb


docker run -d -p 8389:8388 -p 29901:29900/udp zetaplusae/shadowsocks-kcptun \
    -m 'aes-256-cfb' -p 'wordphrase' \
    -s "micro" -c 'aes'


./client_linux_amd64 -l 0.0.0.0:29901 -r 8.9.5.239:29901 --crypt aes --key micro

./client_linux_arm7 -l 0.0.0.0:29901 -r 8.9.5.239:29901 --crypt aes --key micro


sslocal -b 0.0.0.0 -l 8389 -s 127.0.0.1 -p 29901 -k wordphrase -m aes-256-cfb





docker run -d -p 6666:8388 -p 6665:29900/udp mtaesiri/apostle-ssskcptun -m 'aes-256-cfb' -p 'kindle' -s "grindle" -c 'aes'
```

### ShadowSocks Server - 1 Liners

```bash
ssserver -p 9399 -k Yahoo -m RC4-MD5
./server_linux_amd64 --listen 0.0.0.0:3018 --target 127.0.0.1:9399 --crypt aes-192 --key halo
ssserver -p 33 -k Istartedaserver -m aes-256-cfb
ssserver -c shadow.config
ssserver -p 31 -k Istartedaserver -m aes-256-cfb


ssserver -p 80 -k Istartedaserver -m rc4-md5
ssserver -p 8388 -k Istartedaserver -m rc4-md5
```

### ShadowSocks Client (MacOS)

```bash
    ./client_darwin_amd64 -l 0.0.0.0:9399 -r 149.28.62.211:3018 --crypt aes-192 --key halo
```

### ShadowSocks Client (Armbian)

```bash
    ./client_linux_arm7 -l 0.0.0.0:9399 -r 149.28.62.211:3018 --crypt aes-192 --key halo

    sslocal -b 0.0.0.0 -l 1111 -s 127.0.0.1 -p 9399 -k Yahoo -m RC4-MD5
```

### OpenVPN KCPTune

```bash
./server_linux_amd64 --listen 0.0.0.0:1195 --target 127.0.0.1:1194 --crypt aes --key halo
./client_linux_amd64 -l 0.0.0.0:1194 -r 8.9.5.239:1195 --crypt aes --key halo
```



### Reroute Everything - RedSocks

Reroute all network traffics through RedSocks.

```bash
sudo apt-get install iptables git libevent-dev
git clone http://github.com/darkk/redsocks.git
cd redsocks/
make
echo 'base{log_debug = on; log_info = on; log = "file:/tmp/reddi.log";
        daemon = on; redirector = iptables;}
        redsocks { local_ip = 127.0.0.1; local_port = 31338; ip = 192.168.0.8;
        port = 1111; type = socks5; }' > redsocks.conf



echo 'base{log_debug = on; log_info = on; log = "file:/tmp/reddi.log";
        daemon = on; redirector = iptables;}
        redsocks { local_ip = 127.0.0.1; local_port = 31338; ip = 192.168.0.8;
        port = 8389; type = socks5; }' > redsocks.conf


########################################################
########################################################
#### Start ####

./redsocks -c redsocks.conf

sudo iptables -t nat -N REDSOCKS
    
# Ignore LANs and some other reserved addresses.
sudo iptables -t nat -A REDSOCKS -d 0.0.0.0/8 -j RETURN
sudo iptables -t nat -A REDSOCKS -d 10.0.0.0/8 -j RETURN
sudo iptables -t nat -A REDSOCKS -d 127.0.0.0/8 -j RETURN
sudo iptables -t nat -A REDSOCKS -d 169.254.0.0/16 -j RETURN
sudo iptables -t nat -A REDSOCKS -d 172.16.0.0/12 -j RETURN
sudo iptables -t nat -A REDSOCKS -d 224.0.0.0/4 -j RETURN
sudo iptables -t nat -A REDSOCKS -d 240.0.0.0/4 -j RETURN
sudo iptables -t nat -A REDSOCKS -d 192.168.0.0/16 -j RETURN
    
sudo iptables -t nat -A REDSOCKS -p tcp -j REDIRECT --to-ports 31338
    
sudo iptables -t nat -A OUTPUT -p tcp -m owner --uid-owner root -j REDSOCKS
sudo iptables -t nat -A OUTPUT -p tcp -m owner --uid-owner taesiri -j REDSOCKS


#### STOP ####
sudo iptables -F
sudo iptables -X 
sudo iptables -Z
sudo iptables -t nat -F
sudo iptables -t nat -X
sudo iptables -t nat -Z
killall redsocks

## enp5s0 is connected to Internet
## enp4s0 is connected to local Netwrok which desperately wants internet

sudo iptables -t nat -A POSTROUTING -o enp5s0 -j MASQUERADE
sudo iptables -A FORWARD -i enp5s0 -o enp4s0 -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
sudo iptables -A FORWARD -i enp4s0 -o enp5s0 -j ACCEPT
```

```bash
## enp5s0 is connected to Internet
## enp4s0 is connected to local Netwrok which desperately wants internet

sudo iptables -t nat -A POSTROUTING -o enp5s0 -j MASQUERADE
sudo iptables -A FORWARD -i enp5s0 -o enp4s0 -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
sudo iptables -A FORWARD -i enp4s0 -o enp5s0 -j ACCEPT
```

## Linux Goodies

### Ports and Connections

* [Source 1](https://serverfault.com/questions/421310/check-the-number-of-active-connections-on-port-80)

* [Source 2](https://unix.stackexchange.com/questions/36169/how-to-list-all-unique-ip-address-currently-connected-to-a-specific-port)

Check Number of Active (``ESTABLISHED``) connections on given port.

```bash
netstat -anp | grep :8000 | grep ESTABLISHED | wc -l
```

If you want to get all types of Connection, try this one:

```bash
netstat -ant | grep :8000 | awk '{print $6}' | sort | uniq -c | sort -n
```

List all unique IPs connected to specific port

```bash
netstat -nt | awk '/:8088.*ESTABLISHED/ { split ($5, a, ":"); print a[1] }' | uniq 
```

Force unique sort

```bash
netstat -nt | awk '/:443.*ESTABLISHED/ { split ($5, a, ":"); print a[1] }'  | sort -n | uniq 
```


### Proxy Tools

Socks to HTTP Using Delegate

```bash
dg9_9_13 -P8080 SERVER=http SOCKS=127.0.0.1:3000 ADMIN="local@localhost.com"
```

Socks to HTTP Using Privoxy

```
## config file for privoxy
forward-socks5 / 127.0.0.1:3000  .
listen-address 127.0.0.1:8118
```


### Restart into Windows form Ubuntu

[Creating a "Reboot into Windows" Button in Ubuntu | rastating.github.io](https://rastating.github.io/creating-a-reboot-into-windows-button-in-ubuntu/)

### Ubuntu 18.10 DNS Trick

Shitty fix for a shitty problem!

```bash
sudo rm /etc/resolv.conf
sudo mkdir -p /run/resolvconf/
sudo ln -s /run/resolvconf/resolv.conf /etc/resolv.conf

## modify /run/resolvconf/resolv.conf
```

### Setup a Swap

```bash
sudo fallocate -l 1G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

### Run a job with Cron at startup

```bash
sudo crontab -e
@reboot /path/to/script
```

### Fix Window Buttons on Ubuntu

```bash
gsettings set org.gnome.desktop.wm.preferences button-layout "close,maximize,minimize:"
```

### Fast Kill

```bash
lsof -wni tcp:4000
kill -9 <PID of process>
```

### GCC Version Switch

```bash
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-6 10


sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-6 10


## After that:

sudo update-alternatives --config gcc
sudo update-alternatives --config g++
```

## FFMPEG

### Images to Video

```bash
!ffmpeg -r 16 -i /PATH/TO/IMAGES/images-%4d.png  test.mp4
```

### Remove Letterbox

[Source](https://superuser.com/questions/810471/remove-mp4-video-top-and-bottom-black-bars-using-ffmpeg)

Extract crop information

```bash
ffmpeg -ss 90 -i input.mp4 -vframes 10 -vf cropdetect -f null -
```

Preview the file:

```bash
ffplay -vf crop=1280:720:0:0 input.mp4
```

Re-encode with crop filter

```bash
ffmpeg -i input.mp4 -vf crop=1280:720:0:0 -c:a copy output.mp4
```

## Python Goodies

### IPython

Register a kernel

```python
python -m ipykernel install --user --name MYENV --display-name "DISPLAYNAME"
```

### OpenCV + FFMPEG

```bash
conda install -c https://conda.binstar.org/jaimeivancervantes opencv
```

### Tensorflow

Is GPU Available?

```python
import tensorflow as tf
print(tf.test.is_gpu_available())
```

```bash
python -c "import tensorflow as tf; print(tf.test.is_gpu_available())"
```

Print all GPUs

```python
from tensorflow.python.client import device_lib

def get_available_gpus():
    local_device_protos = device_lib.list_local_devices()
    return [x.name for x in local_device_protos if x.device_type == 'GPU']

get_available_gpus()
```

Is GPU Working 1

```python
import tensorflow as tf
with tf.device('/gpu:0'):
        a = tf.constant([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], shape=[2, 3], name='a')
        b = tf.constant([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], shape=[3, 2], name='b')
        c = tf.matmul(a, b)
with tf.Session() as sess:
    print (sess.run(c))
```

Is GPU Working 2

```python
import sys
import numpy as np
import tensorflow as tf
from datetime import datetime

shape = (32,32)

device_name = "/gpu:0"

with tf.device(device_name):
    random_matrix = tf.random_uniform(shape=shape, minval=0, maxval=1)
    dot_operation = tf.matmul(random_matrix, tf.transpose(random_matrix))
    sum_operation = tf.reduce_sum(dot_operation)


startTime = datetime.now()
with tf.Session(config=tf.ConfigProto(log_device_placement=True)) as session:
        result = session.run(sum_operation)
        print(result)

# It can be hard to see the results on the terminal with lots of output -- add some newlines to improve readability.
print("\n" * 5)
print("Shape:", shape, "Device:", device_name)
print("Time taken:", datetime.now() - startTime)

print("\n" * 5)
```
