# 🔄 Routing BGP (Border Gateway Protocol)

## 📖 Apa itu BGP?

**BGP (Border Gateway Protocol)** adalah protokol routing eksterior (EGP - Exterior Gateway Protocol) yang digunakan untuk bertukar informasi routing antar Autonomous System (AS) di internet. BGP adalah protokol routing yang digunakan sebagai tulang punggung internet untuk menghubungkan berbagai network provider dan enterprise networks.

### 🎯 Tujuan BGP:

1. **Routing Antar AS** - Menghubungkan dan bertukar informasi routing antar Autonomous System
2. **Path Selection** - Memilih jalur terbaik berdasarkan berbagai atribut
3. **Policy-Based Routing** - Mengimplementasikan kebijakan routing berdasarkan kebutuhan bisnis
4. **Skalabilitas** - Mendukung routing untuk jaringan terbesar di dunia (Internet)
5. **Redundansi** - Menyediakan jalur backup untuk ketersediaan tinggi

### 🏷️ Karakteristik BGP:

| Karakteristik | Deskripsi |
|---------------|-----------|
| **Jenis Protokol** | Path-Vector (EGP) |
| **Metrik** | Berbagai atribut (AS-Path, Local Preference, MED, dll) |
| **Administrative Distance** | 20 (eBGP), 200 (iBGP) |
| **Update** | Triggered (saat terjadi perubahan) |
| **Transport** | TCP Port 179 |
| **Mekanisme** | Incremental update (hanya mengirim perubahan) |
| **Loop Prevention** | AS-Path (eBGP), Split Horizon (iBGP) |
| **VLSM** | Mendukung |
| **Authentication** | MD5 authentication |

### 📝 Komponen BGP:

| Komponen | Fungsi |
|----------|--------|
| **AS (Autonomous System)** | Kumpulan router di bawah satu administrasi |
| **AS Number** | Nomor unik untuk setiap AS (1-64511 public, 64512-65535 private) |
| **eBGP** | BGP antar AS yang berbeda |
| **iBGP** | BGP di dalam satu AS |
| **Neighbor/Peer** | Router BGP yang saling terhubung |
| **BGP Attributes** | Informasi yang digunakan untuk memilih jalur terbaik |
| **BGP Table** | Database yang berisi semua route yang diketahui |

### 📊 BGP Attributes (Path Selection):

| Atribut | Kode | Prioritas | Deskripsi |
|---------|------|-----------|-----------|
| **Weight** | - | Tertinggi | Cisco proprietary, lokal ke router |
| **Local Preference** | LP | Tertinggi | Diberikan ke semua router dalam AS |
| **Origin** | OR | IGP > EGP > incomplete | Asal route (IGP, EGP, incomplete) |
| **AS-Path Length** | - | Terpendek | Jumlah AS yang dilalui |
| **Origin Type** | OR | - | IGP, EGP, incomplete |
| **MED (Multi-Exit Discriminator)** | MED | Terendah | Digunakan untuk memilih antar AS |
| **eBGP vs iBGP** | - | eBGP > iBGP | Prefer eBGP over iBGP |
| **IGP Metric** | - | Terendah | Metric ke next-hop |
| **Router ID** | - | Terendah | Router ID terendah |

---

## 🌐 Topologi

![Topologi Routing BGP](topologi.png)

---

## 📊 Tabel Perangkat dan IP Address

### Router 1 (AS 100)

| Perangkat | Antarmuka | IP Address | Netmask | Keterangan |
|-----------|-----------|------------|---------|------------|
| **Router 1** | Gi0/0/0 | 10.10.10.1 | 255.255.255.0 | Ke Router 2 (eBGP) |
| **Router 1** | Gi0/0/1 | 192.168.1.1 | 255.255.255.0 | Ke Switch 1 |

### Router 2 (AS 200)

| Perangkat | Antarmuka | IP Address | Netmask | Keterangan |
|-----------|-----------|------------|---------|------------|
| **Router 2** | Gi0/0/0 | 10.10.10.2 | 255.255.255.0 | Ke Router 1 (eBGP) |
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

