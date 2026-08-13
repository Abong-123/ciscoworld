# 🔄 Routing RIP (Routing Information Protocol) v2

## 📖 Apa itu Routing RIP?

**RIP (Routing Information Protocol)** adalah salah satu protokol routing distance-vector yang paling sederhana dan tertua. RIP menggunakan hop count sebagai metrik untuk menentukan jalur terbaik menuju jaringan tujuan. RIP v2 merupakan pengembangan dari RIP v1 dengan menambahkan dukungan untuk VLSM (Variable Length Subnet Mask), authentication, dan multicast.

### 🎯 Tujuan RIP:

1. **Routing Dinamis** - Router saling bertukar informasi routing secara otomatis
2. **Adaptif** - Dapat menyesuaikan diri dengan perubahan topologi jaringan
3. **Sederhana** - Mudah dikonfigurasi dan dipahami
4. **Jaringan Kecil** - Cocok untuk jaringan dengan skala kecil hingga menengah

### 🏷️ Karakteristik RIP:

| Karakteristik | Deskripsi |
|---------------|-----------|
| **Jenis Protokol** | Distance-vector |
| **Metrik** | Hop Count (maksimal 15 hop) |
| **Administrative Distance** | 120 |
| **Update Interval** | 30 detik |
| **Versi** | RIP v1 (classful) dan RIP v2 (classless) |
| **Port** | UDP 520 |
| **Broadcast/Multicast** | RIP v1: Broadcast (255.255.255.255), RIP v2: Multicast (224.0.0.9) |
| **Authentication** | RIP v2 mendukung MD5 authentication |

### 📝 Perbedaan RIP v1 dan RIP v2:

| Fitur | RIP v1 | RIP v2 |
|-------|--------|--------|
| **VLSM** | Tidak mendukung | Mendukung |
| **Classless** | Tidak (classful) | Ya (classless) |
| **Authentication** | Tidak ada | MD5 authentication |
| **Update** | Broadcast | Multicast (224.0.0.9) |
| **Subnet Mask** | Tidak dikirim | Dikirim dalam update |

---

## 🌐 Topologi

![Topologi Routing RIP v2](topologi.png)

---

## 📊 Tabel Perangkat dan IP Address

### Router 1

| Perangkat | Antarmuka | IP Address | Netmask | Keterangan |
|-----------|-----------|------------|---------|------------|
| **Router 1** | Gi0/0/0 | 10.10.10.1 | 255.255.255.0 | Ke Router 2 |
| **Router 1** | Gi0/0/1 | 192.168.1.1 | 255.255.255.0 | Ke Switch 1 |

### Router 2

| Perangkat | Antarmuka | IP Address | Netmask | Keterangan |
|-----------|-----------|------------|---------|------------|
| **Router 2** | Gi0/0/0 | 10.10.10.2 | 255.255.255.0 | Ke Router 1 |
| **Router 2** | Gi0/0/1 | 192.168.2.1 | 255.255.255.0 | Ke Switch 2 |

### Switch 1

| Perangkat | Antarmuka | VLAN | Status | Keterangan |
|-----------|-----------|------|--------|------------|
| **Switch 1** | Fa0/1 | VLAN 1 (Native) | Up | Ke Router 1 Gi0/0/1 |
| **Switch 1** | Fa0/2-24 | VLAN 1 (Native) | Up | Ke End-Devices |

### Switch 2

| Perangkat | Antarmuka | VLAN | Status | Keterangan |
|-----------|-----------|------|--------|------------|
| **Switch 2** | Fa0/1 | VLAN 1 (Native) | Up | Ke Router 2 Gi0/0/1 |
| **Switch 2** | Fa0/2-24 | VLAN 1 (Native) | Up | Ke End-Devices |

### Perangkat End-User

