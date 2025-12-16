# CNN ile Özel Görüntü Sınıflandırma Projesi (BLG-407)

Bu proje, BLG-407 Makine Öğrenmesi dersi kapsamında, **kendi toplanmış görüntü verisi** ile üç farklı CNN yaklaşımını (Transfer Learning, Temel CNN, Geliştirilmiş CNN) karşılaştırmalı olarak uygular.

## Proje Yapısı
- model1_transfer_learning.ipynb — VGG16 ile Transfer Learning (ImageNet ağırlıkları, taban dondurulmuş)
- model2_basic_cnn.ipynb — Sıfırdan Basit CNN (baseline)
- model3_advanced_cnn.ipynb — Geliştirilmiş CNN (augmentation + hiperparametre)
- dataset/
	- fork/
	- spoon/

## Veri Seti Kuralları (Ödev)
- En az iki sınıf, her sınıfta **50+** özgün görsel (toplam **100+**).
- Görseller **öğrenci tarafından çekilmiş** olmalı; internetten hazır dataset kabul edilmez.
- Çeşitlilik: farklı **açı/ışık/arka plan**.
- Boyut: **128×128** önerilir. Klasör yapısı:
	- dataset/
		- sınıf1/
		- sınıf2/

## Kurulum ve Çalıştırma

### Google Colab (Önerilen)
1. Repoyu indirin/klonlayın, `dataset/` klasörünü Drive’a yükleyin.
2. Notebook’u Colab’de açın, ilk hücrede Drive’ı bağlayın.
3. `PROJECT_PATH` değerini Drive’daki proje yoluna ayarlayın.
4. Tüm hücreleri sırayla çalıştırın.

### VS Code (Windows) — Yerel Çalıştırma
1. Drive mount hücresini **yorumlayın/silin**.
2. `PROJECT_PATH` değerini yerel yolunuza ayarlayın (örnek):
	 - `PROJECT_PATH = r"C:/Users/<kullanici>/Desktop/vsc/CNN-Image-Classification-Project"`
3. Gerekli paketleri kurun:
   
	 ```powershell
	 pip install tensorflow matplotlib numpy
	 ```

## Notebook Akışı ve Beklentiler

### Model 1 — Transfer Learning (VGG16)
- `VGG16(include_top=False, weights='imagenet', input_shape=(128,128,3))`
- `base_model.trainable = False` (taban dondurulur)
- Tepe: `Flatten → Dense(128,relu) → Dropout(0.5) → Dense(1,sigmoid)`
- Çıktı: Eğitim/Doğrulama **accuracy/loss** grafikleri, **validation accuracy**.

#### Kod Akışı Açıklamaları
- Drive bağlama ve yol ayarları: Colab/yerel farklarına göre `PROJECT_PATH` düzenlenir.
- Veri yükleme: `image_dataset_from_directory` ile eğitim/validasyon ayrımı (`validation_split=0.2`).
- Model kurulumu: VGG16 tabanı dondurulur, üst katmanlar eklenir ve derlenir.
- Eğitim ve grafikler: accuracy/loss grafikleri çizilir, validation accuracy raporlanır.
- Hata analizi: Yanlış sınıflamalar görselleştirilir.

### Model 2 — Temel CNN (Baseline)
- Blok: `Rescaling + 3×(Conv2D/MaxPool) → Flatten → Dense(128) → Dense(1,sigmoid)`
- `Adam(lr=0.0005)`, `epochs≈30`, `batch_size=16`
- Çıktı: Grafikler + **validation accuracy**; yanlış örnekler görselleştirilir.

#### Kod Akışı Açıklamaları
- Ortam hazırlığı: Drive bağlama (Colab) veya yerel yol ayarı.
- Veri hazırlığı: `cache+prefetch` ile veri boru hattı optimize edilir.
- Mimari: Rescaling + 3×(Conv+Pool) → Flatten → Dense blokları.
- Eğitim ve grafikler: 30 epoch; accuracy/loss grafikleri; validation accuracy.
- Hata analizi: Eşikleme ile yanlış sınıflamalar görselleştirilir.