### Router 1 (AS 100)

| Antarmuka | IP Address | Subnet Mask | Status | Keterangan |
|-----------|------------|-------------|--------|------------|
| Gi0/0/0 | 10.10.10.1 | 255.255.255.0 | Up | Link ke Router 2 (eBGP) |
| Gi0/0/1 | 192.168.1.1 | 255.255.255.0 | Up | Link ke Switch 1 |

### Router 2 (AS 200)

| Antarmuka | IP Address | Subnet Mask | Status | Keterangan |
|-----------|------------|-------------|--------|------------|
| Gi0/0/0 | 10.10.10.2 | 255.255.255.0 | Up | Link ke Router 1 (eBGP) |
| Gi0/0/1 | 192.168.2.1 | 255.255.255.0 | Up | Link ke Switch 2 |

### BGP Configuration

| Router | AS Number | Neighbor | Neighbor AS | Keterangan |
|--------|-----------|----------|-------------|------------|
| **R1** | 100 | 10.10.10.2 | 200 | eBGP Peer |
| **R2** | 200 | 10.10.10.1 | 100 | eBGP Peer |

### Network Advertisement

| Router | Network | AS | Keterangan |
|--------|---------|-----|------------|
| **R1** | 192.168.1.0/24 | 100 | Network lokal di AS 100 |
| **R2** | 192.168.2.0/24 | 200 | Network lokal di AS 200 |

---

## ⚙️ Langkah-Langkah Konfigurasi

### 1. Konfigurasi Router 1 (AS 100)

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
R1(config-if)# description === LINK TO ROUTER 2 (eBGP) ===
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

#### 1.3 Konfigurasi BGP di Router 1

```cisco
R1> enable
R1# configure terminal

! Aktifkan routing BGP dengan AS 100
R1(config)# router bgp 100

! Identifikasi Router ID (opsional)
R1(config-router)# bgp router-id 1.1.1.1

! Tambahkan network yang akan di-advertise
R1(config-router)# network 192.168.1.0 mask 255.255.255.0

! Konfigurasi neighbor eBGP ke Router 2 (AS 200)
R1(config-router)# neighbor 10.10.10.2 remote-as 200

! Tambahkan network yang akan di-advertise ke neighbor
R1(config-router)# neighbor 10.10.10.2 advertise-map NETWORK_ADVERTISE

! Exit dari mode routing
R1(config-router)# exit

! Buat route-map untuk advertise network
R1(config)# route-map NETWORK_ADVERTISE permit 10
R1(config-route-map)# match ip address 1
R1(config-route-map)# exit

! Buat access-list untuk network yang akan di-advertise
R1(config)# access-list 1 permit 192.168.1.0 0.0.0.255

! Verifikasi konfigurasi
R1(config)# end
R1# copy running-config startup-config
```

**Penjelasan:**
- `router bgp 100` : Mengaktifkan proses routing BGP dengan AS 100
- `bgp router-id 1.1.1.1` : Menentukan Router ID (gunakan loopback untuk production)
- `network 192.168.1.0 mask 255.255.255.0` : Mengadvertise network 192.168.1.0/24 ke BGP
- `neighbor 10.10.10.2 remote-as 200` : Menentukan neighbor BGP dengan AS 200
- `neighbor 10.10.10.2 advertise-map NETWORK_ADVERTISE` : Menggunakan route-map untuk advertise
- `route-map NETWORK_ADVERTISE permit 10` : Membuat route-map untuk mengontrol advertise
- `match ip address 1` : Memfilter berdasarkan access-list
- `access-list 1 permit 192.168.1.0 0.0.0.255` : Access-list untuk network yang di-advertise

---

### 2. Konfigurasi Router 2 (AS 200)

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
R2(config-if)# description === LINK TO ROUTER 1 (eBGP) ===
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

#### 2.3 Konfigurasi BGP di Router 2

