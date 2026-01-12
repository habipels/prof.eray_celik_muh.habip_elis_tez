# KAŞİF-AI Hızlı Başlangıç Kılavuzu

## 📦 Kurulum

### 1. Gereksinimler
- Python 3.10+
- pip

### 2. Bağımlılıkları Yükle
```bash
# Sanal ortam oluştur (önerilen)
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# Bağımlılıkları yükle
pip install -r requirements.txt
```

### 3. Ortam Değişkenlerini Ayarla
```bash
# .env dosyası oluştur
# İsteğe bağlı: LLM kullanımı için
export OPENAI_API_KEY='your-api-key-here'
export LLM_MODEL='gpt-4'
```

## 🚀 Temel Kullanım

### Örnek 1: Basit Öğrenme Oturumu

```python
from core import KasifAIEngine

# Motoru başlat
engine = KasifAIEngine()

# Yeni öğrenci için oturum başlat
session = engine.start_learning_session(
    learner_id="student_001",
    target_topics=['pandas', 'statistics']
)

# İlk içerik ve açıklama
print(f"Konu: {session['recommendation'].topic}")
print(f"Açıklama: {session['explanation'].summary}")

# Öğrenci cevap göndersin
response = engine.submit_learner_response(
    learner_id="student_001",
    content_id=session['content'].content_id,
    response="df.head()",
    time_spent_seconds=120,
    attempts_count=1
)

print(f"Geri Bildirim: {response['feedback']}")
```

### Örnek 2: Performans Takibi

```python
# Performans özeti
summary = engine.get_performance_summary("student_001")
print(f"Hakimiyet: {summary['state']['overall_mastery']:.0%}")

# İlerleme raporu
report = engine.get_learning_progress_report("student_001", days=7)
print(report['report'].detailed_explanation)
```

### Örnek 3: İpucu Sistemi

```python
from models.content import Question, QuestionType, ContentMetadata
from datetime import datetime

# Soru oluştur (mock)
question = Question(
    question_id="q_001",
    question_type=QuestionType.CODE_WRITING,
    content_text="DataFrame'de ilk 5 satırı nasıl gösterirsin?",
    metadata=ContentMetadata(
        created_at=datetime.now(),
        difficulty_level=DifficultyLevel.BEGINNER,
        knowledge_components=['pandas'],
        learning_objectives=[],
        estimated_time_minutes=5
    )
)

# İpucu iste
hint_response = engine.request_hint(
    learner_id="student_001",
    question=question,
    attempt_count=2,
    time_spent_seconds=180
)

if hint_response['hint_provided']:
    print(f"İpucu: {hint_response['hint'].content}")
```

## 📊 Mimari Bileşenler

### 1. Öğrenen Profili Motoru
```python
from engines.learner_profile_engine import LearnerProfileEngine

profile_engine = LearnerProfileEngine()
profile = profile_engine.create_profile("student_001")
```

### 2. Öğrenme Durumu Tahminleyici
```python
from engines.learning_state_predictor import LearningStatePredictor

predictor = LearningStatePredictor()
state = predictor.predict_learning_state(profile)
print(f"Risk Seviyesi: {state.risk_assessment.risk_level}")
```

### 3. İçerik Üretim Motoru
```python
from engines.content_generation_engine import ContentGenerationEngine

content_engine = ContentGenerationEngine()
question = content_engine.generate_question(
    learner_profile=profile,
    learning_state=state,
    topic="pandas",
    knowledge_components=["pandas_filtering"]
)
```

### 4. Uyarlanabilir Yönlendirme Motoru
```python
from engines.adaptive_guidance_engine import AdaptiveGuidanceEngine

guidance = AdaptiveGuidanceEngine()
recommendation = guidance.recommend_next_step(
    profile=profile,
    state=state,
    completed_topics=[],
    current_session_duration_minutes=30
)
```

### 5. Açıklama Motoru
```python
from engines.explanation_feedback_engine import ExplanationFeedbackEngine

explainer = ExplanationFeedbackEngine()
explanation = explainer.explain_content_selection(
    content, profile, state, recommendation
)
print(explanation.detailed_explanation)
```

## 🔧 Konfigürasyon

