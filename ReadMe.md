# ReadMe
---
**Hazırlayan:** Utku Esat Sevinç  
**mail:** uesevinc@outlook.com

## ️ Kullanılan Teknolojiler ve Kütüphaneler
- **Python 3.10+**
- **Pandas** → Veri işleme  
- **NumPy** → Sayısal işlemler  
- **Matplotlib / Seaborn** → Görselleştirme  
- **Missingno** → Eksik veri analizi  
- **Scikit-learn** → Imputer, Encoder, StandardScaler  

---

##  Veri Seti Bilgileri
- **Gözlem sayısı:** 2235  
- **Özellik sayısı:** 13  
- **Hedef değişken:** `TedaviSuresi` (tedavi süresi, seans sayısı)  

**Önemli Sütunlar:**
- `Yas` → Hasta yaşı  
- `Cinsiyet` → Erkek / Kadın  
- `KanGrubu` → Kan grubu bilgisi  
- `Uyruk` → Milliyet  
- `KronikHastalik` → Kronik hastalıklar (liste formatında)  
- `Alerji` → Alerjiler  
- `Bolum` → Tedavi bölümü/klinik  
- `UygulamaSuresi` → Uygulama süresi (dakika)  
- `TedaviSuresi` → **Hedef değişken** (seans sayısı)  

---

##  Yapılan Çalışmalar

### 1. Exploratory Data Analysis (EDA)
- Sayısal değişkenlerin dağılımları incelendi.  
- Kategorik değişkenlerin frekans grafikleri oluşturuldu.  
- Eksik veriler `missingno` ile görselleştirildi.  
- Korelasyon matrisi çıkarıldı.  
- Hedef değişkenin kategorik değişkenlere göre ortalama değerleri analiz edildi.  

### 2. Data Preprocessing (Ön İşleme)
- Eksik veriler dolduruldu (`SimpleImputer` ile mean/constant).  
- Kategorik değişkenler encode edildi (OneHotEncoder, LabelEncoder).  
- `UygulamaSuresi` metin → sayısal dönüştürüldü.  
- `KronikHastalik` → sayısal özellik (`KronikHastalik_Sayisi`).  
- `Alerji` → binary özellik (`Alerji_Var_Mi`).  
- Gereksiz sütunlar (`HastaNo`, `Tanilar`, `TedaviAdi`, `UygulamaYerleri`) kaldırıldı.  
- Sayısal değişkenler (`Yas`, `UygulamaSuresi`, `KronikHastalik_Sayisi`) standardize edildi (`StandardScaler`).  
- `TedaviSuresi` → regex ile sayısal forma dönüştürüldü.  

---

##  Çalıştırma

### 1. Gerekli kütüphaneleri yükle
```bash
pip install pandas numpy matplotlib seaborn scikit-learn missingno

### 2. Dosya tipi ve yolu

file_path = 'C:\\Users\\uesev\\Desktop\\Utku\\Talent_Academy_Case_DT_2025.xlsx' # Dosya yolunu doğru olmalı
Dosya yolunu doğru bir şekilde dosya kendi konumunuza göre eklenmelidir (excel formatında)