```cisco
R2> enable
R2# configure terminal

! Aktifkan routing BGP dengan AS 200
R2(config)# router bgp 200

! Identifikasi Router ID (opsional)
R2(config-router)# bgp router-id 2.2.2.2

! Tambahkan network yang akan di-advertise
R2(config-router)# network 192.168.2.0 mask 255.255.255.0

! Konfigurasi neighbor eBGP ke Router 1 (AS 100)
R2(config-router)# neighbor 10.10.10.1 remote-as 100

! Tambahkan network yang akan di-advertise ke neighbor
R2(config-router)# neighbor 10.10.10.1 advertise-map NETWORK_ADVERTISE

! Exit dari mode routing
R2(config-router)# exit

! Buat route-map untuk advertise network
R2(config)# route-map NETWORK_ADVERTISE permit 10
R2(config-route-map)# match ip address 1
R2(config-route-map)# exit

! Buat access-list untuk network yang akan di-advertise
R2(config)# access-list 1 permit 192.168.2.0 0.0.0.255

! Verifikasi konfigurasi
R2(config)# end
R2# copy running-config startup-config
```

**Penjelasan:**
- `router bgp 200` : Mengaktifkan proses routing BGP dengan AS 200
- `bgp router-id 2.2.2.2` : Menentukan Router ID
- `network 192.168.2.0 mask 255.255.255.0` : Mengadvertise network 192.168.2.0/24
- `neighbor 10.10.10.1 remote-as 100` : Menentukan neighbor BGP dengan AS 100
- `neighbor 10.10.10.1 advertise-map NETWORK_ADVERTISE` : Menggunakan route-map untuk advertise

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

#### 5.1 PC1 (Terhubung ke SW1 - AS 100)

| Parameter | Nilai |
|-----------|-------|
| IP Address | 192.168.1.10 |
| Netmask | 255.255.255.0 |
| Gateway | 192.168.1.1 |
| DNS | 8.8.8.8 |

#### 5.2 PC2 (Terhubung ke SW1 - AS 100)

| Parameter | Nilai |
|-----------|-------|
| IP Address | 192.168.1.20 |
| Netmask | 255.255.255.0 |
| Gateway | 192.168.1.1 |
| DNS | 8.8.8.8 |

#### 5.3 PC3 (Terhubung ke SW2 - AS 200)

| Parameter | Nilai |
|-----------|-------|
| IP Address | 192.168.2.10 |
| Netmask | 255.255.255.0 |
| Gateway | 192.168.2.1 |
| DNS | 8.8.8.8 |

#### 5.4 PC4 (Terhubung ke SW2 - AS 200)

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

### 3. Verifikasi Neighbor BGP

```cisco
R1# show ip bgp summary
```

**Output yang diharapkan:**
```
BGP router identifier 1.1.1.1, local AS number 100
BGP table version is 1, main routing table version 1

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.10.10.2      4          200       6       6        1    0    0 00:01:23        1
```

**Penjelasan:**
- `Neighbor` = IP address neighbor
- `V` = BGP version
- `AS` = AS number neighbor
- `MsgRcvd` = Jumlah pesan diterima
- `MsgSent` = Jumlah pesan dikirim
- `TblVer` = Table version
- `Up/Down` = Waktu neighbor telah aktif
- `State/PfxRcd` = Status dan jumlah prefix diterima

### 4. Verifikasi Tabel BGP

```cisco
R1# show ip bgp
```

**Output yang diharapkan:**
```
BGP table version is 1, local router ID is 1.1.1.1
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
*>   192.168.1.0/24   0.0.0.0                  0         32768 i
*>   192.168.2.0/24   10.10.10.2               0             0 200 i
```

