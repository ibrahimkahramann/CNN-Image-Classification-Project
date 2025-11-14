# CNN ile Özel Görüntü Sınıflandırma Projesi (BLG-407)

Bu proje, BLG-407 Makine Öğrenmesi dersi kapsamında, özel olarak toplanmış bir görüntü veri seti kullanılarak Keras ile üç farklı CNN modeli geliştirmek amacıyla yapılmıştır.

Projenin amacı, "çatal" (fork) ve "kaşık" (spoon) sınıflarını ayırt edebilen bir model eğitmek ve farklı mimarilerin (Transfer Learning, Temel CNN, Gelişmiş CNN) performansını karşılaştırmaktır.

## Proje Bileşenleri

Proje, hocanın isterleri doğrğultusunda 3 ana Jupyter Notebook dosyasından oluşmaktadır:

1.  **`model1.ipynb` (Transfer Learning):** VGG16 gibi state-of-the-art bir mimariyi "Transfer Learning" (ince ayar) ile kullanarak veri setindeki temel başarıyı ölçer.
2.  **`model2.ipynb` (Temel CNN):** Sıfırdan, basit bir CNN mimarisi tasarlayarak bir "temel model" (baseline) oluşturur.
3.  **`model3.ipynb` (Gelişmiş CNN):** `model2`'deki mimariyi alır ve `Data Augmentation` ile `Dropout`, `Batch Size`, `Learning Rate` gibi hocanın istediği hiperparametreleri sistematik olarak deneyerek performansı artırmayı hedefler.

## Veri Seti

Veri seti, "çatal" (fork) ve "kaşık" (spoon) olmak üzere 2 sınıftan oluşmaktadır.
Tüm görüntüler, farklı açı, ışık ve arka plan koşullarında bizzat proje sahibi tarafından telefon kamerasıyla çekilmiştir.

* **Görsel Sayısı:** 100+ (Her sınıf için 50+ adet)
* **Çözünürlük:** Tüm görüntüler $128\times128$ piksele yeniden boyutlandırılmıştır.
* **Veri Yapısı:** `dataset/fork/` ve `dataset/spoon/` klasör yapısına uygundur.

## Sonuçlar ve Modellerin Karşılaştırılması

Üç model de aynı veri seti üzerinde eğitilmiş ve "Test (Validation) Doğruluğu" değerleri karşılaştırılmıştır.

| Model | Mimari | Test Doğruluğu | Temel Notlar |
| :--- | :--- | :--- | :--- |
| **Model 1** | Transfer Learning (VGG16) | **~%92.00** | **En Başarıı Model.** VGG16'nın hazır bilgisi sayesinde zorlu veri setini kolayca tanıdı. |
| **Model 2** | Temel CNN (Sıfırdan) | %64.00 | **Temel Model.** `LR=0.0005` ayarına rağmen şiddetli **overfitting** (ezberleme) yaşadı (`image_89947f.png`). |
| **Model 3** | Gelişmiş CNN (Sıfırdan) | **%84.00** | **Beklenen Sonuç.** `Data Augmentation` ve `Dropout` eklenerek `model2`'deki overfitting çözüldü ve başarı %20 arttı (`image_8994fc.png`). |

---

### Sözlü Sunum Analizi

#### 1. Hangi model neden daha iyi sonuç verdi?
En yüksek doğruluğu (%92) **Model 1 (Transfer Learning)** verdi. Çünkü VGG16, "şekil" ve "kenar" algılama konusunda zaten milyonlarca fotoğraf görmüş bir beyne sahipti (ImageNet) ve bizim "çatal/kaşık" problemimizi (benzer örnekler) kolayca çözdü.

#### 2. Model 3, Model 2'den neden daha iyi oldu?
**Model 3 (%84), Model 2'den (%64) daha iyi oldu.**
* **Model 2**, `Data Augmentation` veya `Dropout` olmadan, "ezberlemeye" (overfitting) çok yatkındı. Eğitim doğruluğu %100'e çıkarken, test doğruluğu %64'te kaldı (`image_89947f.png`).
* **Model 3**, hocanın istediği hiperparametre iyileştirmelerini uygulayarak bu ezberlemeyi **başarıyla engelledi**.
* `model3`'ün grafikleri (`image_8994fc.png`), modelin artık ezberlemediğini, "genelleme" yaptığını ve öğrendiğini göstermektedir. Bu sayede `model2`'ye göre belirgin bir iyileşme sağlanmıştır.

#### 3. Model 3'te yapılan hiperparametre değişikliklerinin etkisi ne oldu?
`model3.ipynb` dosyasındaki 10 adımlık deney tablosunda görüldüğü gibi, `model2`'den `model3`'e geçerken birçok parametre denendi. En iyi sonucu (`%84`) veren **Deney 7**'de 4 ana değişiklik yapıldı:
1.  **`Data Augmentation` (Eklendi):** Ezberlemeyi engellemek için en kritik adımdı.
2.  **`Dropout (0.2)` (Eklendi):** Modelin tek bir özelliğe aşırı güvenmesini engelledi.
3.  **`Batch Size (32)` (Artırıldı):** Eğitimi daha stabil hale getirdi.
4.  **`LR (0.001)` (Değiştirildi):** Bu yeni mimari için en iyi öğrenme hızı bulundu.

Bu dört değişikliğin **kombinasyonu**, `model2`'nin %64'lük overfitting sorununu çözerek `model3`'ü %84'lük başarılı bir modele dönüştürdü.

## Kurulum ve Çalıştırma

Proje Google Colab üzerinde geliştirilmiştir.

1.  Bu repoyu klonlayın veya indirin.
2.  `dataset/` klasörünü kendi Google Drive'ınıza yükleyin.
3.  `.ipynb` dosyalarını Google Colab'de açın.
4.  Notebook içerisindeki `PROJECT_PATH` değişkenini, `dataset` klasörünüzün bulunduğu yola göre güncelleyin.
5.  Sırasıyla tüm hücreleri çalıştırın.