# 🎯 Customer Status Prediction

> **Müşteri durumunu tahmin eden çok sınıflı bir makine öğrenmesi projesi — R ile geliştirildi.**

---

## 📌 Proje Özeti

Bu proje, B2B müşterilerin durumunu tahmin etmek için makine öğrenmesi modelleri geliştirmeyi amaçlamaktadır. Müşteri demografisi, gelir geçmişi, destek aktivitesi, memnuniyet anketleri ve daha fazlasını kapsayan **9 farklı veri kaynağı** birleştirilerek eğitim ve test aşamaları eksiksiz biçimde yürütülmektedir.

### Hedef Sınıflar

| Sınıf | Açıklama |
|-------|----------|
| 🔴 **Churn** | Müşteriyi kaybetme riski yüksek |
| 🟡 **Onboarding** | Yeni veya alışma sürecindeki müşteri |
| 🟢 **Retained** | Sadık, aktif müşteri |

---

## 📊 Model Performansı

| Model | Accuracy | Kappa |
|-------|----------|-------|
| 🏆 **Random Forest** | **95.2%** | **0.919** |
| GBM (Gradient Boosting) | 94.4% | 0.907 |
| Logistic Regression | 91.0% | 0.850 |

> **En iyi model: Random Forest** — %95.2 doğruluk oranıyla production'a alınmıştır.

---

## 🗂️ Veri Kaynakları

Proje 9 farklı CSV dosyasını birleştirmektedir:

| Dosya | İçerik |
|-------|--------|
| `customer_status_level` | Hedef değişken (Status) ve müşteri seviyesi |
| `customer_demographics` | Müşteri yaşı (ay cinsinden) |
| `customer_monthly_recurring_revenue` | Aylık yinelenen gelir (MRR) |
| `customer_region_and_industry` | Bölge, dikey ve alt dikey bilgisi |
| `customer_revenue_history` | Toplam gelir geçmişi |
| `customer_satisfaction_scores` | NPS, kullanım sıklığı, kalite puanları |
| `newsletter_engagement` | Bülten etkileşim sayısı |
| `product_bug_reports` | Ürün hata rapor sayısı |
| `support_ticket_activity` | Destek talebi sayısı ve çözüm süresi |

---

## 🔬 Metodoloji

### 1️⃣ Veri Ön İşleme
- **ID hizalaması** — Tüm tablolarda `Customer.ID` standardizasyonu
- **Eksik değer yönetimi** — Sayısal sütunlar için medyan imputation; anket yokluğu için `has_survey` bayrağı
- **Duplikasyon kontrolü** — Her veri kümesi için benzersiz müşteri sayısı doğrulaması

### 2️⃣ Keşifsel Veri Analizi (EDA)
- Hedef değişken dağılımı incelemesi (%47 Churn, %41 Retained, %12 Onboarding)
- Sayısal değişkenlerin korelasyon analizi (Feature Engineering öncesi ve sonrası)
- Kategorik değişkenlerin dağılım grafikleri

### 3️⃣ Özellik Mühendisliği (Feature Engineering)
6 yeni özellik türetilmiştir:

| Yeni Özellik | Açıklama |
|---|---|
| `Satisfaction_Composite` | Memnuniyet skorlarının ağırlıklı ortalaması |
| `Support_Burden` | Destek talebi yoğunluk endeksi |
| `Revenue_per_Month` | Aylık gelir verimliliği |
| `Bug_Frequency` | Hata raporu sıklığı |
| `Engagement_Ratio` | Bülten etkileşim oranı |
| `MRR_Ratio` | MRR'ın toplam gelire oranı |

### 4️⃣ Model Eğitimi
- **5-katlı çapraz doğrulama** (5-fold Cross Validation)
- `multiClassSummary` ile çok sınıflı metrik takibi
- One-hot encoding (`dummyVars`) ile kategorik değişken dönüşümü
- Çoklu bağlantısallık kontrolü (|r| > 0.8 eşiği)

### 5️⃣ Model Değerlendirme
- Confusion matrix (her model için ayrı ısı haritası)
- One-vs-Rest ROC eğrileri ve AUC skorları
- Accuracy & Kappa karşılaştırma grafikleri

---

## 🗃️ Proje Yapısı

```
Customer_Status_Prediction/
│
├── Train Datasets/          # Eğitim CSV dosyaları
├── Test Datasets/           # Test CSV dosyaları
│
├── train.R                  # Eğitim pipeline'ı
├── predict.R                # Test / tahmin pipeline'ı
│
├── train_models_final.RData # Kayıtlı modeller ve ön işleme nesneleri
│
├── confusion_matrix_random_forest.png
├── confusion_matrix_logistic_regression.png
├── confusion_matrix_gbm.png
│
└── README.md
```

---

## ⚙️ Kullanılan Kütüphaneler

```r
library(dplyr)        # Veri manipülasyonu
library(tidyr)        # Veri temizleme
library(caret)        # Makine öğrenmesi framework'ü
library(ggplot2)      # Görselleştirme
library(randomForest) # Random Forest modeli
library(nnet)         # Lojistik regresyon (çok sınıflı)
library(gbm)          # Gradient Boosting Machine
library(corrplot)     # Korelasyon matrisi görselleştirme
library(pROC)         # ROC & AUC analizi
library(tidytext)     # Metin işleme
library(knitr)        # R Markdown desteği
library(kableExtra)   # Gelişmiş tablo formatı
```

---

## 🚀 Kullanım

### Eğitim

```r
# Proje dizinini ayarlayın
PROJECT_DIR <- "C:/Users/Bartu/Desktop/Customer_Status_Prediction"

# Eğitim scriptini çalıştırın
source("train.R")
# → train_models_final.RData dosyasını oluşturur
```

### Tahmin

```r
# Eğitim tamamlandıktan sonra test pipeline'ını çalıştırın
source("predict.R")
# → train_models_final.RData dosyasını yükler ve test verisine uygular
```

> ⚠️ **Not:** Tahmin scriptini çalıştırmadan önce `train.R` tamamlanmış olmalı ve `train_models_final.RData` dosyası mevcut olmalıdır.

---

## 🔁 Pipeline Akışı

```
Ham Veriler (9 CSV)
       ↓
  ID Standardizasyonu
       ↓
  Eksik Değer İşleme
       ↓
  EDA & Korelasyon Analizi
       ↓
  Feature Engineering (+6 yeni özellik)
       ↓
  Değişken Seçimi & One-Hot Encoding
       ↓
  Train/Validation Split
       ↓
  Model Eğitimi (RF + LogReg + GBM)
       ↓
  Değerlendirme (Confusion Matrix, ROC, AUC)
       ↓
  En İyi Model Kaydı (train_models_final.RData)
       ↓
  Test Verisi Üzerinde Tahmin
```

---

## 📈 Çıktı Dosyaları

| Dosya | Açıklama |
|---|---|
| `train_models_final.RData` | Eğitilmiş modeller ve preprocessing nesneleri |
| `confusion_matrix_*.png` | Her model için confusion matrix görselleştirmesi |
| `roc_curves_*.png` | One-vs-Rest ROC eğrileri |
| `auc_scores_*.png` | Model-sınıf bazlı AUC karşılaştırma grafikleri |
| `new_features_correlation.png` | Yeni özelliklerin hedefle korelasyonu |

---

*Bu proje, Bartu Paçal ve Begüm Başovalı tarafından geliştirilmiştir.*
**Tarih:** 2025
