# AWS EC2 Deployment Talimatları

## 1. EC2 Instance Başlat

1. AWS Console'a gir: https://console.aws.amazon.com
2. **EC2 → Launch Instance**
3. Ayarlar:
   - **Name:** food-detection-api
   - **AMI:** Ubuntu 22.04 LTS
   - **Instance type:** t2.medium (minimum)
   - **Key pair:** Yeni oluştur veya mevcut seç
   - **Security Group:** Port 8000 aç (Custom TCP, 0.0.0.0/0)

## 2. EC2'ye Bağlan

```bash
ssh -i your-key.pem ubuntu@YOUR_EC2_IP
```

## 3. Python Ortamını Kur

```bash
sudo apt update
sudo apt install python3-pip python3-venv -y
```

## 4. Proje Dosyalarını Yükle

**Bilgisayarında çalıştır:**
```bash
scp -i your-key.pem -r backend/* ubuntu@YOUR_EC2_IP:~/food-api/
```

**Veya EC2'de git clone:**
```bash
mkdir ~/food-api
cd ~/food-api
# Dosyaları manuel upload et
```

## 5. Bağımlılıkları Kur

```bash
cd ~/food-api
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

## 6. Sunucuyu Başlat

```bash
cd ~/food-api
source venv/bin/activate
python main.py
```

**Arka planda çalıştırmak için:**
```bash
nohup python main.py > api.log 2>&1 &
```

## 7. Test Et

```bash
curl http://YOUR_EC2_IP:8000/health
```

## 8. Firebase'i Güncelle

1. Firebase Console → Firestore
2. `config/server` dokümanı
3. `url` alanını güncelle: `http://YOUR_EC2_IP:8000`

---

## 📁 AWS'e Yüklenecek Dosyalar

```
backend/
├── main.py           # FastAPI sunucu
├── requirements.txt  # Python bağımlılıkları
└── model/
    └── best.pt       # YOLO model (5.5 MB)
```
