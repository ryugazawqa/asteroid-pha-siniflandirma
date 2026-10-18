# 🌍 Tehlikeli Asteroit (PHA) Sınıflandırması

NASA'nın Dünya'ya Yakın Nesneler (Near-Earth Object, NEO) verisiyle, bir asteroidin **Potansiyel Olarak Tehlikeli Asteroit (PHA)** olup olmadığını makine öğrenmesiyle tahmin eden proje.

## Veri seti

Kaggle'daki NASA NEO veri seti (`*near_earth*.csv`):
- 41.281 asteroit, 29 sütun
- Tehlikeli (PHA): 2.539 (%6), güvenli: 38.742 → **dengesiz veri**

## Yöntem

1. **Veri sızıntısını önleme:** PHA tanımında doğrudan kullanılan sütunlar (`H` mutlak parlaklık, `moid` minimum yörünge kesişim mesafesi, çap vb.) çıkarıldı. Böylece model yalnızca yörünge parametrelerinden öğrenmek zorunda kaldı.
2. **Özellik mühendisliği:** Yörünge parametrelerinden afelyon/perihelyon mesafesi, özgül yörünge enerjisi ve hız yaklaşımı (Kepler'in 3. yasası) türetildi. Toplam 12 özellik kullanıldı.
3. **Sınıf dengesizliği:** SMOTE yalnızca eğitim setine uygulandı. Test seti gerçekçi dağılımda bırakıldı.
4. **Modeller:** Random Forest ve XGBoost.
5. **Doğrulama:** Stratified 5-Fold çapraz doğrulama ve eğitim/test farkı ile overfitting kontrolü.

## Sonuçlar (test seti, 8.257 asteroit)

| Model | Accuracy | F1 (PHA) | Recall (PHA) | ROC-AUC |
|---|---|---|---|---|
| Random Forest | %78,16 | %20,75 | %46 | 0,748 |
| XGBoost | %69,26 | %21,33 | %68 | 0,765 |

**Yorum:**
- PHA tanımını belirleyen değişkenler bilinçli olarak çıkarıldığı için problem zorlaşıyor ve F1 skorları düşük kalıyor.
- XGBoost tehlikeli asteroitlerin %68'ini yakalıyor. Bu, tehlikeyi kaçırmamanın daha önemli olduğu bu problem için daha uygun.
- Eğitim ve test F1 skorları arasındaki fark büyük (overfitting). SMOTE'suz çapraz doğrulamada F1 çok düşük çıkıyor.
- **Geliştirme fikirleri:** Eşik (threshold) ayarı, sınıf ağırlıklı kayıp fonksiyonu, daha fazla yörünge özelliği.

## Görseller

Notebook çalıştırıldığında şu görselleri üretir:
- `01_eda.png`: sınıf ve özellik dağılımları
- `02_model_performance.png`: karışıklık matrisi, ROC ve PR eğrileri
- `03_feature_importance.png`: özellik önemi
- `04_correlation.png`: korelasyon matrisi

## Çalıştırma

Notebook Kaggle ortamında hazırlandı. NASA NEO veri setini girdi olarak ekleyip `asteroid_pha_siniflandirma.ipynb` dosyasını çalıştırın.

Kullanılan kütüphaneler: `pandas`, `numpy`, `scikit-learn`, `xgboost`, `imbalanced-learn`, `matplotlib`, `seaborn`