**Penjelasan:**
- `Network` = Network tujuan
- `Next Hop` = Next hop untuk mencapai network
- `Metric` = MED (Multi-Exit Discriminator)
- `LocPrf` = Local Preference (untuk iBGP)
- `Weight` = Weight (Cisco proprietary)
- `Path` = AS-Path yang dilalui

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
B    192.168.2.0/24 [20/0] via 10.10.10.2, 00:01:00
```

**Penjelasan:**
- `B` = Route dari BGP
- `[20/0]` = Administrative distance 20 (eBGP), metric 0
- `via 10.10.10.2` = Next hop untuk mencapai network tujuan
- `00:01:00` = Waktu route ini ada di tabel routing

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
B    192.168.1.0/24 [20/0] via 10.10.10.1, 00:01:00
C    192.168.2.0/24 is directly connected, GigabitEthernet0/0/1
L    192.168.2.1/32 is directly connected, GigabitEthernet0/0/1
```

### 7. Verifikasi BGP Neighbor Detail

```cisco
R1# show ip bgp neighbors 10.10.10.2
```

**Output yang diharapkan:**
```
BGP neighbor is 10.10.10.2, remote AS 200, external link
  BGP version 4, remote router ID 2.2.2.2
  BGP state = Established, up for 00:01:23
  Last read 00:00:23, last write 00:00:18, hold time is 180, keepalive interval is 60 seconds
  Neighbor capabilities:
    Route refresh: advertised and received(new)
    Address family IPv4 Unicast: advertised and received
  Message statistics:
    InQ depth is 0
    OutQ depth is 0
                         Sent       Rcvd
    Opens:                  1          1
    Notifications:          0          0
    Updates:                2          2
    Keepalives:            10         10
    Route Refresh:          0          0
    Total:                 13         13
  Default minimum time between advertisement runs is 30 seconds

 For address family: IPv4 Unicast
  BGP table version 1, neighbor version 1/0
  Output queue size : 0
  Index 1, Advertise bit 0
  1 accepted prefixes consume 48 bytes
  Prefix advertised 1
  Paths 1 available, 1 best

  Address tracking is enabled, the RIB does have a route to 10.10.10.2
  Connections established 1; dropped 0
  Last reset never
  External BGP neighbor may be up to 255 hops away.
Connection state is ESTAB, I/O status: 1, unread input bytes: 0
Connection is ECN Disabled
Local host: 10.10.10.1, Local port: 46551
Foreign host: 10.10.10.2, Foreign port: 179
```

### 8. Verifikasi Protocol BGP

```cisco
R1# show ip protocols
```

**Output yang diharapkan:**
```
Routing Protocol is "bgp 100"
  Outgoing update filter list for all interfaces is not set
  Incoming update filter list for all interfaces is not set
  IGP synchronization is disabled
  Automatic route summarization is disabled
  Neighbor(s):
    Address          FiltIn FiltOut DistIn DistOut Weight RouteMap
    10.10.10.2                                             NETWORK_ADVERTISE
  Address tracking is enabled, the RIB does have a route to 10.10.10.2
  Distance: external 20 internal 200 local 200
```

### 9. Verifikasi Koneksi Antar AS

**Dari PC1 (192.168.1.10 - AS 100):**

```cmd
ping 192.168.1.1     # ke Gateway R1 - HARUS BERHASIL
ping 192.168.1.20    # ke PC2 - HARUS BERHASIL (sama AS)
ping 10.10.10.2      # ke R2 - HARUS BERHASIL
ping 192.168.2.1     # ke Gateway R2 - HARUS BERHASIL (via BGP)
ping 192.168.2.10    # ke PC3 - HARUS BERHASIL (beda AS via BGP)
ping 192.168.2.20    # ke PC4 - HARUS BERHASIL (beda AS via BGP)
```

**Dari PC3 (192.168.2.10 - AS 200):**

```cmd
ping 192.168.2.1     # ke Gateway R2 - HARUS BERHASIL
ping 192.168.2.20    # ke PC4 - HARUS BERHASIL (sama AS)
ping 10.10.10.1      # ke R1 - HARUS BERHASIL
ping 192.168.1.1     # ke Gateway R1 - HARUS BERHASIL (via BGP)
ping 192.168.1.10    # ke PC1 - HARUS BERHASIL (beda AS via BGP)
ping 192.168.1.20    # ke PC2 - HARUS BERHASIL (beda AS via BGP)
```

