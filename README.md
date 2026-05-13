# YZTA 5.0 Datathon — Bilişsel Performans Tahmini

## Proje Özeti
Bu proje, YZTA 5.0 Datathon yarışması kapsamında geliştirilmiştir.  
**Hedef:** Uyku ve yaşam tarzı verilerinden `bilissel_performans_skoru` (0-10 sürekli) tahmin etmek.  
**Metrik:** RMSE (düşük = daha iyi)  
**Veri seti:** 56.000 eğitim / 24.000 test örneği

---

## Sonuçlar

| Submission | Açıklama | Public RMSE |
|---|---|---|
| v1 | Baseline (LabelEncoding + XGBoost) | 1.20647 |
| v2 | Feature engineering + CatBoost tuned | 1.20555 |

---

## Kullanılan Yaklaşım

### Model
- **CatBoost** — native kategorik desteği ile (`cat_features` parametresi)
- GPU ile eğitim (`task_type='GPU'`)
- Optuna ile Bayesian hiperparametre optimizasyonu (40 trial)
- 5-Fold StratifiedKFold (hedef değişken `pd.qcut` ile bin'lendi)
- Early stopping (100 round)

### Özellik Mühendisliği (12 yeni özellik)
| Özellik | Açıklama |
|---|---|
| `sleep_efficiency` | uyku_suresi / yatak_suresi |
| `social_jetlag` | hafta içi-sonu uyku farkı |
| `extreme_exhaustion` | çok az uyku + yüksek egzersiz kombinasyonu |
| `uyku_kalitesi_skoru` | uyku kalitesi × verimlilik etkileşimi |
| `uyarici_yuk` | kafein + ekran süresi toplamı |
| `aktivite_calisma_orani` | fiziksel aktivite / çalışma saati |
| `stres_uyku_etkilesimi` | stres × uyku süresi |
| `kafein_ekran_etkilesimi` | kafein × ekran süresi |
| `ruh_stres_etkilesimi` | ruh sağlığı × stres skoru |
| `uyku_bozucu_faktor` | bileşik uyku bozucu endeks |
| `yas_grubu` | yaş kategorisi (genç/orta/olgun) |
| `vki_kategori` | BMI kategorisi |

### Ön İşleme
- Kategorik eksik değerler: `'Bilinmiyor'` ile doldurma
- Sayısal eksik değerler: fold içi medyan imputation (data leakage önleme)
- Aykırı değer kırpma: fold içi IQR (data leakage önleme)
- CatBoost native categorical: label encoding yapılmadı

---

## Gereksinimler
catboost
optuna
scikit-learn
pandas
numpy
matplotlib
seaborn



## Çalıştırma
```bash
pip install catboost optuna scikit-learn pandas numpy matplotlib seaborn
jupyter notebook bilissel_performans_tahmini.ipynb
Notebook'u baştan sona sırayla çalıştırın.

Optuna adımı ~30-70 dakika sürebilir (GPU'suz sistemlerde daha uzun).
