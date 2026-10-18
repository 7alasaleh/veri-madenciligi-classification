# Sağlık Göstergeleri ile Sigara Kullanım Durumunun Sınıflandırılması

## 1. Proje Hakkında

Bu projede, bireylerin sağlık göstergelerinden yararlanarak sigara kullanım durumunun sınıflandırılması ve benzer sağlık özelliklerine sahip bireylerin kümelenmesi amaçlanmaktadır.

Proje kapsamında iki farklı veri madenciliği görevi gerçekleştirilmiştir:

1. **Classification (Sınıflandırma):** Sağlık göstergelerini kullanarak sigara kullanım durumunu tahmin etmek.
2. **Clustering (Kümeleme):** Sağlık göstergelerine göre benzer bireyleri gruplandırmak ve kümelerdeki sigara kullanım oranlarını incelemek.


## 2. Problem Tanımı

* **İş problemi:** Sağlık göstergeleri ile sigara kullanım durumu arasındaki istatistiksel ilişkileri incelemek ve sigara kullanım durumunu tahmin etmek.
* **Analiz birimi:** Veri setindeki bir bireye ait sağlık kaydı.
* **Hedef değişken:** `smoking`

  * `0`: Sigara kullanmayan
  * `1`: Sigara kullanan
* **Zaman ufku:** Gelecekteki sigara kullanımını tahmin etmek yerine, kayıtta bulunan sigara kullanım durumunu sınıflandırmak.
* **Başarı ölçütleri:** Sınıflandırmada F1-score, Recall, Precision ve Accuracy; kümelemede Silhouette Score.
* **Baseline:** En sık görülen sınıfı tahmin eden `DummyClassifier(strategy="most_frequent")`.

## 3. Veri Seti ve Veri Hazırlama

Veri seti CSV formatındadır ve Kaggle Notebook ortamında kullanılmıştır.

| Özellik                                                  |   Değer |
| -------------------------------------------------------- | ------: |
| Başlangıçtaki satır sayısı                               | 198.240 |
| Başlangıçtaki sütun sayısı                               |      43 |
| Yinelenen satır sayısı                                   |   5.517 |
| Tekrarlanan satırlar kaldırıldıktan sonraki kayıt sayısı | 192.723 |
| Kullanılan sağlık özelliklerinin sayısı                  |      22 |
| Eksik değer sayısı                                       |       0 |

Veri hazırlama aşamasında:

* Veri seti incelenmiş ve veri tipleri kontrol edilmiştir.
* Tamamen aynı olan yinelenen satırlar kaldırılmıştır.
* Sınıflandırma için 22 sağlık göstergesi seçilmiştir.
* `smoking` hedef değişken olarak ayrılmıştır.
* Sınıflandırma verisi, sınıf oranlarını korumak amacıyla `stratify=y` kullanılarak %80 eğitim ve %20 test verisi şeklinde bölünmüştür.
* Deneylerin tekrarlanabilirliği için `random_state=42` kullanılmıştır.

Kullanılan başlıca özellikler arasında yaş, boy, kilo, bel çevresi, sistolik ve diyastolik kan basıncı, açlık kan şekeri, kolesterol, trigliserit, HDL, LDL, hemoglobin, AST, ALT ve Gtp bulunmaktadır.

## 4. Kullanılan Yöntemler

### 4.1. Sınıflandırma

İlk olarak `DummyClassifier` ile bir baseline oluşturulmuştur. Bu model, eğitim verisinde en sık görülen sınıfı tüm test kayıtları için tahmin etmektedir.

Ardından aşağıdaki parametrelerle Random Forest modeli eğitilmiştir:

* `n_estimators=120`
* `max_depth=20`
* `min_samples_leaf=2`
* `random_state=42`

Modelin performansı Accuracy, Precision, Recall ve F1-score metrikleriyle değerlendirilmiştir. Ayrıca modelin tahminlerini incelemek için confusion matrix, özellik önemlerini incelemek için feature importance grafiği oluşturulmuştur.

### 4.2. Kümeleme

İkinci görevde K-Means algoritması kullanılmıştır.