### Config Örneği
```python
from core.config import Config

config = Config()

# AI ayarları
config.ai.llm_provider = "openai"
config.ai.llm_model = "gpt-4"
config.ai.temperature = 0.7

# Öğrenme ayarları
config.learning.mastery_threshold = 0.7
config.learning.consecutive_failure_limit = 3

# Pedagoji ayarları
config.pedagogy.scaffolding_enabled = True
config.pedagogy.immediate_feedback = True
```

## 📈 Metrikler

### Öğrenen Metrikleri
```python
from utils.metrics import PerformanceMetrics

metrics = PerformanceMetrics()

# Hakimiyet skoru
mastery = metrics.calculate_mastery_score(
    correct_count=7,
    total_count=10,
    confidence_factor=1.0
)

# Öğrenme hızı
velocity = metrics.calculate_learning_velocity(
    records=profile.performance_history,
    time_window_hours=24
)
```

### Sistem Metrikleri
```python
from utils.metrics import SystemEffectivenessMetrics

sys_metrics = SystemEffectivenessMetrics()

# Uyarlama doğruluğu
accuracy = sys_metrics.adaptation_accuracy(
    recommended_difficulties=[...],
    actual_performances=[...]
)
```

## 🎓 Pedagojik Araçlar

```python
from utils.pedagogy import (
    map_difficulty_to_bloom,
    generate_socratic_question,
    calculate_optimal_practice_interval
)

# Bloom taksonomisi eşlemesi
bloom_level = map_difficulty_to_bloom(
    difficulty=DifficultyLevel.INTERMEDIATE,
    mastery=0.6
)

# Sokratik soru
question = generate_socratic_question(
    topic="pandas",
    error_type=ErrorType.CONCEPTUAL
)

# Optimal pratik aralığı
days = calculate_optimal_practice_interval(
    current_mastery=0.6,
    performance_history=[True, True, False, True]
)
```

## 🧪 Test Etme

### Örnek Kullanım Dosyasını Çalıştır
```bash
python example_usage.py
```

Bu dosya üç örnek senaryoyu çalıştırır:
1. Tam öğrenme oturumu
2. Performans takibi
3. Uyarlanabilir zorluk ayarlama

## 📚 Dokümantasyon

- **README.md**: Genel bakış ve kurulum
- **ARCHITECTURE.md**: Detaylı mimari dokümantasyon
- **PSEUDOCODE.md**: Algoritma pseudocode'ları (tez için)

## 🤝 Akademik Kullanım

Bu sistem akademik araştırma için tasarlanmıştır. Tezinizde kullanırken:

1. **Algoritma Açıklamaları**: `PSEUDOCODE.md` dosyasını kullanın
2. **Mimari Şemalar**: `ARCHITECTURE.md`'deki diyagramları referans alın
3. **Teori Temelleri**: Her modülün akademik referanslarını kaynaklarda belirtin

## ⚠️ Önemli Notlar

### LLM Kullanımı
- Gerçek LLM kullanımı için API key gereklidir
- API key olmadan sistem **mock mode**'da çalışır
- Mock mode tam fonksiyonel ama üretken içerik placeholder'dır

### Veritabanı
- Şu anki implementasyon memory-based'dir
- Production için PostgreSQL entegrasyonu önerilir

### Ölçeklenebilirlik
- Mevcut mimari küçük-orta ölçekli kullanım için tasarlanmıştır
- Büyük ölçek için Redis cache ve async işleme önerilir

## 🐛 Sorun Giderme

### Problem: Import hataları
```bash
# Python path'i kontrol et
export PYTHONPATH="${PYTHONPATH}:/path/to/habip_elis_tez"
```

### Problem: LLM API çağrısı başarısız
```python
# Mock mode'u açıkça kullan
engine = KasifAIEngine(llm_client=None)
```

## 📞 Destek

Bu proje bir akademik tez projesidir. Sorular için:
- 📧 E-posta: [email]
- 📄 Tez Danışmanı: [danışman_adı]

## 📝 Lisans

Akademik araştırma amaçlı - Tez projesi

---

**Son Güncelleme**: 11 Ocak 2026  
**Versiyon**: 0.1.0
