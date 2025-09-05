# Pusula Data Science Intern Case Study – Detaylı Rapor

**Hazırlayan:** Utku Esat Sevinç  


##  Projenin Amacı
Bu proje kapsamında fizik tedavi ve rehabilitasyon alanına ait **2235 gözlem** ve **13 sütundan** oluşan bir veri seti analiz edilmiş, temizlenmiş ve modellemeye hazır hale getirilmiştir.  

**Hedef değişken:** `TedaviSuresi` 
Projenin hedefi bir makine öğrenmesi modeli kurmak değildir. Amaç, veri setini **eksiksiz, tutarlı ve modellemeye uygun** hale getirmektir.  

---

##  Veri Seti Özellikleri
- **Gözlem:** 2235  
- **Sütun:** 13  
- **Özellik tipleri:**  
  - Sayısal: `Yas`, `UygulamaSuresi`, `TedaviSuresi`  
  - Kategorik: `Cinsiyet`, `KanGrubu`, `Uyruk`, `Bolum`  
  - Metinsel: `KronikHastalik`, `Alerji`, `Tanilar`  
  - Tanımlayıcı: `HastaNo` (model için kullanılmadı)  

---

##  Exploratory Data Analysis (EDA)

1. **Eksik Veri Analizi**  
   - Eksik veriler `missingno` görselleriyle incelendi.  
   - Eksik değerler en çok `KronikHastalik`, `Alerji`, `Uyruk` sütunlarında görüldü.  
   - Sayısal değişkenlerde (`Yas`, `UygulamaSuresi`) daha az sayıda eksik veri bulundu.  

2. **Sayısal Değişkenler**  
   - `Yas`: Çoğunluk 20–70 yaş arasında yoğunlaşmış, aykırı yaş değerleri tespit edildi (örn. >100).  
   - `UygulamaSuresi`: Başlangıçta `"30 dk"`, `"45 dakika"` gibi metinsel formatta olduğundan dönüştürülmesi gerekti.  
   - `TedaviSuresi`: `"15 Seans"` gibi metin formatındaydı, regex ile sayısala çevrildi.  

3. **Kategorik Değişkenler**  
   - `Cinsiyet`: Dengeli dağılım.  
   - `KanGrubu`: Beklenen dağılımda.  
   - `Bolum`: En çok **Ortopedi**, **Nöroloji**, **Genel Cerrahi** bölümlerinde hasta yoğunluğu var.  

4. **Korelasyon Analizi**  
   - Sayısal değişkenler arası korelasyon düşük çıktı.  
   - Klinik bazlı farklılıklar `TedaviSuresi` üzerinde etkili görünüyor.  

---

##  Data Preprocessing (Ön İşleme)

### 1. Eksik Verilerin Doldurulması
| Sütun               | Tipi      | Eksik Veri Durumu | Doldurma Yöntemi                                          | Neden Bu Yöntem Seçildi                                                                                                                                                    |
| ------------------- | --------- | ----------------- | --------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Yas**             | Sayısal   | Var               | Ortalama (`mean`)                                         | Yaş sürekli bir değişken, ortalama ile doldurmak dağılımı korur.                                                                                                           |
| **UygulamaSuresi**  | Sayısal   | Var               | Ortalama (`mean`)                                         | Süre verisi, ortalama ile doldurmak doğal bir seçimdir.                                                                                                                    |
| **TedaviSuresi**    | Hedef     | Yok               | Regex → Sayısal                                           | Eksik yoktu, sadece `"15 Seans"` → `15` olarak dönüştürüldü.                                                                                                               |
| **Cinsiyet**        | Kategorik | Çok az sayıda     | En sık görülen değer (mod) veya `"Bilinmiyor"` kategorisi | Eksik cinsiyet bilgisi ya veri giriş hatasıdır ya da beyan edilmemiştir. Mod ile doldurmak dağılımı korur, `"Bilinmiyor"` kategorisi eklemek de ayrı bir seçenek olabilir. |
| **KanGrubu**        | Kategorik | Yok / Çok az      | Eksik yoksa işlem yok, varsa `"Bilinmiyor"` kategorisi    | Kan grubu kritik bir tıbbi bilgi olduğu için “boş” değerlerin korunması önemlidir.                                                                                         |
| **Uyruk**           | Kategorik | Var               | `"Bilgi_Yok"`                                             | Eksik milliyet bilgisi ayrı bir kategori olarak değerlendirildi.                                                                                                           |
| **KronikHastalik**  | Metinsel  | Var               | `"Bilgi_Yok"`                                             | Eksik bilgi de anlam taşır, bu yüzden ayrı kategori olarak korundu.                                                                                                        |
| **Alerji**          | Metinsel  | Var               | `"Bilgi_Yok"`                                             | Eksik alerji bilgisi, alerji yok anlamına gelmeyebilir. Bu yüzden eksikliği ayrı tuttuk.                                                                                   |
| **Bolum**           | Kategorik | Yok               | -                                                         | Eksik değer yoktu, işlem yapılmadı.                                                                                                                                        |
| **Tanilar**         | Metinsel  | Çok çeşitli       | Çıkarıldı                                                 | Çok karmaşık serbest metin olduğundan model-ready veri için uygun değildi.                                                                                                 |
| **TedaviAdi**       | Metinsel  | Çok çeşitli       | Çıkarıldı                                                 | Çok detaylı ve serbest metin, bu aşamada model için kullanılmadı.                                                                                                          |
| **UygulamaYerleri** | Metinsel  | Çok çeşitli       | Çıkarıldı                                                 | Çok detaylı serbest metin, preprocessing kapsamında çıkarıldı.                                                                                                             |
| **HastaNo**         | Kimlik    | Yok               | Çıkarıldı                                                 | Sadece ID bilgisiydi, model için anlamsız.                                                                                                                                 |

