# nginx-layer4-lb
Nginx Setup for Layer 4 (Transport Layer) Load Balancer and avoid TLS offloading.

## Advantage of using Layer 4 Load Balancer
- A single TCP connection end-to-end - from client to backend server
- No SSL offloading - this is important when server needs to authenticate a client using `X.509` certificate and extract user information from `CN` section.
- Capture the requst/response payload size for each request

## Limitations
- Only origin based _sticky-session_ can be achieved


For `layer 4` load balancing `Nginx` requires `stream module`. Stream module is not part of `Nginx` build, but it is part of `NginxPLUS` build.

To use stream capability without going for `NginxPLUS` you need to download `Nginx` Source Code and build using `--with-stream_realip_module` compiler flag. This wil add stream dynamic module to the installer.

## Download Source Code and Build

#### Download Source Code
    wget https://nginx.org/download/nginx-1.19.1.tar.gz && tar zxvf nginx-1.19.1.tar.gz

#### Configure Stream Module
    cd nginx-1.19.1
    ./configure \
    	--user=aloksingh \
        --prefix=/usr/local/nginx_l4 \
        --with-stream=dynamic \
        --with-stream_realip_module

#### Build the Installer
    make install

## Nginx Setup
Please refer `nginx.conf` file under `conf` directory.

## Hit the Secure URL
    curl --cacert rootCA_Alok.crt --key clientAlok.key --cert clientAlok.crt https://localhost:443/api/user
    
   Where:
   - `rootCA_Alok.crt` is a backend server Root Certificate for `localhost`
   - `clientAlok.key` is client private key
   - `clientAlok.crt` is client certificate
   - Assuming the `clientAlok.crt` signed using `Root Certificate` that is available at Server's `Trust Store`. So that server can trust the client certificate.
