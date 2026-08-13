# 🔄 Routing EIGRP (Enhanced Interior Gateway Routing Protocol)

## 📖 Apa itu EIGRP?

**EIGRP (Enhanced Interior Gateway Routing Protocol)** adalah protokol routing distance-vector yang dikembangkan oleh Cisco sebagai pengembangan dari IGRP. EIGRP menggabungkan keunggulan distance-vector dan link-state routing protocol dengan menggunakan algoritma **DUAL (Diffusing Update Algorithm)** untuk memastikan jaringan bebas dari routing loop dan cepat dalam konvergensi.

### 🎯 Tujuan EIGRP:

1. **Konvergensi Cepat** - Menggunakan DUAL untuk konvergensi yang sangat cepat
2. **Efisiensi Bandwidth** - Hanya mengirim update saat terjadi perubahan (triggered update)
3. **Skalabilitas** - Mendukung VLSM dan dapat digunakan pada jaringan besar
4. **Load Balancing** - Mendukung equal-cost dan unequal-cost load balancing
5. **Keandalan** - Menggunakan RTP (Reliable Transport Protocol) untuk pengiriman update

### 🏷️ Karakteristik EIGRP:

| Karakteristik | Deskripsi |
|---------------|-----------|
| **Jenis Protokol** | Advanced Distance-Vector (Hybrid) |
| **Metrik** | Bandwidth, Delay, Load, Reliability, MTU |
| **Administrative Distance** | 90 (Internal), 170 (External) |
| **Update** | Triggered (tidak periodik) |
| **Algoritma** | DUAL (Diffusing Update Algorithm) |
| **VLSM** | Mendukung |
| **Authentication** | MD5 authentication |
| **Multicast Address** | 224.0.0.10 |
| **Auto-Summary** | Default: enabled (disarankan disable) |

### 📝 Komponen EIGRP:

| Komponen | Fungsi |
|----------|--------|
| **DUAL** | Algoritma untuk memastikan loop-free routing |
| **RTP** | Reliable Transport Protocol untuk komunikasi antar router |
| **Neighbor Table** | Menyimpan informasi tentang router tetangga |
| **Topology Table** | Menyimpan semua route yang diketahui |
| **Routing Table** | Menyimpan route terbaik |

---

## 🌐 Topologi

![Topologi Routing EIGRP](topologi.png)

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

### EIGRP Configuration

| Router | AS Number | Network | Interface | Keterangan |
|--------|-----------|---------|-----------|------------|
| **R1** | 100 | 10.10.10.0 | Gi0/0/0 | Jaringan antar router |
| **R1** | 100 | 192.168.1.0 | Gi0/0/1 | Jaringan lokal SW1 |
| **R2** | 100 | 10.10.10.0 | Gi0/0/0 | Jaringan antar router |
| **R2** | 100 | 192.168.2.0 | Gi0/0/1 | Jaringan lokal SW2 |

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

#### 1.3 Konfigurasi EIGRP di Router 1

```cisco
R1> enable
R1# configure terminal

! Aktifkan routing EIGRP dengan AS 100
R1(config)# router eigrp 100

! Nonaktifkan auto-summary (penting untuk VLSM)
R1(config-router)# no auto-summary

! Tambahkan network yang akan di-advertise
R1(config-router)# network 10.10.10.0 0.0.0.255
R1(config-router)# network 192.168.1.0 0.0.0.255

! Exit dari mode routing
R1(config-router)# exit

! Verifikasi konfigurasi
R1(config)# end
R1# copy running-config startup-config
```

**Penjelasan:**
- `router eigrp 100` : Mengaktifkan proses routing EIGRP dengan AS (Autonomous System) 100
- `no auto-summary` : Menonaktifkan auto-summary agar subnet mask tetap terjaga
- `network 10.10.10.0 0.0.0.255` : Mengadvertise network 10.10.10.0/24 (wildcard mask)
- `network 192.168.1.0 0.0.0.255` : Mengadvertise network 192.168.1.0/24 (wildcard mask)

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

#### 2.3 Konfigurasi EIGRP di Router 2

