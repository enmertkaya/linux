# DevOps İçeriği — Bölüm 2: Linux

Bu repo, **Linux** bölümünde yapılanların uygulamalı özetidir.
Tüm komutlar Vagrant ile kurulan CentOS ve Ubuntu sanal makinelerinde gerçekten çalıştırılmış, çıktılar bu dokümana birebir eklenmiştir.
Her komutun hangi makinede (**CentOS** / **Ubuntu**) çalıştırıldığı belirtilmiştir.

> **Ortam:** Windows 11 Pro · VirtualBox 7.2.6 · Vagrant 2.4.9 · Git Bash · CentOS Stream 9 · Ubuntu 22.04.5 LTS

---

## İçindekiler

1. [Linux'a Giriş (Introduction to Linux)](#1-linuxa-giriş-introduction-to-linux)
2. [Komut Özeti (Cheat Sheet)](#2-komut-özeti-cheat-sheet)

---

## 1. Linux'a Giriş (Introduction to Linux)

### 1.1 Bölümde neler var?

| # | Konu | # | Konu |
|---|---|---|---|
| 1 | Intro to Linux | 6 | Sudo |
| 2 | Basic CLI Commands | 7 | Software Management |
| 3 | Understanding Files in Linux | 8 | Services & Processes |
| 4 | Filters & Redirection | 9 | Good to Know Commands |
| 5 | Users & Group | 10 | Server Management (web ve veritabanı sunucusu kurulumu) |

### 1.2 Linux nedir?

- **Linux** aslında bir **çekirdektir (kernel)**: donanım ile programlar arasındaki köprü.
  1991'de **Linus Torvalds** tarafından yazıldı, **Unix benzeridir** ve **GPL** lisansıyla dağıtılır.
- **Açık kaynak (open source):** kaynak kodu herkese açıktır; incelenebilir, değiştirilebilir, yeniden dağıtılabilir.
- Kernel + araçlar + paket yöneticisi bir araya gelince **dağıtım (distro)** oluşur: CentOS, Ubuntu, RHEL...

Çalışan kernel sürümü:

**CentOS**

```bash
uname -r
```

Çıktı:

```text
5.14.0-514.el9.x86_64
```

**Ubuntu**

```bash
uname -r
```

Çıktı:

```text
5.15.0-194-generic
```

### 1.3 Neden Linux?

| Neden | Açıklama |
|---|---|
| **Open Source** | Ücretsiz, kodu açık |
| **Community Support** | Devasa topluluk, her sorunun cevabı bir yerde var |
| **Geniş donanım desteği** | Sunucudan Raspberry Pi'ye, routera kadar |
| **Customization** | Her parçası değiştirilebilir |
| **Sunucuların çoğu Linux** | Bulut ve veri merkezlerinin standart işletim sistemi |
| **Automation** | Her şey komut satırından yapılabildiği için script'lenebilir |
| **Security** | Windows'a kıyasla daha güvenli kabul edilir |

### 1.4 Linux prensipleri

| Prensip | Anlamı |
|---|---|
| **Everything is a file** | Donanım dahil her şey bir dosya olarak görünür (disk = `/dev/sda`) |
| **Small, single-purpose programs** | Her komut tek bir işi iyi yapar (`ls` listeler, `wc` sayar) |
| **Programları zincirleme** | `\|` (pipe) ile küçük programlar birleştirilip karmaşık işler yapılır |
| **Avoid captive user interface** | Komutlar etkileşimli ekran yerine çıktı üretir, böylece otomasyona uygundur |
| **Config = text file** | Ayarlar düz metin dosyalarında tutulur, bunlar da **`/etc`** altındadır |

**"Her şey dosyadır":** disk ve "kara delik" aygıtı `/dev` altında dosya olarak durur.

**CentOS**

```bash
ls -l /dev/sda /dev/null
```

Çıktı:

```text
crw-rw-rw-. 1 root root 1, 3 Oct 20 07:29 /dev/null
brw-rw----. 1 root disk 8, 0 Oct 20 07:29 /dev/sda
```

Satır başındaki `b` = **block device** (disk), `c` = **character device**.

**Zincirleme:** `ls` dosyaları listeler, `wc -l` satır sayar; pipe ile `/etc` altındaki dosya sayısı bulunur.

**CentOS**

```bash
ls /etc | wc -l
```

Çıktı:

```text
191
```

**Ubuntu** (aynı komut)

```text
176
```

### 1.5 Linux dağıtımları

Popüler sunucu dağıtımları: **Red Hat Enterprise Linux (RHEL)**, **Ubuntu Server**, **CentOS**, **SUSE Enterprise Linux**.
Sektörde iki büyük aile var; ayrım **paket formatına** göre yapılır:

| Aile | Paket formatı | Paket aracı | Örnek dağıtımlar |
|---|---|---|---|
| **RPM tabanlı** | `.rpm` | `rpm`, `yum` / `dnf` | RHEL, CentOS, Oracle Linux, Amazon Linux (AWS) |
| **Debian tabanlı** | `.deb` | `dpkg`, `apt` | Ubuntu Server, Kali Linux |

Bizim iki makinemiz iki aileyi de temsil ediyor. Dağıtım bilgisi de bir metin dosyasında durur:

**CentOS**

```bash
cat /etc/os-release | head -4
```

Çıktı:

```text
NAME="CentOS Stream"
VERSION="9"
ID="centos"
ID_LIKE="rhel fedora"
```

**Ubuntu**

```bash
cat /etc/os-release | head -4
```

Çıktı:

```text
PRETTY_NAME="Ubuntu 22.04.5 LTS"
NAME="Ubuntu"
VERSION_ID="22.04"
VERSION="22.04.5 LTS (Jammy Jellyfish)"
```

Aynı programın (`bash`) paket bilgisi her ailede kendi aracıyla sorgulanır:

**CentOS** (RPM)

```bash
rpm -q bash
```

Çıktı:

```text
bash-5.1.8-9.el9.x86_64
```

**Ubuntu** (Debian)

```bash
dpkg -l bash | tail -1
```

Çıktı:

```text
ii  bash           5.1-6ubuntu1.1 amd64        GNU Bourne Again SHell
```

### 1.6 Önemli dizinler

Windows'taki "klasör"e Linux'ta **dizin (directory)** denir. Her şey kök dizin `/` altından başlar.

| Amaç | Dizinler |
|---|---|
| Ev dizinleri | `/root` (root kullanıcısı), `/home/<kullanici>` |
| Kullanıcı komutları | `/bin`, `/usr/bin`, `/usr/local/bin` |
| Sistem (yönetici) komutları | `/sbin`, `/usr/sbin`, `/usr/local/sbin` |
| Ek bağlama noktaları | `/media`, `/mnt` |
| Yapılandırma | `/etc` |
| Geçici dosyalar | `/tmp` |
| Kernel ve bootloader | `/boot` |
| Sunucu verisi | `/var`, `/srv` |
| Sistem bilgisi | `/proc`, `/sys` |
| Paylaşılan kütüphaneler | `/lib`, `/usr/lib`, `/usr/local/lib` |

**CentOS**

```bash
ls /
```

Çıktı:

```text
afs
bin
boot
dev
etc
home
lib
lib64
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
vagrant
var
```

**Ubuntu**

```bash
ls /
```

Çıktı:

```text
bin
boot
dev
etc
home
lib
lib32
lib64
libx32
lost+found
media
mnt
opt
proc
root
run
sbin
snap
srv
sys
tmp
usr
vagrant
var
```

Temel dizinler iki dağıtımda da aynı. Farklar dağıtıma özgü: Ubuntu'da `snap` (Snap paketleri) ve `lost+found` var.
`vagrant` dizini ise Vagrant'ın Windows'taki proje klasörünü VM'e bağladığı paylaşımlı klasördür.

### 1.7 Akılda kalsın

- Linux bir **Unix benzeri kernel**, **GPL** lisanslı, yaratıcısı **Linus Torvalds**.
- RHEL & CentOS **RPM**, Ubuntu **Debian** tabanlıdır.
- Yapılandırma dosyaları düz metindir ve **`/etc`** altında durur.
- Sunucu verileri **`/var`** ve **`/srv`** altında tutulur.

---

## 2. Komut Özeti (Cheat Sheet)

| Komut | İşlevi |
|---|---|
| `uname -r` | Çalışan kernel sürümünü gösterir |
| `cat /etc/os-release` | Dağıtım adını ve sürümünü gösterir |
| `ls /` | Kök dizindeki dizinleri listeler |
| `ls -l /dev/sda` | Diskin aygıt dosyasını gösterir ("her şey dosyadır") |
| `komut1 \| komut2` | Pipe: ilk komutun çıktısını ikinciye verir |
| `wc -l` | Satır sayar |
| `rpm -q <paket>` | (RPM / CentOS) Paketin kurulu sürümünü gösterir |
| `dpkg -l <paket>` | (Debian / Ubuntu) Paketin kurulu sürümünü gösterir |
