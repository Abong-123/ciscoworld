# 🔄 Routing OSPF (Open Shortest Path First)

## 📖 Apa itu OSPF?

**OSPF (Open Shortest Path First)** adalah protokol routing link-state yang dikembangkan oleh IETF (Internet Engineering Task Force) sebagai standar terbuka untuk routing di jaringan IP. OSPF menggunakan algoritma **SPF (Shortest Path First)** atau yang dikenal dengan algoritma **Dijkstra** untuk menghitung jalur terpendek menuju setiap jaringan tujuan.

### 🎯 Tujuan OSPF:

1. **Konvergensi Cepat** - Menggunakan algoritma SPF untuk perhitungan jalur tercepat
2. **Skalabilitas** - Mendukung hierarki dengan area untuk jaringan besar
3. **Tidak Ada Hop Count Limit** - Tidak terbatas pada hop count seperti RIP
4. **Load Balancing** - Mendukung equal-cost load balancing
5. **VLSM** - Mendukung Variable Length Subnet Mask
6. **Authentication** - Mendukung authentication untuk keamanan

### 🏷️ Karakteristik OSPF:

| Karakteristik | Deskripsi |
|---------------|-----------|
| **Jenis Protokol** | Link-state |
| **Metrik** | Cost (Bandwidth-based) |
| **Administrative Distance** | 110 |
| **Update** | Triggered (saat terjadi perubahan) |
| **Algoritma** | SPF (Dijkstra) |
| **VLSM** | Mendukung |
| **Authentication** | Plain-text, MD5, SHA |
| **Multicast Address** | 224.0.0.5 (AllSPFRouters), 224.0.0.6 (AllDRouters) |
| **Area** | Mendukung area untuk skalabilitas |

### 📝 Komponen OSPF:

| Komponen | Fungsi |
|----------|--------|
| **LSA (Link State Advertisement)** | Mengumumkan informasi routing |
| **LSDB (Link State Database)** | Database yang berisi semua LSA |
| **SPF Algorithm** | Menghitung jalur terpendek |
| **Area** | Pengelompokan router untuk skalabilitas |
| **DR (Designated Router)** | Router yang bertanggung jawab di multi-access network |
| **BDR (Backup Designated Router)** | Backup untuk DR |
| **Hello Protocol** | Untuk menemukan dan memelihara neighbor |

### 📊 OSPF Area Types:

| Area Type | Deskripsi |
|-----------|-----------|
| **Backbone Area (Area 0)** | Area utama yang menghubungkan semua area |
| **Standard Area** | Area normal yang menerima semua LSA |
| **Stub Area** | Tidak menerima LSA dari luar AS |
| **Totally Stubby Area** | Tidak menerima LSA dari luar area |
| **Not-So-Stubby Area (NSSA)** | Stub area yang mengizinkan external routes |

---

## 🌐 Topologi

![Topologi Routing OSPF](topologi.png)

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

### OSPF Configuration

| Router | Process ID | Area | Network | Interface | Keterangan |
|--------|-----------|------|---------|-----------|------------|
| **R1** | 1 | 0 | 10.10.10.0 | Gi0/0/0 | Jaringan antar router |
| **R1** | 1 | 0 | 192.168.1.0 | Gi0/0/1 | Jaringan lokal SW1 |
| **R2** | 1 | 0 | 10.10.10.0 | Gi0/0/0 | Jaringan antar router |
| **R2** | 1 | 0 | 192.168.2.0 | Gi0/0/1 | Jaringan lokal SW2 |

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

#### 1.3 Konfigurasi OSPF di Router 1

```cisco
R1> enable
R1# configure terminal

! Aktifkan routing OSPF dengan process ID 1
R1(config)# router ospf 1

! Tambahkan network yang akan di-advertise
R1(config-router)# network 10.10.10.0 0.0.0.255 area 0
R1(config-router)# network 192.168.1.0 0.0.0.255 area 0

! Exit dari mode routing
R1(config-router)# exit

! Verifikasi konfigurasi
R1(config)# end
R1# copy running-config startup-config
```