### Model 3 — Geliştirilmiş CNN
- **Data augmentation** (RandomFlip/Rotation/Zoom) + `Dropout(0.2)`
- `Adam(lr=0.001)`, `batch_size=32`, **EarlyStopping(val_accuracy, patience=7)**
- En az 3 hiperparametreyi değiştirin; deney tablosu ve yorum ekleyin.

#### Kod Akışı Açıklamaları
- Veri artırımı: `RandomFlip/Rotation/Zoom` ile augmentation bloğu ve `map(...)` kullanımı.
- Mimari: `Dropout(0.2)`, `Adam(lr=0.001)`; model derleme ve özet.
- EarlyStopping: `val_accuracy` takibi ve en iyi ağırlıkların geri yüklenmesi.
- Eğitim ve grafikler: accuracy/loss; final validation accuracy.
- Hata analizi: Yanlış örneklerin tespiti ve görselleştirme.

## Değerlendirme ve Raporlama
- Eğitim/Doğrulama **accuracy/loss** grafikleri zorunlu.
- **Validation accuracy** raporlanır; yanlış sınıflanan örneklerden görseller gösterilir.
- Model 3 için: Hangi değişikliklerin performansı arttırdığını **tablo** ve **yorum** ile açıklayın.

### Sunum Üst Bilgileri (Notebook Başına)
- Adınız, Soyadınız, Okul Numaranız, GitHub Repo Bağlantısı başlıkları notebookların en üstünde yer alır.
- GitHub Repo örnek: https://github.com/kullanici_adi/CNN_siniflandirma

### Ödev Teslim Kuralları (Özet)
- Teslim: Son 2 hafta yüzyüze kontrol; sonrasında kabul edilmez.
- Yöntem: Sadece GitHub; e-posta veya manuel teslim kabul edilmez.
- Kapsam: Tüm öğrenciler sorumlu (dersi önce alanlar dahil).
- Değerlendirme: Sözlü sunum, kod akışı düzeni, projeye hakimiyet; repo ve `.ipynb` zorunlu.

### Rubrikten Kritik Maddeler
- Veri seti: Özgün çekimler, 50+ görsel/sınıf, 128×128 önerisi.
- Model1: Transfer learning/fine-tuning, grafikler ve test doğruluğu.
- Model2: Basit CNN, grafikler ve test doğruluğu.
- Model3: ≥3 hiperparametre denemesi, augmentation, performans tablosu ve analiz; Model2’ye göre iyileşme beklenir.
- Dökümantasyon: README ve notebook markdown açıklamaları açık ve düzenli olmalı.

## Sözlü Sunum İçin İpuçları
- Hangi model neden daha iyi? Transfer learning’in avantaj/dezavantajları.
- Overfitting nasıl teşhis edildi ve nasıl azaltıldı (augmentation, dropout)?
- Hiperparametre değişikliklerinin etkisini grafikler ve tablo ile bağlayın.

## Notlar ve Yaygın Hatalar
- **Yol hataları:** Yerelde `PROJECT_PATH` güncellenmeli; Colab yolu yerel ortamda çalışmaz.
- **Bellek hataları:** Doğrulama verisini NumPy’a alırken `BATCH_SIZE` düşürün veya daha az batch analiz edin.
- **Dosya isimleri:** Notebook adları bu repoda `model1_transfer_learning.ipynb`, `model2_basic_cnn.ipynb`, `model3_advanced_cnn.ipynb` biçimindedir.

## Çalıştırma Komutları (Yerel)

```powershell
pip install tensorflow matplotlib numpy
```

Yerelde çalışırken her notebookta Drive mount hücresini yorumlayıp `PROJECT_PATH` değerini aşağıdaki gibi ayarlayın:

```python
PROJECT_PATH = r"C:/Users/<kullanici>/Desktop/vsc/CNN-Image-Classification-Project"
```

Bu README, ödev izlencesine uygun çalıştırma ve raporlama akışını özetler; notebook’larda eklenen Markdown bölümleri detaylı yönergeler sunar.