Problemin Tanımı

Orman yangınları, dünya genelinde ekolojik dengeyi bozan, biyoçeşitliliği tehdit eden ve ciddi ekonomik kayıplara yol açan en yıkıcı doğal afetlerden biridir. Yangınların erken tespiti, müdahale süresini kısaltarak felaketin büyümesini önlemede hayati bir rol oynamaktadır. Geleneksel yöntemler olan gözetleme kuleleri ve insan devriyeleri, geniş arazilerde yetersiz kalabilmekte ve müdahalelerde gecikmelere yol açabilmektedir. Bu noktada, teknolojiden faydalanarak daha hızlı, daha verimli ve otomatik bir tespit sistemi kurma ihtiyacı doğmaktadır. Görüntü işleme ve derin öğrenme teknolojileri, bu alanda devrim niteliğinde çözümler sunma potansiyeline sahiptir.



Projenin Amacı

Bu projenin temel amacı, Evrişimli Sinir Ağları (Convolutional Neural Networks - CNN) mimarisini kullanarak, bir görüntüde orman yangını olup olmadığını yüksek doğrulukla tespit edebilen bir derin öğrenme modeli geliştirmektir. Proje kapsamında, yangın içeren ve içermeyen binlerce görüntüden oluşan bir veri seti kullanılarak, modelin görsel desenleri ve yangına özgü ayırt edici özellikleri (alevlerin dokusu, dumanın varlığı, renk dağılımı vb.) öğrenmesi hedeflenmektedir. Geliştirilecek olan bu modelin, potansiyel olarak insansız hava araçları (İHA), güvenlik kameraları veya uydu görüntüleri üzerinden çalışarak, ormanlık alanların otonom bir şekilde izlenmesine ve yangın vakalarının anında bildirilmesine olanak tanıyan sistemlerin temelini oluşturması amaçlanmaktadır. Bu sayede, insan kaynaklı hataların en aza indirilmesi ve müdahale ekiplerinin en kısa sürede doğru lokasyona yönlendirilmesi sağlanacaktır.


Veri Setinin Tanıtımı

Bu projede, modelin eğitilmesi ve değerlendirilmesi için Kaggle üzerinde halka açık olarak yayınlanan "The Wildfire Dataset" kullanılmıştır. Bu veri seti, orman yangını tespiti gibi ikili sınıflandırma (binary classification) problemleri için özel olarak derlenmiştir. Veri seti, iki ana kategoride toplanmış çok sayıda görüntü içermektedir:

fire: Aktif olarak alevlerin veya yoğun dumanın görüldüğü, orman yangını içeren görüntüler.

nofire: Yangın belirtisi olmayan, sağlıklı orman, doğa manzaraları ve yeşil alanları içeren görüntüler.

Bu zengin ve çeşitli görseller, modelin yangın durumunu farklı ışık koşulları, mevsimler ve açılardan tanıma yeteneğini geliştirmesi için kritik bir temel oluşturmaktadır.



Veri Setinin Yapısı ve Sınıf Dağılımı

Veri seti, derin öğrenme projelerinde standart bir pratik olan üç ana alt klasöre ayrılmıştır. Bu yapı, modelin eğitilmesi, eğitim sırasında performansının ayarlanması (tuning) ve son olarak nihai performansının adil bir şekilde ölçülmesi için tasarlanmıştır.

train (Eğitim Seti): Modelin, yangın ve yangın olmayan görseller arasındaki temel desenleri ve özellikleri öğrendiği en büyük veri kümesidir.

val (Doğrulama Seti - Validation Set): Eğitim sırasında, her bir epoch (döngü) sonunda modelin performansını kontrol etmek için kullanılan veri setidir. Bu set, modelin ezber yapıp yapmadığını (overfitting) anlamamıza ve hiperparametre ayarları yapmamıza yardımcı olur.

test (Test Seti): Eğitim ve doğrulama süreçlerinde modelin hiç görmediği, tamamen bağımsız bir veri kümesidir. Modelin gerçek dünya performansını ölçmek için kullanılan nihai sınav niteliğindedir.

Her bir klasörde bulunan görüntü sayıları aşağıdaki gibidir:

Eğitim Seti: Toplam 1887 adet geçerli görüntü.

Doğrulama Seti: Toplam 403 adet geçerli görüntü.

Test Seti: Toplam 410 adet geçerli görüntü.

Bu yapılandırılmış ayrım, modelin hem genelleme yeteneğini sağlıklı bir şekilde geliştirmesini hem de performansının tarafsız bir şekilde değerlendirilmesini sağlamaktadır.


Kullanılan Yöntemler