| Perangkat | Network | IP Address | Netmask | Gateway | Keterangan |
|-----------|---------|------------|---------|---------|------------|
| **PC1** | 192.168.1.0/24 | 192.168.1.10 | 255.255.255.0 | 192.168.1.1 | Terhubung ke SW1 |
| **PC2** | 192.168.1.0/24 | 192.168.1.20 | 255.255.255.0 | 192.168.1.1 | Terhubung ke SW1 |
| **PC3** | 192.168.2.0/24 | 192.168.2.10 | 255.255.255.0 | 192.168.2.1 | Terhubung ke SW2 |
| **PC4** | 192.168.2.0/24 | 192.168.2.20 | 255.255.255.0 | 192.168.2.1 | Terhubung ke SW2 |

---

## 📋 Detail Konfigurasi

### Router 1

| Antarmuka | IP Address | Subnet Mask | Status | Keterangan |
|-----------|------------|-------------|--------|------------|
| Gi0/0/0 | 10.10.10.1 | 255.255.255.0 | Up | Link ke Router 2 |
| Gi0/0/1 | 192.168.1.1 | 255.255.255.0 | Up | Link ke Switch 1 |

### Router 2

| Antarmuka | IP Address | Subnet Mask | Status | Keterangan |
|-----------|------------|-------------|--------|------------|
| Gi0/0/0 | 10.10.10.2 | 255.255.255.0 | Up | Link ke Router 1 |
| Gi0/0/1 | 192.168.2.1 | 255.255.255.0 | Up | Link ke Switch 2 |

### RIP v2 Configuration

| Router | Network | Interface | Keterangan |
|--------|---------|-----------|------------|
| **R1** | 10.10.10.0 | Gi0/0/0 | Jaringan antar router |
| **R1** | 192.168.1.0 | Gi0/0/1 | Jaringan lokal SW1 |
| **R2** | 10.10.10.0 | Gi0/0/0 | Jaringan antar router |
| **R2** | 192.168.2.0 | Gi0/0/1 | Jaringan lokal SW2 |

---

## ⚙️ Langkah-Langkah Konfigurasi

### 1. Konfigurasi Router 1

#### 1.1 Konfigurasi Dasar Router 1

```cisco
Router> enable
Router# configure terminal
Router(config)# hostname R1

! Nonaktifkan DNS lookup
R1(config)# no ip domain-lookup

! Enkripsi password
R1(config)# service password-encryption

! Set password untuk akses console dan VTY
R1(config)# line console 0
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# exit

R1(config)# line vty 0 4
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# exit

! Set enable password
R1(config)# enable secret cisco123

R1(config)# end
R1# copy running-config startup-config
```

#### 1.2 Konfigurasi IP Address Router 1

```cisco
R1> enable
R1# configure terminal

! Konfigurasi interface ke Router 2 (Gi0/0/0)
R1(config)# interface gigabitEthernet 0/0/0
R1(config-if)# description === LINK TO ROUTER 2 (10.10.10.0/24) ===
R1(config-if)# ip address 10.10.10.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit

! Konfigurasi interface ke Switch 1 (Gi0/0/1)
R1(config)# interface gigabitEthernet 0/0/1
R1(config-if)# description === LINK TO SWITCH 1 (192.168.1.0/24) ===
R1(config-if)# ip address 192.168.1.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# end
R1# copy running-config startup-config
```

#### 1.3 Konfigurasi RIP v2 di Router 1

```cisco
R1> enable
R1# configure terminal

! Aktifkan routing RIP
R1(config)# router rip

! Gunakan versi 2
R1(config-router)# version 2

! Nonaktifkan auto-summary (penting untuk VLSM)
R1(config-router)# no auto-summary

! Tambahkan network yang akan di-advertise
R1(config-router)# network 10.0.0.0
R1(config-router)# network 192.168.1.0

! Exit dari mode routing
R1(config-router)# exit

! Verifikasi konfigurasi
R1(config)# end
R1# copy running-config startup-config
```

**Penjelasan:**
- `router rip` : Mengaktifkan proses routing RIP
- `version 2` : Menggunakan RIP versi 2 (mendukung VLSM dan authentication)
- `no auto-summary` : Menonaktifkan auto-summary agar subnet mask tetap terjaga
- `network 10.0.0.0` : Mengadvertise network 10.10.10.0/24
- `network 192.168.1.0` : Mengadvertise network 192.168.1.0/24

---

### 2. Konfigurasi Router 2

#### 2.1 Konfigurasi Dasar Router 2