- **`Yas` (Sayısal)**  
  - Eksikler **ortalama yaş** ile dolduruldu.  
  - Çünkü: Yaş sürekli bir değişken, ortalama ile doldurmak dağılımı korur. Eğer median kullansaydık aykırı değerlerin etkisini azaltırdık, ama veri dengeli olduğu için ortalama uygun görüldü.  

- **`UygulamaSuresi` (Sayısal – dönüştürülmüş)**  
  - Eksikler **ortalama uygulama süresi** ile dolduruldu.  
  - Çünkü: Bu değişken süre olduğu için ortalama değerin atanması mantıklı ve verinin genel yapısını bozmuyor.  
  
  - **`Cinsiyet` (Kategorik)**  
  - Eksikler **Yeni kategori (2)** ile dolduruldu.  
  - Çünkü: Bu değişken süre olduğu için ortalama değerin atanması mantıklı ve verinin genel yapısını bozmuyor.  


- **`KronikHastalik` (Kategorik/Metinsel)**  
  - Eksikler `"Bilgi_Yok"` etiketiyle dolduruldu.  
  - Çünkü: Kronik hastalık bilgisinin yokluğu da model için anlamlı olabilir. “Eksik” değerlerin kaybolmaması adına onları ayrı bir kategori olarak koruduk.  

- **`Alerji` (Kategorik/Metinsel)**  
  - Eksikler `"Bilgi_Yok"` etiketiyle dolduruldu.  
  - Çünkü: Alerji bilgisinin olmaması da bir bilgi taşır. Bazı durumlarda hasta hiç alerji beyan etmemiş olabilir, bunu modelin bilmesi önemlidir.  

- **`Uyruk` (Kategorik)**  
  - Eksikler `"Bilgi_Yok"` etiketiyle dolduruldu.  
  - Çünkü: Hastanın uyruk bilgisinin olmaması yine model açısından farklı bir kategori olarak ele alınabilir.  

---

### 2. Metinsel Sütunların Dönüştürülmesi
- **`UygulamaSuresi`**  
  - `"30 dk"`, `"45 dakika"` → regex ile sadece rakam alındı (`30`, `45`).  
  - Daha sonra float tipine dönüştürüldü.  

- **`TedaviSuresi` (Hedef değişken)**  
  - `"15 Seans"` gibi değerlerden rakam çıkarıldı → `15`.  
  - Float tipine dönüştürüldü.  

---

### 3. Encoding (Kategorik → Sayısal)
- **`Cinsiyet`**  
  - LabelEncoder ile dönüştürüldü: `0 = Kadın`, `1 = Erkek`, `2 = Boş`.  

- **`KanGrubu`, `Bolum`, `Uyruk`**  
  - OneHotEncoding uygulandı.  
  - İlk kategori `drop_first=True` ile çıkarıldı, böylece dummy değişken tuzağından kaçınıldı.  

---

### 4. Feature Engineering
- **`KronikHastalik_Sayisi`**  
  - Kronik hastalık sayısı çıkarıldı. Örn. `"Hipertansiyon, Diyabet"` → `2`.  
  - Eksik olanlar `"Bilgi_Yok"` → `0`.  

- **`Alerji_Var_Mi`**  
  - Alerji bilgisi var mı yok mu diye binary bir sütun oluşturuldu.  
  - `"Bilgi_Yok"` veya `"Yok"` → `0`, diğer tüm değerler → `1`.  

---

### 5. Gereksiz Sütunların Kaldırılması
- `HastaNo`, `Tanilar`, `TedaviAdi`, `UygulamaYerleri` → model için anlam taşımadıkları veya fazla metinsel oldukları için çıkarıldı.  

---

### 6. Sayısal Değişkenlerin Ölçeklenmesi
- `Yas`, `UygulamaSuresi`, `KronikHastalik_Sayisi` → **StandardScaler** ile normalize edildi.  
- Bu sayede farklı ölçekteki değişkenler (ör. yaş 0–100, süre 0–200) aynı ölçekte işlendi.  

---