Veri Ön İşleme ve Çoğaltma (Data Augmentation):
Tüm görüntüler, modele girdi olarak verilebilmesi için standart 224x224 piksel boyutuna getirilmiş ve piksel değerleri [0, 1] aralığına normalize edilmiştir.
Modelin genelleme yeteneğini artırmak ve aşırı öğrenmeyi (overfitting) önlemek amacıyla, Albumentations kütüphanesi ile zengin bir veri çoğaltma stratejisi uygulanmıştır. Bu strateji, anlık olarak (on-the-fly) döndürme, yatay çevirme, parlaklık/kontrast ayarı ve CoarseDropout (görüntüden rastgele bölgeler silme) gibi çeşitli teknikleri içermektedir.
Model Mimarisi (Evrişimli Sinir Ağı - CNN):
Projenin temelini, Keras kütüphanesinin Functional API'si ile oluşturulmuş derin bir Evrişimli Sinir Ağı oluşturmaktadır.
Model, görüntüdeki hiyerarşik özellikleri (kenarlar, dokular, şekiller) çıkarmak için artan filtre sayılarına sahip çoklu Conv2D ve MaxPooling2D bloklarından oluşur.
Eğitimi stabilize etmek ve hızlandırmak için her evrişim bloğunda BatchNormalization katmanı kullanılmıştır.
Aşırı öğrenmeyi engellemek amacıyla, sınıflandırma katmanlarında Dropout tekniği uygulanmıştır.
Hiperparametre Optimizasyonu:
Modelin en iyi performansı gösterecek yapılandırmasını bulmak için Keras Tuner kütüphanesi ile otomatik hiperparametre optimizasyonu yapılmıştır.
RandomSearch stratejisi kullanılarak, evrişim katmanı sayısı, filtre sayısı, Dense katman nöron sayısı ve öğrenme oranı gibi kritik hiperparametreler için en uygun değerler sistematik olarak aranmıştır.
Model Yorumlanabilirliği (XAI):
Modelin bir "kara kutu" olmasını önlemek ve karar verme mekanizmasını anlamak için Grad-CAM (Gradient-weighted Class Activation Mapping) tekniği uygulanmıştır. Bu sayede, modelin bir tahminde bulunurken görüntünün hangi bölgelerine odaklandığını gösteren ısı haritaları (heatmaps) oluşturulmuştur.


Elde Edilen Sonuçlar

Proje kapsamında geliştirilen üç farklı modelin (Başlangıç Modeli, Hiperparametre Optimizasyonlu Model ve Transfer Öğrenme Modeli) performansları, daha önce hiç görmedikleri test seti üzerinde değerlendirilmiş ve aşağıdaki sonuçlar elde edilmiştir.
Performans Karşılaştırma Tablosu
Model Yaklaşımı	Test Seti Doğruluğu (Accuracy)	Test Seti Kaybı (Loss)
1. Başlangıç CNN Modeli	82.03%	0.3823
2. Hiperparametre Optimizasyonlu Model	83.59%	0.3972
3. Transfer Öğrenme (EfficientNetB0)	58.59%	---
Sonuçların Analizi ve Yorumlanması
Başlangıç Modelinin Başarısı: Sıfırdan tasarlanan ve eğitilen başlangıç CNN modelimiz, test seti üzerinde %82.03'lük kayda değer bir doğruluk oranına ulaşarak projenin temel hedefini başarıyla gerçekleştirmiştir.
Hiperparametre Optimizasyonunun Etkisi: Keras Tuner kullanılarak yapılan otomatik hiperparametre optimizasyonu, modelin performansını daha da ileriye taşımıştır. Optimize edilmiş model (best_model), %83.59'luk bir doğruluk oranı ile başlangıç modelini geride bırakmıştır. Bu, en iyi katman yapısı, öğrenme oranı ve Dropout oranı gibi ayarların sistematik olarak bulunmasının, modelin genelleme yeteneğini artırdığını göstermektedir. Bu model, projenin en başarılı modeli olmuştur.
Transfer Öğrenme Sonucunun Değerlendirilmesi: Beklenenin aksine, EfficientNetB0 taban mimarisi kullanılarak geliştirilen transfer öğrenme modeli, %58.59'luk bir doğruluk oranı ile en düşük performansı sergilemiştir. Bu durumun olası nedenleri şunlar olabilir:
Veri Seti Uyuşmazlığı: ImageNet üzerinde eğitilmiş olan modelin öğrendiği genel özellikler (arabalar, hayvanlar, binalar), orman yangınlarının spesifik doku ve desenlerine tam olarak transfer edilememiş olabilir.
Yetersiz İnce Ayar (Fine-Tuning): Modelin sadece en üst katmanlarını eğitmek, bu spesifik görev için yeterli olmamış olabilir. Taban modelin daha fazla katmanını "çözerek" (unfreezing) ve daha düşük bir öğrenme oranıyla daha uzun süre eğiterek (ince ayar) performans artırılabilirdi.
Veri Seti Büyüklüğü: Görece küçük olan bu veri seti için, sıfırdan eğitilen ve probleme daha özel tasarlanmış bir modelin, devasa bir modelin ince ayarından daha iyi sonuç vermesi de olası bir senaryodur.
Genel Değerlendirme: Projenin nihai çıktısı, %83.59 doğruluk oranına sahip, hiperparametre optimizasyonu ile geliştirilmiş özel CNN modelidir. Grad-CAM ile yapılan analizler, bu modelin tahminlerini büyük ölçüde görüntülerdeki alev ve duman gibi mantıksal bölgelere odaklanarak verdiğini doğrulamıştır. Bu sonuçlar, projenin hedeflerine başarıyla ulaşıldığını ve derin öğrenme ile orman yangını tespiti için etkili ve yorumlanabilir bir çözüm geliştirildiğini göstermektedir.

Kaggle:
https://www.kaggle.com/code/unalpilavci/akbank-deep-learning-bootcamp-project