```cisco
Router> enable
Router# configure terminal
Router(config)# hostname R2

! Nonaktifkan DNS lookup
R2(config)# no ip domain-lookup

! Enkripsi password
R2(config)# service password-encryption

! Set password untuk akses console dan VTY
R2(config)# line console 0
R2(config-line)# password cisco
R2(config-line)# login
R2(config-line)# exit

R2(config)# line vty 0 4
R2(config-line)# password cisco
R2(config-line)# login
R2(config-line)# exit

! Set enable password
R2(config)# enable secret cisco123

R2(config)# end
R2# copy running-config startup-config
```

#### 2.2 Konfigurasi IP Address Router 2

```cisco
R2> enable
R2# configure terminal

! Konfigurasi interface ke Router 1 (Gi0/0/0)
R2(config)# interface gigabitEthernet 0/0/0
R2(config-if)# description === LINK TO ROUTER 1 (10.10.10.0/24) ===
R2(config-if)# ip address 10.10.10.2 255.255.255.0
R2(config-if)# no shutdown
R2(config-if)# exit

! Konfigurasi interface ke Switch 2 (Gi0/0/1)
R2(config)# interface gigabitEthernet 0/0/1
R2(config-if)# description === LINK TO SWITCH 2 (192.168.2.0/24) ===
R2(config-if)# ip address 192.168.2.1 255.255.255.0
R2(config-if)# no shutdown
R2(config-if)# exit

R2(config)# end
R2# copy running-config startup-config
```

#### 2.3 Konfigurasi RIP v2 di Router 2

```cisco
R2> enable
R2# configure terminal

! Aktifkan routing RIP
R2(config)# router rip

! Gunakan versi 2
R2(config-router)# version 2

! Nonaktifkan auto-summary
R2(config-router)# no auto-summary

! Tambahkan network yang akan di-advertise
R2(config-router)# network 10.0.0.0
R2(config-router)# network 192.168.2.0

! Exit dari mode routing
R2(config-router)# exit

! Verifikasi konfigurasi
R2(config)# end
R2# copy running-config startup-config
```

**Penjelasan:**
- `router rip` : Mengaktifkan proses routing RIP
- `version 2` : Menggunakan RIP versi 2
- `no auto-summary` : Menonaktifkan auto-summary
- `network 10.0.0.0` : Mengadvertise network 10.10.10.0/24
- `network 192.168.2.0` : Mengadvertise network 192.168.2.0/24

---

### 3. Konfigurasi Switch 1

#### 3.1 Konfigurasi Dasar Switch 1

```cisco
Switch> enable
Switch# configure terminal
Switch(config)# hostname SW1

! Nonaktifkan DNS lookup
SW1(config)# no ip domain-lookup

! Enkripsi password
SW1(config)# service password-encryption

! Set password untuk akses console dan VTY
SW1(config)# line console 0
SW1(config-line)# password cisco
SW1(config-line)# login
SW1(config-line)# exit

SW1(config)# line vty 0 15
SW1(config-line)# password cisco
SW1(config-line)# login
SW1(config-line)# exit

! Set enable password
SW1(config)# enable secret cisco123

SW1(config)# end
SW1# copy running-config startup-config
```

#### 3.2 Konfigurasi Port Switch 1

```cisco
SW1> enable
SW1# configure terminal

! Konfigurasi port Fa0/1 ke Router 1
SW1(config)# interface fastEthernet 0/1
SW1(config-if)# description === LINK TO ROUTER 1 ===
SW1(config-if)# switchport mode access
SW1(config-if)# switchport access vlan 1
SW1(config-if)# no shutdown
SW1(config-if)# exit

! Konfigurasi port Fa0/2-24 untuk end-devices
SW1(config)# interface range fastEthernet 0/2 - 24
SW1(config-if-range)# description === END-DEVICES VLAN 1 ===
SW1(config-if-range)# switchport mode access
SW1(config-if-range)# switchport access vlan 1
SW1(config-if-range)# no shutdown
SW1(config-if-range)# exit

SW1(config)# end
SW1# copy running-config startup-config
```

---

### 4. Konfigurasi Switch 2

