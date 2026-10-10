# Fazor 1.0.0 — kapsamlı doğrulama raporu

10 Eylül 2026 · Windows 11 x64

## Sonuç

Toplam **111.810 / 111.810 kontrol geçti; başarısız kontrol yok.** Bu sayı farklı test katmanlarında gerçekten yürütülen örneklerin toplamıdır. Aynı temel özellik bazı katmanlarda farklı amaçlarla yeniden sınanır.

| Test katmanı                               |     Kontrol |             Sonuç |
| ------------------------------------------ | ----------: | ----------------: |
| Temel birim ve regresyon testleri          |          24 |          24 geçti |
| Birim çember özel açı taraması             |       1.746 |       1.746 geçti |
| Fonksiyon tanım kümesi ve çıktı aralıkları |      96.050 |      96.050 geçti |
| Hedefli zor/sınır örnekleri                |          94 |          94 geçti |
| Bağımsız 120 basamaklı mpmath referansları |       3.220 |       3.220 geçti |
| Rastgele ve stres testleri                 |       2.337 |       2.337 geçti |
| Sayısal karşı örnek/kimlik testleri        |       3.086 |       3.086 geçti |
| Matematiksel özellik testleri              |       1.217 |       1.217 geçti |
| Fazör ve logaritma çapraz kontrolleri      |       3.905 |       3.905 geçti |
| Gerçek Electron arayüzü                    |         131 |         131 geçti |
| **Toplam**                                 | **111.810** | **111.810 geçti** |

JSON sonuçları `test-results/` klasöründedir. Rastgele testler sabit tohumlarla tekrar üretilebilir. Bağımsız referans kümesi `tests/generate-reference.py` tarafından mpmath 1.4.1 ve 120 ondalık basamak hassasiyetiyle üretilmiştir; Fazor veya math.js sonuçları beklenen değer olarak kullanılmamıştır.

## Kontrol edilenler

- İşlem önceliği, üs/kök, logaritma, faktöriyel, kombinasyon, permütasyon ve temel aritmetik.
- DEG/RAD/GRAD altında doğrudan/ters trigonometrik işlevler, `atan2`, kutup noktaları ve trigonometrik kimlikler.
- `asin` ve `acos` için `[-1,1]` girdi sınırı; `asin`, `acos` ve `atan` ana değer çıktı aralıkları; üç açı biriminde 96.048 sınır, kimlik ve aralık kontrolü.
- Karekök, doğal/genel logaritma, `asec`, `acsc`, `acosh`, `atanh`, `asech` ve `acsch` gerçek sayı tanım kümeleri; gerçek ekranda örtük karmaşık sonucun reddi.
- Ondalık, bilimsel, mühendislik ve yaklaşık kesir biçimleri; skaler, karmaşık sayı, vektör ve matris sonuçları; çok küçük ve çok büyük sayılar.
- Karmaşık aritmetik, eşlenik, mutlak değer, kutupsal/dikdörtgensel gösterim ve faz açısı.
- Polinom integralleri, 1–2.000 frekanslı salınımlı integraller, 100 rastgele dar Gauss tepesi ve temel integral kimlikleri.
- Noktada türev, yüksek frekans, rastgele yüksek dereceli polinomlar, sabit fonksiyon ve türevsiz/tanımsız noktaların reddi.
- 10⁻²⁰⁰–10²⁰⁰ ölçekli kökler, süreksizlik, 2.–4. derece polinomlar, tekrarlı/karmaşık kökler ve eşitsizlik aralıkları.
- 2×2, 3×3 ve 4×4 matrislerde toplama, çıkarma, çarpma, ters, determinant, transpoz, kare ve iz; 300 rastgele ters-matris özelliği; tekil sistemlerin reddi.
- Vektör toplama/çıkarma, iç/dış çarpım, norm, birim vektör ve üç açı biriminde vektör açısı; sıfır vektörünün reddi.
- Özet istatistik, frekanslar, büyük ofsetler ve iptal hatası; doğrusal, ikinci derece, logaritmik, üstel, kuvvet ve ters regresyon.
- Normal PDF/CDF/ters CDF; 10⁻¹⁰⁰ düzeyine kadar kuyruk örnekleri; binom ve Poisson PMF/CDF, toplam olasılık ve monotonluk.
- f/g sayı tablosu, temsil edilemeyen adımlar, 100 rastgele elektronik tablo bağımlılık zinciri ve döngüsel başvurular.
- BIN/OCT/DEC/HEX, AND/OR/XOR/NOT/toplama/çıkarma; 300 rastgele tabanlar arası çapraz kontrol.
- Oranın dört bilinmeyen konumu, DMS çift yönlü dönüşüm, bilimsel sabitler ve yaygın mühendislik birimleri.
- RK4 ileri/geri çözüm, analitik çözümler ve yetersiz adımın sessizce yanlış sayı üretmek yerine reddedilmesi.
- A–F ve x/y/z belleği, Ans hassasiyeti, f/g kalıcılığı, geçmiş, AC/Escape, imleç, kopyalama, yardım, 15 ekran ve 390 piksel mobil düzen.
- Değiştirilebilir logaritma tabanı; 0–69 faktöriyel sınırı ve `78!` için Math Error; A–D dört matrisin tekli/ikili işlemleri; açık/koyu tema ve Türkçe/İngilizce kalıcılığı; üretici ve telif ekranı.

