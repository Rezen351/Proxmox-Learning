# 🚀 Proxmox Learning Journey for Gen Z

Welcome, Alif! Ini adalah panduan **Proxmox** dari basic sampai advanced! Cocok banget buat yang pengen belajar virtualisasi tanpa ribet. Ayo mulai dari basic, terus kita bakal explore fitur-fitur keren lainnya!

---

## 📚 Daftar Isi
1. [Apa Itu Proxmox?](#apa-itu-proxmox)
2. [Terminologi Dasar](#terminologi-dasar)
3. [Langkah Awal Setup Proxmox](#langkah-awal-setup-proxmox)
4. [Membuat VM Pertama Kamu](#membuat-vm-pertama-kamu)
5. [Containers 101](#containers-101)
6. [Jaringan Dasar di Proxmox](#jaringan-dasar-di-proxmox)
7. [Backup & Restore](#backup--restore)
8. [Tips & Tricks](#tips--tricks)
9. [Next Level - Advanced!](#next-level---advanced)

---

### Apa Itu Proxmox?

**Proxmox VE (Virtual Environment)** itu platform open-source buat virtualisasi. Di sini kamu bisa bikin "mesin virtual" atau "container" dalam satu hardware aja. Jadi, semua bisa dikelola dari satu tempat aja, praktis banget kan?

### Terminologi Dasar

- **VM (Virtual Machine):** Mesin virtual lengkap dengan OS.
- **Container (LXC):** Environment yang lebih ringan dan isolated, pas buat aplikasi kecil.
- **Node:** Mesin fisik tempat install Proxmox.
- **Cluster:** Gabungan beberapa node biar makin powerful.
- **Storage:** Tempat simpen data VM/container, bisa lokal atau network.

### Langkah Awal Setup Proxmox

1. **Download ISO** di [proxmox.com](https://www.proxmox.com/) dan install di mesin kamu.
2. **Booting:** Masuk ke Web UI Proxmox via `https://[IP_address]:8006` dan login sebagai root.
3. **License Warning:** Abaikan aja kalau dapet trial license warning.

### Membuat VM Pertama Kamu

1. Klik **Create VM** di interface.
2. Pilih OS (Ubuntu, Windows, dll) dan setting resource (CPU, RAM, dll).
3. Start VM dan ikuti proses instalasi OS seperti biasa.

> **Note:** Jangan kasih semua resource di satu VM, nanti yang lain bisa lemot!

### Containers 101

Container lebih ringan daripada VM. Ini cocok buat aplikasi kecil atau yang nggak butuh resource banyak. Pilihan ideal buat yang mau hemat resource dan cepat.

### Jaringan Dasar di Proxmox

- **Bridged Network:** Buat VM/container punya IP sendiri di jaringan lokal.
- **NAT Network:** Semua IP virtual, cocok buat testing.

### Backup & Restore

- **Snapshot:** Backup cepat sebelum ada perubahan besar.
- **Full Backup:** Backup lengkap buat recovery kalau ada masalah.
- **Restore:** Balikin VM/container dari backup kapanpun butuh.

### Tips & Tricks

- **Update Rutin**: Biar aman, rajin cek update Proxmox.
- **Monitoring**: Cek dashboard Proxmox buat resource usage.
- **CLI Commands**: Pakai command line `qm` buat manage VM dengan cepat.

---

### Next Level - Advanced!

Kalau udah paham dasar, bisa explore bikin cluster, setup HA (High Availability), bikin jaringan yang lebih kompleks, atau otomatisasi dengan script!

--- 

**Happy learning Proxmox!** Jangan lupa update progres belajarmu di sini ya!
