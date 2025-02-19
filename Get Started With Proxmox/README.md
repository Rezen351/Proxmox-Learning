# Panduan Membuat Server Sendiri dengan Proxmox dan ZeroTier

### Spesifikasi Komputer
- **Prosesor**: Intel Core i5 11400F
- **Grafis**: GTX 1660 Super
- **RAM**: 24 GB
- **Penyimpanan**: SSD 128 GB, HDD 1TB+

### Aplikasi yang Digunakan dan Dipelajari
1. Hyper-V
2. Proxmox
3. Mikrotik
4. Ubuntu
5. ZeroTier
6. Nextcloud

---

## 1. Started and Installation proxmox

### 1.1 Install Proxmox
Untuk menginstal Proxmox VE (Virtual Environment):

1. **Download Proxmox VE ISO** dari situs resmi [Proxmox](https://www.proxmox.com).
2. Gunakan alat seperti `Rufus` atau `balenaEtcher` untuk membuat bootable USB.
3. Boot komputer Anda menggunakan USB yang telah dibuat dan ikuti instruksi untuk menginstal Proxmox.
### 1.2 Install Proxmox Backup Server
Proxmox Backup Server akan digunakan untuk menyimpan cadangan data dan VM.

1. Update dan install Proxmox Backup Server:
    ```bash
    apt update
    apt install proxmox-backup-server
    ```

### 1.3 Konfigurasi Jaringan Internal dan WAN
Pastikan untuk mengonfigurasi jaringan internal dan WAN agar Proxmox dan backup server dapat terhubung.

---

## 2. Konfigurasi Nextcloud Server dengan Ubuntu

### 2.1 Install Ubuntu pada Proxmox
1. Buat sebuah **Virtual Machine** baru di Proxmox dengan sistem operasi Ubuntu.
2. Download ISO Ubuntu terbaru dari [Ubuntu Downloads](https://ubuntu.com/download/desktop).
3. Ikuti langkah-langkah untuk menginstal Ubuntu pada mesin virtual.

### 2.2 Instalasi Nextcloud
Setelah Ubuntu terpasang, install **Nextcloud** dengan mengikuti langkah-langkah berikut:

1. Install Apache2 dan PHP:
    ```bash
    sudo apt update
    sudo apt install apache2 php libapache2-mod-php php-mysql php-zip php-gd php-mbstring php-curl php-xml php-bz2 unzip -y
    ```

2. Download Nextcloud dan ekstrak:
    ```bash
    wget https://download.nextcloud.com/server/releases/nextcloud-<version>.zip -O /tmp/nextcloud.zip
    sudo unzip -q /tmp/nextcloud.zip -d /var/www/
    sudo chown -R www-data:www-data /var/www/nextcloud
    ```

3. Konfigurasi Apache untuk Nextcloud:
    ```bash
    sudo nano /etc/apache2/sites-available/nextcloud.conf
    ```
    Masukkan konfigurasi berikut:
    ```
    Alias /nextcloud "/var/www/nextcloud/"
    <Directory /var/www/nextcloud/>
        Options +FollowSymlinks
        AllowOverride All
        Require all granted
    </Directory>
    ```

4. Aktifkan konfigurasi dan restart Apache:
    ```bash
    sudo a2ensite nextcloud
    sudo systemctl restart apache2
    ```

---

## 3. Konfigurasi Jaringan dengan ZeroTier

ZeroTier memungkinkan Anda untuk membuat jaringan virtual pribadi (VPN) untuk menghubungkan perangkat secara aman melalui internet.

### 3.1 Instalasi ZeroTier pada Ubuntu
Untuk menghubungkan Ubuntu ke jaringan ZeroTier, lakukan instalasi dengan perintah berikut:

```bash
curl -s https://install.zerotier.com | sudo 
```
Setelah itu, hubungkan ke jaringan zerotier
```bash
sudo zerotier-cli join "Network ID"
```
### 3.2 Konfigurasi Routing dan IPv4 Forwading
Untuk mengkonfigurasi node keluar zerotier, pastikan IPv4 Forwading diaktifkan
1. edit file sysclt.conf
 ```bash
 sudo nano /etc/sysctl.conf
 ```
2. Tambahkan baris berikut:
```bash
net.ipv4.ip_forward = 1
```
3. Terapkan pengaturan:
```bash
sudo sysctl -p
```
4. Konfigurasi NAT dan forwarding dengan iptables:

```bash
sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
sudo iptables -A FORWARD -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
sudo iptables -A FORWARD -i zt0 -o eth0 -j ACCEPT
```
5. Install iptables-persistent untuk menyimpan aturan:
```bash
Copy code
sudo apt-get install iptables-persistent
sudo netfilter-persistent save
```
6. Tambahkan rute default ke ZeroTier Network: Di panel ZeroTier, pada tab Managed Routes, tambahkan rute:
```php
0.0.0.0/0 via <zerotier-ip-of-router-node>
```
## 4. Konfigurasi Proxmox untuk Penyimpanan dan Backup
### 4.1 Setup Disk untuk VM dan Backup
Buat penyimpanan ZFS di Proxmox:

```bash
zpool create -f vm-storage /dev/sdd
```
Buat volume ZFS untuk mesin virtual:

```bash
zfs create storage1/vmc
```
Install mesin virtual Ubuntu dan Windows 7 di Proxmox.

### 4.2 Setup Kontainer MySQL
Untuk membuat kontainer dengan MySQL, lakukan langkah-langkah berikut:

```bash
sudo apt update
sudo apt install mariadb-server mariadb-client -y
sudo service mariadb start
sudo mysql_secure_installation
mysql -u root -p
CREATE DATABASE mydatabase;
GRANT ALL PRIVILEGES ON mydatabase.* TO 'user'@'localhost' IDENTIFIED BY 'password';
FLUSH PRIVILEGES;
EXIT;
```
## 5. Konfigurasi Advance Jaringan
### 5.1 Multiple Ip Access
Tambahkan Routing
Tambahkan rute statis untuk jaringan eth1 agar bisa diakses dari luar. Misalnya, jika jaringan eth1 adalah 172.30.80.0/20:

```bash
ip route add 172.30.80.0/20 dev eth1
```
Jika perlu agar rute ini tetap ada setelah reboot, tambahkan perintah tersebut ke /etc/network/interfaces di bagian konfigurasi eth1:

```plaintext
iface eth1 inet static
        address 172.30.80.101/20
        up ip route add 172.30.80.0/20 dev eth1
```
## 6. Pengaturan dan Backup Data
Setelah semua konfigurasi selesai, Anda bisa mulai melakukan simulasi backup data. Pastikan data yang penting dicadangkan secara teratur untuk menjaga integritas sistem.

## 7. Pengaturan SSH dan Tunnel dengan Serveo
### 7.1 Install OpenSSH Server
Install OpenSSH untuk mengakses server melalui SSH:

```bash
sudo apt install openssh-server
```
Edit file konfigurasi SSH:
```bash
sudo nano /etc/ssh/sshd_config
```
Ubah pengaturan berikut:
```bash
PasswordAuthentication yes
PermitRootLogin yes
```
### 7.2 Setup Serveo Tunnel
Untuk membuat tunnel SSH yang aman dan dapat diakses secara publik menggunakan Serveo.net:
```bash
ssh -R 80:localhost:8080 serveo.net
```
Anda dapat mengganti port dan domain sesuai dengan kebutuhan.

## 8. Penutup
Dengan mengikuti langkah-langkah di atas, Anda akan dapat mengonfigurasi server pribadi menggunakan Proxmox dan ZeroTier, serta mengaksesnya dari jaringan lokal maupun publik secara aman. Pastikan untuk selalu menjaga konfigurasi jaringan dan backup data Anda.

Jika Anda memiliki pertanyaan atau masalah, silakan buka issue di halaman ini!

## 9. Setup Network
### 9.2 Setup VLAN
1. Install Virtual Switch
```bash
apt install openvswitch-switch
```
2.  On Network Create OVS Bridge
3.  Create OVS intPort, add name vlan100 and vlan tag repaeat bridge same OVS Bridge
4.  Done
### 9.2 Setup Netgate for Network Manager
1. Install Netgate by VM on Proxmox
2. Next2 and wait until reboot
3. Setup WAN LAN1 dan LAN2
4. Setup VLAN for LAN1
5. Setup DHCP server and NAT Hibrid also Firewall rule
6. Setup Netbird IP set as Gateway for access some IP in All Router

### 9.3 Setup SSH Server by Cloudflare
1. Setup Tunnel Zero Trust
2. Add Tunnel SSH
3. Go Access and Add Aplication
4. Choice Aplication Hosted
5. Enter Field some domain
6. Scroll Down until Identity providers Warp Authentication
License
7. Setting Policies
8. Choice Browser Rendering SSH
MIT License

```yaml

---

Ini adalah kode yang dapat digunakan sebagai **README.md** untuk proyek Anda di GitHub. Kode ini mengatur setiap langkah dengan jelas dan mudah dipahami. Anda hanya perlu menyesuaikan beberapa nilai placeholder (seperti `<version>`, `<NetworkID>`, dan `<zerotier-ip-of-router-node>`) dengan informasi yang sesuai.
```
