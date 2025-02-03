# Cysic Prover Setup Guide  

**⚠️ Pastikan Anda menjalankan dengan 2x RTX 4090 atau setara.**  
**🚫 Jangan gunakan single GPU.**  
**  Saya menggunakan Hyperbolic untuk menyewa VPS

https://app.hyperbolic.xyz/

1. Install Curl

```bash
sudo apt update && sudo apt upgrade -y
```

```bash
sudo apt install curl
```

2. Download dan Install Node
Ganti your-reward-address-here dengan alamat reward Anda sendiri:
```bash
sudo curl -L https://github.com/cysic-labs/phase2_libs/releases/download/v1.0.0/setup_prover.sh -o ~/setup_prover.sh && bash ~/setup_prover.sh your-reward-address-here
```

3. Install Firejail
```bash
sudo apt-get install firejail
```

4. Setup Supervisor
Supervisor digunakan untuk mengelola proses prover secara otomatis.

   4.1 Install Supervisor
```bash
sudo apt install -y supervisor
```
   4.2 Buat Direktori Log
```bash
mkdir -p /home/ubuntu/logs
```
   4.3 Konfigurasi Supervisor
Jalankan perintah berikut untuk membuat file supervisord.conf:
```bash
sudo echo '[unix_http_server]
file=/home/ubuntu/supervisor.sock ; Adjusted socket path

[supervisord]
logfile=/home/ubuntu/logs/supervisord.log ; New log location
logfile_maxbytes=50MB
logfile_backups=10
loglevel=info
pidfile=/home/ubuntu/supervisord.pid ; Adjusted PID file
nodaemon=false
silent=false
minfds=1024
minprocs=200
strip_ansi=true

[rpcinterface:supervisor]
supervisor.rpcinterface_factory=supervisor.rpcinterface:make_main_rpcinterface

[supervisorctl]
serverurl=unix:///home/ubuntu/supervisor.sock ; Updated socket path

[program:cysic-prover]
command=/home/ubuntu/cysic-prover/prover
numprocs=1
directory=/home/ubuntu/cysic-prover
priority=999
autostart=true
redirect_stderr=true
stdout_logfile=/home/ubuntu/cysic-prover/cysic-prover.log
stdout_logfile_maxbytes=1GB
stdout_logfile_backups=1
environment=LD_LIBRARY_PATH="/home/ubuntu/cysic-prover",CHAIN_ID="534352"' > supervisord.conf
```

5. Jalankan Prover
```bash
sudo firejail --rlimit-as=352629760 supervisord -c supervisord.conf
```

6. Cek Log Prover
```bash
sudo firejail --rlimit-as=352629760 supervisorctl tail -f cysic-prover
```