## Test sırasında bulunan ve düzeltilen başlıca hatalar

Testler sadece rapor üretmedi; uygulama çekirdeği sertleştirildi. Düzeltilen sorunlar arasında açı birimine uymayan `atan2`, birimli trigonometrik girdinin iki kez dönüştürülmesi, trigonometrik kutuplarda dev ve yanıltıcı sayı, çok küçük kesirlerin sıfıra dönüşmesi, matris/karmaşık kesir biçimi, mühendislik gösteriminde üs taşıması, DMS işareti ve 60 saniye taşıması bulunuyor.

0.2.1 sürümünde önceki oturumdan kalan GRAD ayarının `sin(30)` ifadesini 30 grad, yani 27 derece olarak yorumlayıp yaklaşık `0,4539905` göstermesi ele alındı. Uygulama artık her açılışta DEG ile başlıyor; seçili açı birimi giriş ve sonuç alanlarında açık adıyla gösteriliyor. Eski bir profile `angle: GRAD` yazılıp yeniden başlatılan gerçek Electron regresyon testinde ondalık `0.5` ve kesir `1/2` doğrulandı.

0.2.2 sürümünde kesir görünümünün `sin(60°)` için ürettiği büyük yaklaşık rasyonel sayı kaldırıldı. Standart 15° birim çember değerleri sin, cos, tan, sec, csc ve cot için köklü biçimde gösteriliyor; RAD modundaki özel açı sonuçları `π` kesirleri olarak yazılıyor. `-720°…720°` aralığında, 15° adımla ve DEG/RAD/GRAD modlarında 1.746 çapraz kontrol eklendi. Bu tarama sırasında bulunan çok turlu açı indirgeme ve kutup algılama hataları da düzeltildi. `sin(17°)` gibi basit tam biçimi bulunmayan sonuçlar artık yanıltıcı büyük kesir yerine `≈` işaretli ondalık değer gösteriyor.

0.3.0 sürümünde `log(x, taban)` ve log tuşu için taban seçimi eklendi. Resmî fx-991CW aralığına göre faktöriyel 0–69 tam sayıyla sınırlandı ve bütün hesaplama hataları arayüzde Math Error başlığı altında gösterildi. Matris çalışma alanı A–D olmak üzere dört matrise ve her matrisin determinant/ters/transpoz/kare/iz işlemlerine genişletildi; matris çiftleri arasında toplama, çıkarma ve iki yönlü çarpma eklendi. Tema ve dil tercihleri kalıcı hale getirildi; karanlık Türkçe/İngilizce görünümler gerçek Electron penceresinde incelendi.

