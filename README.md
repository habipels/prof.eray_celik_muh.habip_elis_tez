# KAŞİF-AI: Üretken Yapay Zekâ Destekli Uyarlanabilir Öğrenme Platformu

## 🎯 Proje Özeti

KAŞİF-AI, veri bilimi eğitimi için tasarlanmış, öğrenen performansına dayalı olarak içerik üreten, yönlendiren ve uyarlayan bir yapay zekâ destekli öğrenme platformudur.

## 🏗️ Çekirdek Mimari Prensipler

1. **Rehberlik Odaklı**: YZ bir cevap makinesi değil, öğrenme rehberidir
2. **Sürekli Ölçüm**: Öğrenenin seviyesi gerçek zamanlı izlenir
3. **Yapılandırılmış Destek**: Doğrudan çözüm yerine ipucu, yönlendirici soru ve düşünme teşviki
4. **Şeffaflık**: Black-box davranış yok, her karar açıklanabilir (XAI)
5. **Kanıt Tabanlı**: Pedagojik ve etik sınırlar içinde

## 📊 Sistem Mimarisi

```
┌─────────────────────────────────────────────────────────────┐
│                    KAŞİF-AI CORE ENGINE                      │
└─────────────────────────────────────────────────────────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │
        ▼                     ▼                     ▼
┌───────────────┐    ┌────────────────┐    ┌──────────────┐
│   Öğrenen     │◄──►│    Öğrenme     │◄──►│   İçerik     │
│   Profili     │    │     Durumu     │    │    Üretim    │
│   Motoru      │    │  Tahminleyici  │    │    Motoru    │
└───────────────┘    └────────────────┘    └──────────────┘
        │                     │                     │
        └─────────────────────┼─────────────────────┘
                              │
        ┌─────────────────────┴─────────────────────┐
        ▼                                           ▼
┌───────────────┐                          ┌────────────────┐
│ Uyarlanabilir │                          │   Açıklama ve  │
│  Yönlendirme  │                          │  Geri Bildirim │
│    Motoru     │                          │     Motoru     │
└───────────────┘                          └────────────────┘
```

## 🧩 Sistem Bileşenleri

### 1. Öğrenen Profili Motoru (`learner_profile_engine`)
- **Görev**: Öğrenenin dinamik bilgi modelini oluşturur ve günceller
- **Veri**: Ön bilgi, performans geçmişi, hata türleri, öğrenme hızı
- **Çıktı**: Öğrenen profil nesnesi (LearnerProfile)

### 2. Öğrenme Durumu Tahminleyici (`learning_state_predictor`)
- **Görev**: Öğrenenin mevcut durum ve yeterlilik seviyesini tahmin eder
- **Veri**: Profil verisi, cevap doğruluğu, geçmiş performans
- **Çıktı**: Zorluk seviyesi, hakimiyet skoru, risk değerlendirmesi

### 3. İçerik Üretim Motoru (`content_generation_engine`)
- **Görev**: LLM kullanarak seviye uyumlu içerik üretir
- **Veri**: Öğrenen seviyesi, konu hedefi, pedagojik şablon
- **Çıktı**: Soru, kod tamamlama görevi, senaryo, ipucu

### 4. Uyarlanabilir Yönlendirme Motoru (`adaptive_guidance_engine`)
- **Görev**: Öğrenme yolunu dinamik olarak planlar
- **Veri**: Durum tahmini, öğrenme hedefleri
- **Çıktı**: Sonraki adım önerisi, zorluk ayarı, müdahale kararı

### 5. Açıklama ve Geri Bildirim Motoru (`explanation_feedback_engine`)
- **Görev**: XAI prensipleriyle karar açıklaması yapar
- **Veri**: Sistem kararları, öğrenen sorguları
- **Çıktı**: İnsan okunabilir açıklamalar

## 📁 Proje Yapısı

```
kasif_ai/
├── core/                       # Çekirdek AI motoru
│   ├── __init__.py
│   ├── engine.py              # Ana orkestrasyon motoru
│   └── config.py              # Konfigürasyon
├── models/                     # Veri modelleri
│   ├── __init__.py
│   ├── learner.py             # Öğrenen profil modeli
│   ├── content.py             # İçerik modelleri
│   └── learning_state.py      # Öğrenme durumu modelleri
├── engines/                    # AI motorları
│   ├── __init__.py
│   ├── learner_profile_engine.py
│   ├── learning_state_predictor.py
│   ├── content_generation_engine.py
│   ├── adaptive_guidance_engine.py
│   └── explanation_feedback_engine.py
├── utils/                      # Yardımcı araçlar
│   ├── __init__.py
│   ├── metrics.py             # Performans metrikleri
│   └── pedagogy.py            # Pedagojik yardımcılar
├── tests/                      # Test modülleri
└── requirements.txt            # Python bağımlılıkları
```

## 🔄 Veri Akışı

```
1. Öğrenen ⟹ Öğrenen Profili Motoru ⟹ Profil Güncelleme
                    ↓
2. Profil + Cevaplar ⟹ Öğrenme Durumu Tahminleyici ⟹ Durum Skoru
                    ↓
3. Durum Skoru ⟹ Uyarlanabilir Yönlendirme ⟹ Strateji Kararı
                    ↓
4. Strateji + Profil ⟹ İçerik Üretim Motoru ⟹ Yeni İçerik
                    ↓
5. Tüm Kararlar ⟹ Açıklama Motoru ⟹ Gerekçeli Geri Bildirim
                    ↓
6. İçerik + Açıklama ⟹ Öğrenen
```

## 📊 Performans Metrikleri

### Öğrenen Seviyesi
- **Hakimiyet Skoru (MS)**: 0-100 arası konu bilgi seviyesi
- **Öğrenme Hızı (LR)**: Zaman başına kazanç oranı
- **Hata Dağılımı (ED)**: Kavramsal vs Uygulamsal hata oranı

### Sistem Etkinliği
- **Uyarlama Doğruluğu**: Önerilen zorluk vs gerçek performans uyumu
- **Müdahale Zamanlaması**: Risk tespiti gecikme süresi
- **İçerik Uygunluğu**: Üretilen içeriğin pedagojik uygunluk skoru

## 🛠️ Teknoloji Stack

- **Backend**: Python 3.10+, FastAPI
- **AI/ML**: LangChain, OpenAI API / Anthropic Claude
- **Veri**: PostgreSQL, Redis (cache)
- **Metrikler**: scikit-learn, NumPy, pandas

## 🚀 Kurulum

```bash
# Sanal ortam oluştur
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# Bağımlılıkları yükle
pip install -r requirements.txt

# Ortam değişkenlerini ayarla
cp .env.example .env
# .env dosyasını düzenle (OpenAI API key vb.)
```

## 📖 Akademik Referans

Bu mimari aşağıdaki akademik prensiplere dayanır:

- **Intelligent Tutoring Systems (ITS)**: Bilgi izleme ve uyarlanabilir öğretim
- **Explainable AI (XAI)**: LIME, SHAP benzeri açıklama teknikleri
- **Learning Analytics**: Evidence-Centered Design (ECD)
- **Cognitive Load Theory**: Aşamalı zorluk artışı
- **Zone of Proximal Development (ZPD)**: Vygotsky'nin optimal öğrenme bölgesi

## 📄 Lisans

Akademik araştırma amaçlı - Tez çalışması

## 👤 Geliştirici

Habip Elis - Yüksek Lisans Tez Projesi