**Penjelasan:**
- `router ospf 1` : Mengaktifkan proses routing OSPF dengan Process ID 1
- `network 10.10.10.0 0.0.0.255 area 0` : Mengadvertise network 10.10.10.0/24 ke area 0
- `network 192.168.1.0 0.0.0.255 area 0` : Mengadvertise network 192.168.1.0/24 ke area 0
- `area 0` : Menentukan area backbone (semua area harus terhubung ke area 0)

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

#### 2.3 Konfigurasi OSPF di Router 2

```cisco
R2> enable
R2# configure terminal

! Aktifkan routing OSPF dengan process ID 1
R2(config)# router ospf 1

! Tambahkan network yang akan di-advertise
R2(config-router)# network 10.10.10.0 0.0.0.255 area 0
R2(config-router)# network 192.168.2.0 0.0.0.255 area 0

! Exit dari mode routing
R2(config-router)# exit

! Verifikasi konfigurasi
R2(config)# end
R2# copy running-config startup-config
```

**Penjelasan:**
- `router ospf 1` : Mengaktifkan proses routing OSPF dengan Process ID 1
- `network 10.10.10.0 0.0.0.255 area 0` : Mengadvertise network 10.10.10.0/24 ke area 0
- `network 192.168.2.0 0.0.0.255 area 0` : Mengadvertise network 192.168.2.0/24 ke area 0

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

### 3. Verifikasi Neighbor OSPF

```cisco
R1# show ip ospf neighbor
```

**Output yang diharapkan:**
```
Neighbor ID     Pri   State           Dead Time   Address         Interface
10.10.10.2        1   FULL/DR         00:00:33    10.10.10.2      GigabitEthernet0/0/0
```

**Penjelasan:**
- `Neighbor ID` = Router ID dari neighbor
- `Pri` = Priority (default 1)
- `State` = FULL/DR, FULL/BDR, FULL/DROTHER, 2-WAY
- `Dead Time` = Waktu sebelum neighbor dianggap mati
- `Address` = IP address neighbor
- `Interface` = Interface yang terhubung

### 4. Verifikasi Database OSPF

```cisco
R1# show ip ospf database
```

**Output yang diharapkan:**
```
            OSPF Router with ID (10.10.10.1) (Process ID 1)

                Router Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum Link count
10.10.10.1      10.10.10.1      21          0x80000004 0x00D2F0 2
10.10.10.2      10.10.10.2      18          0x80000004 0x00D3E7 2

                Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
10.10.10.2      10.10.10.2      18          0x80000001 0x00A1B7
```

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
O    192.168.2.0/24 [110/2] via 10.10.10.2, 00:00:04, GigabitEthernet0/0/0
```

**Penjelasan:**
- `O` = Route dari OSPF
- `[110/2]` = Administrative distance 110, metric/cost 2
- `via 10.10.10.2` = Next hop untuk mencapai network tujuan
- `00:00:04` = Waktu route ini ada di tabel routing

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
O    192.168.1.0/24 [110/2] via 10.10.10.1, 00:00:04, GigabitEthernet0/0/0
C    192.168.2.0/24 is directly connected, GigabitEthernet0/0/1
L    192.168.2.1/32 is directly connected, GigabitEthernet0/0/1
```

### 7. Verifikasi Protocol OSPF

```cisco
R1# show ip protocols
```

**Output yang diharapkan:**
```
Routing Protocol is "ospf 1"
  Outgoing update filter list for all interfaces is not set
  Incoming update filter list for all interfaces is not set
  Router ID 10.10.10.1
  It is an area border router
  Number of areas in this router is 1. 1 normal 0 stub 0 nssa
  Maximum path: 4
  Routing for Networks:
    10.10.10.0 0.0.0.255 area 0
    192.168.1.0 0.0.0.255 area 0
  Routing Information Sources:
    Gateway         Distance      Last Update
    10.10.10.2           110      00:00:09
  Distance: (default is 110)
```

### 8. Verifikasi OSPF Interface

```cisco
R1# show ip ospf interface
```