0.4.0 sürümünde doğrudan `büyüklük∠açı` fazör girişi, DEG/RAD/GRAD fazör sonuç biçimi ve 3.905 yeni fazör/logaritma çapraz kontrolü eklendi. Her logaritma artık tabanını kendi `log(sayı, taban)` çağrısında taşır; on tabanlı logaritmanın önizlemede `ln` görünmesi giderildi. Analiz alanına ifade tuş takımı, kurulum ve ilk açılış lisans kabulü, NSIS kaldırıcı, uygulama simgesi ve ASAR bütünlük doğrulaması eklendi.

Aynı sürümde gerçek sayı ve karmaşık sayı değerlendirmesi iki ayrı motora ayrıldı. Math.js'in `acos(82)` gibi gerçek sayı tanım kümesi dışındaki bir girdiyi sessizce karmaşık sonuca genişletmesi bilimsel ekranda engellendi. `asin`, `acos`, ters sekant/kosekant, karekök, logaritma ve ters hiperbolik fonksiyonların sınırları açıkça doğrulanıyor; karmaşık sonuçlar yalnızca Karmaşık Hesaplama ekranında kabul ediliyor. Üç açı biriminde giriş ve ana değer çıktı aralıklarını tarayan 96.048 kontrol ile gerçek Electron arayüzünde Math Error davranışı eklendi.

İlk integral yöntemi bazı salınımlı fonksiyonlarda 0,5 yerine 1 bulabiliyor ve dar tepeleri kaçırabiliyordu. Yerine hata tahminli, bölünmüş uyarlanabilir 15/7 Gauss–Kronrod yöntemi kondu. Türev için adım yakınsaması ve sağ/sol tutarlılığı denetleniyor. Çözücü artık fonksiyon ölçeğinden bağımsız sonlandırma ve artık kontrolü yapıyor.

Büyük ofsetli regresyon, istatistiksel toplama/minimum/maksimum, aşırı normal kuyrukları, büyük Poisson değerleri, çok küçük binom CDF değerleri, tekil doğrusal sistemler ve kararsız RK4 adımları düzeltildi. Elektronvolt, güncel tam SI değeri `1.602176634×10⁻¹⁹ J` ile güncellendi. Hata sonrası eski sonucu biçim değiştirme veya belleğe kaydetme, elektronik tablodaki eşzamanlı sonuç yarışı ve gereksiz yinelenen eşitsizlik noktaları da giderildi.

## Güvence sınırı

Bu çalışma güçlü bir doğrulama sağlar, fakat sonlu testler bütün gerçek sayı girdileri için matematiksel ispat değildir. IEEE-754 çift duyarlık sınırları geçerlidir. Uygulama güvenilir yakınsama veya tanım doğrulayamıyorsa tahmini bir sayı göstermek yerine hata verir.

Genel amaçlı sayısal integral, bilinmeyen ve örnekleme noktalarının arasına tamamen saklanmış keyfi dar bir özelliği teorik olarak garanti edemez. Sonsuz/uçta tanımsız integraller desteklenmez. Kötü koşullu doğrusal sistemler reddedilebilir. RK4 sert diferansiyel denklemler için uygun değildir. Cihazla bütün olası tuş dizileri üzerinde birebir Casio uygunluk sertifikasyonu yapılmamıştır.

## Tekrar çalıştırma

```sh
npm ci
npm test
npm run build
node tests/ui-audit.mjs
```

`npm test` hesaplama katmanlarının tamamını çalıştırır. Son komut gerçek Electron arayüzünü sınar. Windows paketi ayrıca paketlenmiş çalıştırılabilir dosya üzerinden aynı arayüz denetiminden geçirilmiştir.