* Küme sayısı: `k=3`
* Örneklem büyüklüğü: En fazla 30.000 kayıt
* Ölçekleme: `StandardScaler`
* Değerlendirme metriği: `Silhouette Score`

Kümeleme sırasında `smoking` değişkeni modele girdi olarak verilmemiştir. Küme etiketleri oluşturulduktan sonra her kümedeki sigara kullanım oranı incelenmiştir.

## 5. Deney Sonuçları

### 5.1. Sınıflandırma Sonuçları

| Model             | Accuracy | Precision | Recall | F1-score |
| ----------------- | -------: | --------: | -----: | -------: |
| Majority Baseline |   57,50% |      0,00 |   0,00 |     0,00 |
| Random Forest     |   77,17% |    70,21% | 80,39% |   74,96% |

Random Forest modeli, test verisinde baseline modelinden daha yüksek Accuracy ve F1-score elde etmiştir. Recall değerinin %80,39 olması, test verisindeki sigara kullanan bireylerin yaklaşık %80'inin model tarafından doğru şekilde tespit edildiğini göstermektedir.

Baseline modelinin Precision, Recall ve F1-score değerleri sıfırdır; çünkü model yalnızca çoğunluk sınıfını tahmin etmekte ve sigara kullanan bireyleri tespit edememektedir.

### 5.2. Kümeleme Sonuçları

Silhouette Score değeri **0,1561** olarak hesaplanmıştır.

| Küme | Kayıt sayısı | Ortalama yaş | Ortalama hemoglobin | Ortalama trigliserit | Sigara kullanım oranı |
| ---- | -----------: | -----------: | ------------------: | -------------------: | --------------------: |
| 0    |       16.994 |       40,779 |              15,591 |              153,350 |               61,704% |
| 1    |       11.979 |       47,953 |              13,630 |               91,620 |               16,746% |
| 2    |        1.027 |       59,586 |              14,342 |              117,588 |               33,106% |

Küme 0'da sigara kullanım oranı en yüksek, Küme 1'de ise en düşüktür. Küme 2, diğer kümelere kıyasla daha az kayıt içermektedir ve ortalama yaşı daha yüksektir.

Silhouette Score değerinin düşük olması, kümelerin birbirinden güçlü biçimde ayrılmadığını göstermektedir. Bu nedenle kümeler kesin veya doğal sağlık grupları olarak yorumlanmamalıdır.

## 6. Grafikler

Proje kapsamında aşağıdaki grafikler oluşturulmuştur:

* `figures/confusion_matrix.png`: Random Forest sınıflandırma sonuçları.
* `figures/feature_importance.png`: En önemli 10 özelliğin karşılaştırılması.
* `figures/cluster_smoking_rate.png`: Kümelere göre sigara kullanım oranları.

## 7. Proje Dosya Yapısı

```text
project/
├── README.md
├── solution.ipynb
├── results.csv
├── reflection.md
└── figures/
    ├── confusion_matrix.png
    ├── feature_importance.png
    └── cluster_smoking_rate.png
```


## 8. Çalıştırma

Proje Kaggle Notebook ortamında geliştirilmiştir.

Gerekli başlıca Python kütüphaneleri:

* pandas
* numpy
* matplotlib
* scikit-learn

Projeyi başka bir ortamda çalıştırmak için veri dosyasının yolu güncellenmeli ve gerekli kütüphaneler kurulmalıdır.

## 9. Sınırlılıklar

* Sonuçlar tek bir eğitim-test bölünmesine dayanmaktadır.
* Modelin bağımsız bir veri setindeki performansı değerlendirilmemiştir.
* Silhouette Score, küme ayrımının sınırlı olduğunu göstermektedir.
* İstatistiksel ilişkiler nedensellik anlamına gelmez.
* Modelin sonuçları tıbbi tanı veya klinik karar desteği için doğrulanmış değildir.

## 10. Sonuç

Bu projede aynı veri seti üzerinde iki farklı veri madenciliği görevi uygulanmıştır. Random Forest, sınıflandırma deneyinde baseline modelinden daha yüksek performans göstermiştir. K-Means ise sigara kullanım oranları farklı olan kümeler oluşturmuştur; ancak düşük Silhouette Score nedeniyle küme sonuçları dikkatli yorumlanmalıdır.