**Output yang diharapkan:**
```
GigabitEthernet0/0/0 is up, line protocol is up
  Internet Address 10.10.10.1/24, Area 0
  Process ID 1, Router ID 10.10.10.1, Network Type BROADCAST, Cost: 1
  Transmit Delay is 1 sec, State DR, Priority 1
  Designated Router (ID) 10.10.10.1, Interface address 10.10.10.1
  Backup Designated Router (ID) 10.10.10.2, Interface address 10.10.10.2
  Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
    oob-resync timeout 40
    Hello due in 00:00:03
  Supports Link-local Signaling (LLS)
  Cisco NSF helper support enabled
  IETF NSF helper support enabled
  Index 1/1/2, flood queue length 0
  Next 0x0(0)/0x0(0)/0x0(0)
  Last flood scan length is 0, maximum is 0
  Last flood scan time is 0 msec, maximum is 0 msec
  Neighbor Count is 1, Adjacent neighbor count is 1
    Adjacent with neighbor 10.10.10.2  (Backup Designated Router)
  Suppress hello for 0 neighbor(s)
```

### 9. Verifikasi Koneksi Antar PC

**Dari PC1 (192.168.1.10):**

```cmd
ping 192.168.1.1     # ke Gateway R1 - HARUS BERHASIL
ping 192.168.1.20    # ke PC2 - HARUS BERHASIL (sama network)
ping 10.10.10.2      # ke R2 - HARUS BERHASIL
ping 192.168.2.1     # ke Gateway R2 - HARUS BERHASIL
ping 192.168.2.10    # ke PC3 - HARUS BERHASIL (beda router via OSPF)
ping 192.168.2.20    # ke PC4 - HARUS BERHASIL (beda router via OSPF)
```

**Dari PC3 (192.168.2.10):**

```cmd
ping 192.168.2.1     # ke Gateway R2 - HARUS BERHASIL
ping 192.168.2.20    # ke PC4 - HARUS BERHASIL (sama network)
ping 10.10.10.1      # ke R1 - HARUS BERHASIL
ping 192.168.1.1     # ke Gateway R1 - HARUS BERHASIL
ping 192.168.1.10    # ke PC1 - HARUS BERHASIL (beda router via OSPF)
ping 192.168.1.20    # ke PC2 - HARUS BERHASIL (beda router via OSPF)
```

### 10. Verifikasi Traceroute

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

### 11. Debugging OSPF (Opsional)

```cisco
! Untuk melihat event OSPF secara real-time
R1# debug ip ospf events

! Untuk melihat adjacency OSPF
R1# debug ip ospf adj

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
| **Routing Protocol** | OSPF Process 1 |
| **Network Advertised** | 10.10.10.0/24, 192.168.1.0/24 |
| **Area** | 0 (Backbone) |
| **Router ID** | 10.10.10.1 |
| **Administrative Distance** | 110 |

### Router 2

| Parameter | Nilai |
|-----------|-------|
| **Hostname** | R2 |
| **Gi0/0/0 IP** | 10.10.10.2/24 |
| **Gi0/0/1 IP** | 192.168.2.1/24 |
| **Routing Protocol** | OSPF Process 1 |
| **Network Advertised** | 10.10.10.0/24, 192.168.2.0/24 |
| **Area** | 0 (Backbone) |
| **Router ID** | 10.10.10.2 |
| **Administrative Distance** | 110 |

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
| PC1 tidak bisa ping ke PC3 | OSPF tidak aktif | Cek `show ip protocols` |
| Route tidak muncul di tabel routing | Network tidak di-advertise | Cek `router ospf 1` dan `network` statement |
| Router tidak membentuk neighbor | Area berbeda | Pastikan area sama (harus area 0) |
| Router tidak membentuk neighbor | Hello/Dead timer berbeda | Pastikan timer sama |
| Router tidak membentuk neighbor | Network type mismatch | Cek `show ip ospf interface` |
| OSPF neighbor stuck in 2-WAY | DR/BDR election | Periksa priority dan network type |
| OSPF neighbor stuck in EXSTART | MTU mismatch | Pastikan MTU sama di kedua ujung |
| OSPF neighbor stuck in LOADING | LSA retransmission | Periksa RTO dan bandwidth |

---

## 📚 Referensi

- [Cisco OSPF Documentation](https://www.cisco.com/c/en/us/support/docs/ip/open-shortest-path-first-ospf/7039-1.html)
- [Cisco OSPF Configuration Guide](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/iproute_ospf/configuration/15-s/iro-15-s-book/iro-cfg.html)
- [OSPF Design Guide](https://www.cisco.com/c/en/us/support/docs/ip/open-shortest-path-first-ospf/13688-11.html)

---