### 10. Verifikasi Traceroute Antar AS

**Dari PC1 (AS 100) ke PC3 (AS 200):**

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

### 11. Debugging BGP (Opsional)

```cisco
! Untuk melihat event BGP secara real-time
R1# debug ip bgp events

! Untuk melihat updates BGP
R1# debug ip bgp updates

! Matikan debugging
R1# undebug all
```

---

## 📊 Ringkasan Konfigurasi

### Router 1 (AS 100)

| Parameter | Nilai |
|-----------|-------|
| **Hostname** | R1 |
| **AS Number** | 100 |
| **Gi0/0/0 IP** | 10.10.10.1/24 |
| **Gi0/0/1 IP** | 192.168.1.1/24 |
| **Router ID** | 1.1.1.1 |
| **BGP Neighbor** | 10.10.10.2 (AS 200) |
| **Network Advertised** | 192.168.1.0/24 |
| **Administrative Distance (eBGP)** | 20 |

### Router 2 (AS 200)

| Parameter | Nilai |
|-----------|-------|
| **Hostname** | R2 |
| **AS Number** | 200 |
| **Gi0/0/0 IP** | 10.10.10.2/24 |
| **Gi0/0/1 IP** | 192.168.2.1/24 |
| **Router ID** | 2.2.2.2 |
| **BGP Neighbor** | 10.10.10.1 (AS 100) |
| **Network Advertised** | 192.168.2.0/24 |
| **Administrative Distance (eBGP)** | 20 |

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

| Perangkat | AS | Network | IP Address | Gateway |
|-----------|-----|---------|------------|---------|
| **PC1** | 100 | 192.168.1.0/24 | 192.168.1.10 | 192.168.1.1 |
| **PC2** | 100 | 192.168.1.0/24 | 192.168.1.20 | 192.168.1.1 |
| **PC3** | 200 | 192.168.2.0/24 | 192.168.2.10 | 192.168.2.1 |
| **PC4** | 200 | 192.168.2.0/24 | 192.168.2.20 | 192.168.2.1 |

---

## 🔧 Troubleshooting

| Masalah | Kemungkinan Penyebab | Solusi |
|---------|---------------------|--------|
| BGP neighbor tidak established | IP address salah atau unreachable | Cek `show ip interface brief` dan ping neighbor |
| BGP neighbor tidak established | AS number salah | Pastikan `remote-as` sesuai dengan AS neighbor |
| BGP neighbor tidak established | ACL blocking TCP port 179 | Periksa ACL dan pastikan TCP 179 diizinkan |
| Route tidak muncul di tabel routing | Network tidak di-advertise | Cek `network` statement dan `show ip bgp` |
| Route tidak di-advertise ke neighbor | Route-map atau ACL salah | Cek route-map dan access-list |
| Route tidak di-advertise ke neighbor | IGP tidak memiliki route | Pastikan route tersedia di tabel routing IGP |
| Route tidak muncul di tabel BGP | Network tidak dalam IGP | Pastikan network ada di tabel routing |
| BGP neighbor state idle | TCP connection gagal | Cek konektivitas dan firewall |
| BGP neighbor state active | Remote AS tidak merespon | Cek konfigurasi remote router |
| BGP table tidak update | Hold timer expired | Cek konektivitas dan timer |

---

## 📚 Referensi

- [Cisco BGP Documentation](https://www.cisco.com/c/en/us/support/docs/ip/border-gateway-protocol-bgp/13753-25.html)
- [Cisco BGP Configuration Guide](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/iproute_bgp/configuration/15-s/irg-15-s-book/irg-bgp-overview.html)
- [BGP Best Path Selection Algorithm](https://www.cisco.com/c/en/us/support/docs/ip/border-gateway-protocol-bgp/13753-25.html)

---