#### 4.1 Konfigurasi Dasar Switch 2

```cisco
Switch> enable
Switch# configure terminal
Switch(config)# hostname SW2

! Nonaktifkan DNS lookup
SW2(config)# no ip domain-lookup

! Enkripsi password
SW2(config)# service password-encryption

! Set password untuk akses console dan VTY
SW2(config)# line console 0
SW2(config-line)# password cisco
SW2(config-line)# login
SW2(config-line)# exit

SW2(config)# line vty 0 15
SW2(config-line)# password cisco
SW2(config-line)# login
SW2(config-line)# exit

! Set enable password
SW2(config)# enable secret cisco123

SW2(config)# end
SW2# copy running-config startup-config
```

#### 4.2 Konfigurasi Port Switch 2

```cisco
SW2> enable
SW2# configure terminal

! Konfigurasi port Fa0/1 ke Router 2
SW2(config)# interface fastEthernet 0/1
SW2(config-if)# description === LINK TO ROUTER 2 ===
SW2(config-if)# switchport mode access
SW2(config-if)# switchport access vlan 1
SW2(config-if)# no shutdown
SW2(config-if)# exit

! Konfigurasi port Fa0/2-24 untuk end-devices
SW2(config)# interface range fastEthernet 0/2 - 24
SW2(config-if-range)# description === END-DEVICES VLAN 1 ===
SW2(config-if-range)# switchport mode access
SW2(config-if-range)# switchport access vlan 1
SW2(config-if-range)# no shutdown
SW2(config-if-range)# exit

SW2(config)# end
SW2# copy running-config startup-config
```

---

### 5. Konfigurasi Perangkat End-User

#### 5.1 PC1 (Terhubung ke SW1)

| Parameter | Nilai |
|-----------|-------|
| IP Address | 192.168.1.10 |
| Netmask | 255.255.255.0 |
| Gateway | 192.168.1.1 |
| DNS | 8.8.8.8 |

#### 5.2 PC2 (Terhubung ke SW1)

| Parameter | Nilai |
|-----------|-------|
| IP Address | 192.168.1.20 |
| Netmask | 255.255.255.0 |
| Gateway | 192.168.1.1 |
| DNS | 8.8.8.8 |

#### 5.3 PC3 (Terhubung ke SW2)

| Parameter | Nilai |
|-----------|-------|
| IP Address | 192.168.2.10 |
| Netmask | 255.255.255.0 |
| Gateway | 192.168.2.1 |
| DNS | 8.8.8.8 |

#### 5.4 PC4 (Terhubung ke SW2)

| Parameter | Nilai |
|-----------|-------|
| IP Address | 192.168.2.20 |
| Netmask | 255.255.255.0 |
| Gateway | 192.168.2.1 |
| DNS | 8.8.8.8 |

---

## ✅ Verifikasi Konfigurasi

### 1. Verifikasi IP Address Router 1

```cisco
R1# show ip interface brief
```

**Output yang diharapkan:**
```
Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0/0   10.10.10.1      YES manual up                    up
GigabitEthernet0/0/1   192.168.1.1     YES manual up                    up
```

### 2. Verifikasi IP Address Router 2

```cisco
R2# show ip interface brief
```

**Output yang diharapkan:**
```
Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0/0   10.10.10.2      YES manual up                    up
GigabitEthernet0/0/1   192.168.2.1     YES manual up                    up
```

### 3. Verifikasi Konfigurasi RIP

```cisco
R1# show ip protocols
```

**Output yang diharapkan:**
```
Routing Protocol is "rip"
  Sending updates every 30 seconds, next due in 0 seconds
  Invalid after 180 seconds, hold down 180, flushed after 240
  Outgoing update filter list for all interfaces is not set
  Incoming update filter list for all interfaces is not set
  Redistributing: rip
  Default version control: send version 2, receive version 2
    Interface        Send  Recv  Triggered RIP  Key-chain
    GigabitEthernet0/0/0  2     2
    GigabitEthernet0/0/1  2     2
  Automatic network summarization is not in effect
  Maximum path: 4
  Routing for Networks:
    10.0.0.0
    192.168.1.0
  Routing Information Sources:
    Gateway         Distance      Last Update
    10.10.10.2           120      00:00:07
  Distance: (default is 120)
```