```cisco
R2> enable
R2# configure terminal

! Aktifkan routing EIGRP dengan AS 100
R2(config)# router eigrp 100

! Nonaktifkan auto-summary
R2(config-router)# no auto-summary

! Tambahkan network yang akan di-advertise
R2(config-router)# network 10.10.10.0 0.0.0.255
R2(config-router)# network 192.168.2.0 0.0.0.255

! Exit dari mode routing
R2(config-router)# exit

! Verifikasi konfigurasi
R2(config)# end
R2# copy running-config startup-config
```

**Penjelasan:**
- `router eigrp 100` : Mengaktifkan proses routing EIGRP dengan AS 100
- `no auto-summary` : Menonaktifkan auto-summary
- `network 10.10.10.0 0.0.0.255` : Mengadvertise network 10.10.10.0/24
- `network 192.168.2.0 0.0.0.255` : Mengadvertise network 192.168.2.0/24

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

### 3. Verifikasi Neighbor EIGRP

```cisco
R1# show ip eigrp neighbors
```

**Output yang diharapkan:**
```
EIGRP-IPv4 Neighbors for AS(100)
H   Address                 Interface       Hold Uptime   SRTT   RTO  Q  Seq
                                            (sec)         (ms)       Cnt Num
0   10.10.10.2              Gi0/0/0           12 00:00:40   10   200  0  5
```

**Penjelasan:**
- `H` = Neighbor ID
- `Address` = IP address neighbor
- `Interface` = Interface yang terhubung
- `Hold` = Hold time (detik)
- `Uptime` = Waktu neighbor telah aktif
- `SRTT` = Smooth Round Trip Time
- `Q Cnt` = Queue Count
- `Seq Num` = Sequence Number

### 4. Verifikasi Topology Table EIGRP

```cisco
R1# show ip eigrp topology
```

**Output yang diharapkan:**
```
EIGRP-IPv4 Topology Table for AS(100)/ID(10.10.10.1)

Codes: P - Passive, A - Active, U - Update, Q - Query, R - Reply,
       r - reply Status, s - sia Status

P 10.10.10.0/24, 1 successors, FD is 28160
        via Connected, GigabitEthernet0/0/0
P 192.168.1.0/24, 1 successors, FD is 28160
        via Connected, GigabitEthernet0/0/1
P 192.168.2.0/24, 1 successors, FD is 30720
        via 10.10.10.2 (30720/28160), GigabitEthernet0/0/0
```

**Penjelasan:**
- `P` = Passive (route stabil)
- `successors` = Jumlah route successor
- `FD` = Feasible Distance
- `via Connected` = Route terhubung langsung
- `via 10.10.10.2 (30720/28160)` = Route melalui neighbor, dengan metric 30720/28160

### 5. Verifikasi Tabel Routing Router 1

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
D    192.168.2.0/24 [90/30720] via 10.10.10.2, 00:01:02, GigabitEthernet0/0/0
```

**Penjelasan:**
- `D` = Route dari EIGRP
- `[90/30720]` = Administrative distance 90, metric 30720
- `via 10.10.10.2` = Next hop untuk mencapai network tujuan
- `00:01:02` = Waktu route ini ada di tabel routing

### 6. Verifikasi Tabel Routing Router 2

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
D    192.168.1.0/24 [90/30720] via 10.10.10.1, 00:01:02, GigabitEthernet0/0/0
C    192.168.2.0/24 is directly connected, GigabitEthernet0/0/1
L    192.168.2.1/32 is directly connected, GigabitEthernet0/0/1
```

### 7. Verifikasi Protocol EIGRP

```cisco
R1# show ip protocols
```

**Output yang diharapkan:**
```
Routing Protocol is "eigrp 100"
  Outgoing update filter list for all interfaces is not set
  Incoming update filter list for all interfaces is not set
  Default networks flagged in outgoing updates
  Default networks accepted from incoming updates
  EIGRP metric weight K1=1, K2=0, K3=1, K4=0, K5=0
  EIGRP maximum hopcount 100
  EIGRP maximum metric variance 1
  Redistributing: eigrp 100
  EIGRP NSF-aware route hold timer is 240s
  Automatic network summarization is not in effect
  Maximum path: 4
  Routing for Networks:
    10.10.10.0/24
    192.168.1.0/24
  Routing Information Sources:
    Gateway         Distance      Last Update
    10.10.10.2           90      00:00:14
  Distance: internal 90 external 170
```

