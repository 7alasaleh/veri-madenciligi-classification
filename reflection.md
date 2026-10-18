# Proje Değerlendirmesi ve Yöntemsel Sınırlılıklar

## 1. Yinelenen Kayıtlar (Duplicate Records)

Veri setinde başlangıçta 198.240 satır bulunuyordu. Tamamen aynı olan 5.517 satır tespit edildi ve bu satırlar kaldırıldı. Veri hazırlama sonrasında 192.723 kayıt kaldı.

**Risk:** Yinelenen kayıtlar, bazı gözlemlerin model eğitiminde gereğinden fazla ağırlık kazanmasına neden olabilir. Aynı kayıtların eğitim ve test verisinde bulunması, model performansının olduğundan yüksek görünmesine de yol açabilir.

**Azaltma yöntemi:** Veri bölünmeden önce tamamen aynı satırlar kaldırılmıştır.

**Kalan sınırlılık:** Aynı değerlere sahip iki kayıt mutlaka aynı kişiyi temsil etmez. Bu nedenle yinelenen satırların tamamının hatalı kayıt olduğu kesin olarak söylenemez.

## 2. Korelasyon ve Nedensellik (Correlation vs. Causation)

Sigara kullanımı ile bazı sağlık göstergeleri arasında korelasyonlar gözlemlenmiştir. Örneğin hemoglobin ile korelasyon 0,441, boy ile 0,437 ve trigliserit ile 0,315 olarak hesaplanmıştır.

**Risk:** Bu ilişkilerin, sigara kullanımının söz konusu sağlık göstergelerindeki değişimlere doğrudan neden olduğunu kanıtladığı düşünülebilir.

**Azaltma yöntemi:** Bulgular yalnızca istatistiksel ilişki ve tahmin performansı olarak yorumlanmıştır. Nedensel sonuçlar çıkarılmamıştır.

**Kalan sınırlılık:** Mevcut analiz, diğer olası etkileri tamamen kontrol etmemekte ve nedensel bir ilişkiyi kanıtlamamaktadır.

## 3. Kümeleme Performansının Sınırlı Olması

K-Means algoritması üç küme oluşturmuştur. Hesaplanan Silhouette Score değeri 0,1561'dir.

**Risk:** Kümelerin birbirinden açık biçimde ayrıldığı veya her kümenin belirgin bir sağlık profili temsil ettiği iddia edilebilir.

**Azaltma yöntemi:** Silhouette Score raporlanmış ve kümelerin ayrımının zayıf olduğu belirtilmiştir. Küme sonuçları kesin sağlık kategorileri olarak sunulmamıştır.

**Kalan sınırlılık:** Küme sayısı `k=3` olarak seçilmiştir. Farklı küme sayıları ve alternatif kümeleme yöntemleri henüz karşılaştırılmamıştır. Bu nedenle üç kümenin en uygun çözüm olduğu gösterilmemiştir.


## Genel Değerlendirme

Random Forest modeli, test verisinde %77,17 Accuracy ve 0,7496 F1-score elde ederek majority baseline modelinden daha yüksek performans göstermiştir. Bununla birlikte, bu sonuçlar modelin tüm veri setlerinde aynı performansı göstereceğini garanti etmez.

K-Means sonuçlarında kümeler arasındaki sigara kullanım oranları farklılık göstermiştir; ancak 0,1561 Silhouette Score, küme ayrımının sınırlı olduğunu ortaya koymaktadır.

Bu nedenle sonuçlar keşifsel ve eğitim amaçlı bulgular olarak değerlendirilmelidir. Daha güçlü sonuçlar elde etmek için çapraz doğrulama, alternatif algoritmaların karşılaştırılması, farklı küme sayılarının incelenmesi ve bağımsız veriyle doğrulama yapılması önerilmektedir.