### 4. Verifikasi Tabel Routing Router 1

```cisco
R1# show ip route
```

**Output yang diharapkan:**
```
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default

Gateway of last resort is not set

C    10.10.10.0/24 is directly connected, GigabitEthernet0/0/0
L    10.10.10.1/32 is directly connected, GigabitEthernet0/0/0
C    192.168.1.0/24 is directly connected, GigabitEthernet0/0/1
L    192.168.1.1/32 is directly connected, GigabitEthernet0/0/1
R    192.168.2.0/24 [120/1] via 10.10.10.2, 00:00:02, GigabitEthernet0/0/0
```

**Penjelasan:**
- `R` = Route yang didapat dari RIP
- `[120/1]` = Administrative distance 120, metric/hop count 1
- `via 10.10.10.2` = Next hop untuk mencapai network tujuan
- `00:00:02` = Waktu terakhir update diterima

### 5. Verifikasi Tabel Routing Router 2

```cisco
R2# show ip route
```

**Output yang diharapkan:**
```
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default

Gateway of last resort is not set

C    10.10.10.0/24 is directly connected, GigabitEthernet0/0/0
L    10.10.10.2/32 is directly connected, GigabitEthernet0/0/0
R    192.168.1.0/24 [120/1] via 10.10.10.1, 00:00:02, GigabitEthernet0/0/0
C    192.168.2.0/24 is directly connected, GigabitEthernet0/0/1
L    192.168.2.1/32 is directly connected, GigabitEthernet0/0/1
```

### 6. Verifikasi Database RIP

```cisco
R1# show ip rip database
```

**Output yang diharapkan:**
```
10.0.0.0/8    auto-summary
10.10.10.0/24    directly connected, GigabitEthernet0/0/0
192.168.1.0/24    directly connected, GigabitEthernet0/0/1
192.168.2.0/24
    [1] via 10.10.10.2, 00:00:05, GigabitEthernet0/0/0
```

### 7. Verifikasi Neighbor RIP

```cisco
R1# show ip rip neighbors
```

**Output yang diharapkan:**
```
Neighbor          Last Update
10.10.10.2        00:00:09
```

### 8. Verifikasi Koneksi Antar PC

**Dari PC1 (192.168.1.10):**

```cmd
ping 192.168.1.1     # ke Gateway R1 - HARUS BERHASIL
ping 192.168.1.20    # ke PC2 - HARUS BERHASIL (sama network)
ping 10.10.10.2      # ke R2 - HARUS BERHASIL
ping 192.168.2.1     # ke Gateway R2 - HARUS BERHASIL
ping 192.168.2.10    # ke PC3 - HARUS BERHASIL (beda router via RIP)
ping 192.168.2.20    # ke PC4 - HARUS BERHASIL (beda router via RIP)
```

**Dari PC3 (192.168.2.10):**

```cmd
ping 192.168.2.1     # ke Gateway R2 - HARUS BERHASIL
ping 192.168.2.20    # ke PC4 - HARUS BERHASIL (sama network)
ping 10.10.10.1      # ke R1 - HARUS BERHASIL
ping 192.168.1.1     # ke Gateway R1 - HARUS BERHASIL
ping 192.168.1.10    # ke PC1 - HARUS BERHASIL (beda router via RIP)
ping 192.168.1.20    # ke PC2 - HARUS BERHASIL (beda router via RIP)
```

### 9. Verifikasi Traceroute

**Dari PC1 ke PC3:**

```cmd
tracert 192.168.2.10
```

**Output yang diharapkan:**
```
Tracing route to 192.168.2.10 over a maximum of 30 hops:

  1    <1 ms    <1 ms    <1 ms  192.168.1.1
  2    <1 ms    <1 ms    <1 ms  10.10.10.2
  3    <1 ms    <1 ms    <1 ms  192.168.2.10

Trace complete.
```

### 10. Debugging RIP (Opsional)

```cisco
! Untuk melihat update RIP secara real-time
R1# debug ip rip

! Matikan debugging
R1# undebug all
```

