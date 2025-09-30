# Mawari Guardian Node (Testnet) 🚀

![Docker Pulls](https://img.shields.io/docker/pulls/mawarinetwork/mawari-node?logo=docker)
![GitHub License](https://img.shields.io/github/license/narokrag/mawari-guardian-node)
![GitHub Stars](https://img.shields.io/github/stars/narokrag/mawari-guardian-node?style=social)

Panduan lengkap untuk menjalankan **Mawari Guardian Node** di jaringan **Mawari Testnet**.  
Guardian Node adalah bagian penting dari infrastruktur terdesentralisasi (DIO) Mawari.

---

## Persyaratan Sistem

**Rekomendasi:**

- 4 vCPU  
- 8 GB RAM  
- 50 GB SSD  

---

## 0) Persiapan Wallet, Token, dan NFT Guardian

1. Buka [testnet.mawari.net](https://testnet.mawari.net), sambungkan wallet kamu, dan pastikan jaringan berada di **Mawari TestNet**.  
2. Dapatkan token MAWARI test melalui faucet di [hub.testnet.mawari.net](https://hub.testnet.mawari.net) (maksimal 2 token per alamat utama).  
3. Di **testnet.mawari.net**, mint hingga **3 NFT Guardian** ke wallet utama. NFT ini akan didelegasikan ke burner/operator wallet yang dibuat otomatis oleh node.

---

## 1) Instal Docker (Ubuntu 24.04)

```bash
sudo apt-get update -y  
sudo apt-get install -y ca-certificates curl gnupg  

sudo install -m 0755 -d /etc/apt/keyrings  
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg  
sudo chmod a+r /etc/apt/keyrings/docker.gpg  

echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu noble stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null  

sudo apt-get update -y  
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin  
```
## ✅ Cek Instalasi Docker

Pastikan Docker sudah terpasang dengan benar:

```bash
docker --version
```
## 🔧 2) Jalankan Guardian Node

Set alamat wallet utama (yang memiliki NFT Guardian):

```bash
export MNTESTNET_IMAGE=us-east4-docker.pkg.dev/mawarinetwork-dev/mwr-net-d-car-uses4-public-docker-registry-e62e/mawari-node:latest  
export OWNER_ADDRESS=0xALAMAT_WALLET_UTAMAMU  

mkdir -p ~/mawari  
docker run -d --name mawari-guardian --pull always --restart unless-stopped \
  -v ~/mawari:/app/cache \
  -e OWNERS_ALLOWLIST=$OWNER_ADDRESS \
  $MNTESTNET_IMAGE  
```
## 🔍 Cek Log Node
```bash
docker logs -f mawari-guardian
```
## 🔍 Cek Alamat Burner Wallet (Operator)
```bash
docker logs mawari-guardian | grep -i "Using burner wallet"
```
## 💰 3) isi Burner Wallet
-dari wallet utama 2 mawari token, kirim 1 mawari token ke burner wallet
## 🪙 4) Delegasikan NFT Guardian ke Burner Wallet

Agar node bisa beroperasi, NFT Guardian dari wallet utama perlu **didelegasikan** ke wallet burner (operator).  

### 🔑 Langkah-langkah

1. Buka halaman [Mawari Testnet Licenses](https://app.testnet.mawari.net/licenses).  
2. Hubungkan **wallet utama** yang memiliki NFT Guardian.  
3. Pilih semua NFT Guardian yang ingin digunakan.  
4. Klik tombol **Delegate**.  
5. Masukkan **alamat burner wallet** (alamat yang muncul di log node).  
6. Konfirmasi transaksi di wallet utama.  

### ✅ Verifikasi Delegasi

Setelah transaksi sukses:  

- Periksa log container:
  ```bash
  docker logs -f mawari-guardian
  ```
## ⚠️ Catatan Penting
- Tanpa **delegasi NFT Guardian**, node tidak akan berjalan.  
- Pastikan alamat **burner wallet** yang dimasukkan sudah benar.  
- Setiap transaksi membutuhkan **token testnet**, jadi pastikan wallet utama memiliki saldo cukup.  
- Jika burner wallet hilang / kunci privat tidak disimpan, maka kontrol atas node dan NFT yang didelegasikan juga hilang.  
- Gunakan server yang aman (aktifkan firewall, SSH key, dan update rutin) untuk menjaga node tetap stabil.  
## 🔐 5) Backup Kunci Privat Burner/Operator

Sangat penting untuk menyimpan kunci privat burner wallet yang dibuat otomatis oleh node.  

## 📂 Lokasi file
Kunci privat tersimpan di file:
~/mawari/flohive-cache.json

### 🔑 Cara mengambil kunci privat
```bash
cat ~/mawari/flohive-cache.json | grep -i "privateKey"
```
### 💡 Tips & Catatan

- `OWNER_ADDRESS` hanya dibutuhkan saat pertama kali membuat container.  
- Jika ingin membuat **burner wallet baru**:
  1. Hentikan container.  
  2. Hapus atau backup file `~/mawari/flohive-cache.json`.  
  3. Jalankan ulang container → faucet token → delegasikan NFT lagi.  
- Direktori `~/mawari` harus **diamankan dan dibackup** secara rutin.  
- Gunakan **SSH key**, aktifkan **firewall**, dan lakukan **update sistem** secara berkala untuk menjaga keamanan node.  
- Selalu cek log node dengan:
```bash
docker logs -f mawari-guardian
```