### 8. Verifikasi Koneksi Antar PC

**Dari PC1 (192.168.1.10):**

```cmd
ping 192.168.1.1     # ke Gateway R1 - HARUS BERHASIL
ping 192.168.1.20    # ke PC2 - HARUS BERHASIL (sama network)
ping 10.10.10.2      # ke R2 - HARUS BERHASIL
ping 192.168.2.1     # ke Gateway R2 - HARUS BERHASIL
ping 192.168.2.10    # ke PC3 - HARUS BERHASIL (beda router via EIGRP)
ping 192.168.2.20    # ke PC4 - HARUS BERHASIL (beda router via EIGRP)
```

**Dari PC3 (192.168.2.10):**

```cmd
ping 192.168.2.1     # ke Gateway R2 - HARUS BERHASIL
ping 192.168.2.20    # ke PC4 - HARUS BERHASIL (sama network)
ping 10.10.10.1      # ke R1 - HARUS BERHASIL
ping 192.168.1.1     # ke Gateway R1 - HARUS BERHASIL
ping 192.168.1.10    # ke PC1 - HARUS BERHASIL (beda router via EIGRP)
ping 192.168.1.20    # ke PC2 - HARUS BERHASIL (beda router via EIGRP)
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

### 10. Debugging EIGRP (Opsional)

```cisco
! Untuk melihat update EIGRP secara real-time
R1# debug ip eigrp

! Matikan debugging
R1# undebug all
```

---

## 📊 Ringkasan Konfigurasi

### Router 1

| Parameter | Nilai |
|-----------|-------|
| **Hostname** | R1 |
| **Gi0/0/0 IP** | 10.10.10.1/24 |
| **Gi0/0/1 IP** | 192.168.1.1/24 |
| **Routing Protocol** | EIGRP AS 100 |
| **Network Advertised** | 10.10.10.0/24, 192.168.1.0/24 |
| **Auto-Summary** | Disabled |
| **Administrative Distance** | 90 (Internal) |

### Router 2

| Parameter | Nilai |
|-----------|-------|
| **Hostname** | R2 |
| **Gi0/0/0 IP** | 10.10.10.2/24 |
| **Gi0/0/1 IP** | 192.168.2.1/24 |
| **Routing Protocol** | EIGRP AS 100 |
| **Network Advertised** | 10.10.10.0/24, 192.168.2.0/24 |
| **Auto-Summary** | Disabled |
| **Administrative Distance** | 90 (Internal) |

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
| PC1 tidak bisa ping ke PC3 | EIGRP tidak aktif | Cek `show ip protocols` |
| Route tidak muncul di tabel routing | Network tidak di-advertise | Cek `router eigrp 100` dan `network` statement |
| Router tidak membentuk neighbor | AS number berbeda | Pastikan AS number sama di semua router |
| Route tidak muncul di tabel routing | Auto-summary aktif | Gunakan `no auto-summary` |
| Koneksi antar router gagal | IP address tidak segaris | Cek IP address di kedua ujung link |
| EIGRP neighbor flapping | Koneksi tidak stabil | Periksa kabel dan interface |
| Route tidak di-advertise | Wildcard mask salah | Gunakan wildcard mask yang tepat |
| Unequal cost load balancing tidak aktif | Variance tidak di-set | Konfigurasi `variance` untuk unequal cost |

---

## 📚 Referensi

- [Cisco EIGRP Documentation](https://www.cisco.com/c/en/us/support/docs/ip/enhanced-interior-gateway-routing-protocol-eigrp/16406-eigrp-toc.html)
- [Cisco EIGRP Configuration Guide](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/iproute_eigrp/configuration/15-s/ire-15-s-book/ire-eigrp-overview.html)
- [EIGRP Neighbor and Topology Table](https://www.cisco.com/c/en/us/support/docs/ip/enhanced-interior-gateway-routing-protocol-eigrp/13621-13.html)

---