**Output yang diharapkan:**
```
RIP: sending v2 update to 224.0.0.9 via GigabitEthernet0/0/0 (10.10.10.1)
RIP: build update entries
    192.168.1.0/24 via 0.0.0.0, metric 1, tag 0
RIP: received v2 update from 10.10.10.2 on GigabitEthernet0/0/0
    192.168.2.0/24 via 0.0.0.0 in 1 hops
```

---

## 📊 Ringkasan Konfigurasi

### Router 1

| Parameter | Nilai |
|-----------|-------|
| **Hostname** | R1 |
| **Gi0/0/0 IP** | 10.10.10.1/24 |
| **Gi0/0/1 IP** | 192.168.1.1/24 |
| **Routing Protocol** | RIP v2 |
| **Network Advertised** | 10.0.0.0, 192.168.1.0 |
| **Auto-Summary** | Disabled |
| **Administrative Distance** | 120 |

### Router 2

| Parameter | Nilai |
|-----------|-------|
| **Hostname** | R2 |
| **Gi0/0/0 IP** | 10.10.10.2/24 |
| **Gi0/0/1 IP** | 192.168.2.1/24 |
| **Routing Protocol** | RIP v2 |
| **Network Advertised** | 10.0.0.0, 192.168.2.0 |
| **Auto-Summary** | Disabled |
| **Administrative Distance** | 120 |

### Switch 1

| Parameter | Nilai |
|-----------|-------|
| **Hostname** | SW1 |
| **VLAN** | 1 (Native) |
| **Port ke R1** | Fa0/1 (Access VLAN 1) |
| **Port ke PC** | Fa0/2-24 (Access VLAN 1) |

### Switch 2

| Parameter | Nilai |
|-----------|-------|
| **Hostname** | SW2 |
| **VLAN** | 1 (Native) |
| **Port ke R2** | Fa0/1 (Access VLAN 1) |
| **Port ke PC** | Fa0/2-24 (Access VLAN 1) |

### PC

| Perangkat | Network | IP Address | Gateway |
|-----------|---------|------------|---------|
| **PC1** | 192.168.1.0/24 | 192.168.1.10 | 192.168.1.1 |
| **PC2** | 192.168.1.0/24 | 192.168.1.20 | 192.168.1.1 |
| **PC3** | 192.168.2.0/24 | 192.168.2.10 | 192.168.2.1 |
| **PC4** | 192.168.2.0/24 | 192.168.2.20 | 192.168.2.1 |

---

## 🔧 Troubleshooting

| Masalah | Kemungkinan Penyebab | Solusi |
|---------|---------------------|--------|
| PC tidak bisa ping ke gateway | IP address salah atau interface down | Cek `show ip interface brief` |
| PC1 tidak bisa ping ke PC3 | RIP tidak aktif | Cek `show ip protocols` |
| Route tidak muncul di tabel routing | Network tidak di-advertise | Cek `router rip` dan `network` statement |
| Route tidak muncul di tabel routing | Auto-summary aktif | Gunakan `no auto-summary` |
| RIP tidak mengirim update | Version mismatch | Pastikan semua router menggunakan version 2 |
| Routing loop | Topologi tidak stabil | Periksa konfigurasi dan tambahkan route untuk stabilisasi |
| Koneksi antar router gagal | IP address tidak segaris | Cek IP address di kedua ujung link |
| Timer tidak sinkron | Timer berbeda | Cek `show ip protocols` dan timer RIP |
| Split horizon tidak aktif | Konfigurasi default | Split horizon aktif secara default, tidak perlu dikonfigurasi |

---

## 📚 Referensi

- [Cisco RIP Configuration Guide](https://www.cisco.com/c/en/us/support/docs/ip/routing-information-protocol-rip/4688-18.html)
- [Cisco RIP Version 2 Documentation](https://www.cisco.com/c/en/us/support/docs/ip/routing-information-protocol-rip/13722-30.html)
- [Cisco IOS IP Routing RIP Configuration](https://www.cisco.com/c/en/us/td/docs/ios/iproute_rip/configuration/guide/irr-book.html)

---