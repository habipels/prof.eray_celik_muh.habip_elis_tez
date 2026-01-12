# KAŞİF-AI Teknik Mimari Dokümantasyonu

## 📋 İçindekiler

1. [Sistem Mimarisi](#sistem-mimarisi)
2. [Modüller Arası Veri Akışı](#veri-akışı)
3. [Algoritma Detayları](#algoritmalar)
4. [Akademik Temeller](#akademik-temeller)
5. [Performans Metrikleri](#metrikler)
6. [API Referansı](#api)

---

## 1. Sistem Mimarisi {#sistem-mimarisi}

### 1.1 Genel Bakış

KAŞİF-AI, beş ana motor ve üç destek modülünden oluşan modüler bir yapıya sahiptir:

```
┌─────────────────────────────────────────────────────┐
│           KasifAIEngine (Orkestrasyon)               │
└────────────────┬────────────────────────────────────┘
                 │
        ┌────────┴────────┐
        │                 │
        ▼                 ▼
┌───────────────┐   ┌──────────────────┐
│   Models      │   │     Engines       │
│               │   │                   │
│ • Learner    │   │ • Profile         │
│ • Content    │◄──┤ • State Pred.     │
│ • State      │   │ • Content Gen.    │
│              │   │ • Guidance        │
│              │   │ • Explanation     │
└──────────────┘   └──────────────────┘
```

### 1.2 Modül Açıklamaları

#### 1.2.1 Öğrenen Profili Motoru (Learner Profile Engine)

**Görev**: Öğrenenin dinamik bilgi modelini oluşturur ve günceller.

**Temel Fonksiyonlar**:
- `create_profile()`: Yeni öğrenen profili
- `update_profile_with_performance()`: Performans güncellemesi
- `get_learning_insights()`: İçgörü üretimi

**Algoritma**: Bayesian Knowledge Tracing (BKT)

**Formül**:
```
P(L_t|obs) = P(obs|L_t) × P(L_t) / P(obs)

Güncelleme:
P(L_{t+1}) = P(L_t|obs) + (1 - P(L_t|obs)) × P(T)

Nerede:
- L_t: t anında öğrenilmiş olma olasılığı
- P(T): Transfer (öğrenme) olasılığı
- P(obs): Gözlem (doğru/yanlış cevap)
```

**Veri Akışı**:
```
PerformanceRecord → BKT Güncelleme → KnowledgeComponent.mastery_level
                  → Error Pattern Analysis
                  → Learning Rate Calculation
                  → LearnerProfile (güncellenmiş)
```

---

#### 1.2.2 Öğrenme Durumu Tahminleyici (Learning State Predictor)

**Görev**: Anlık öğrenme durumu, risk değerlendirmesi ve hakimiyet tahmini.

**Temel Fonksiyonlar**:
- `predict_learning_state()`: Durum tahmini
- `_assess_risk()`: Risk analizi
- `_calculate_zpd_match()`: ZPD uyumu

**Risk Skoru Hesaplama**:
```python
risk_score = (
    consecutive_failures × 0.1 +
    declining_performance × 0.2 +
    low_engagement × 0.15 +
    excessive_time × 0.1 +
    help_dependency × 0.15
)

Risk Seviyeleri:
- LOW: risk < 0.25
- MODERATE: 0.25 ≤ risk < 0.5
- HIGH: 0.5 ≤ risk < 0.75
- CRITICAL: risk ≥ 0.75
```

**ZPD (Zone of Proximal Development) Hesaplaması**:
```
Optimal Başarı Aralığı: 60%-70%

ZPD_match = {
    1.0,              if 0.6 ≤ success ≤ 0.7  (mükemmel)
    0.8,              if 0.5 ≤ success < 0.6 or 0.7 < success ≤ 0.8
    0.5,              if 0.4 ≤ success < 0.5 or 0.8 < success ≤ 0.9
    0.3,              otherwise (çok kolay/zor)
}
```

---

#### 1.2.3 İçerik Üretim Motoru (Content Generation Engine)

**Görev**: LLM kullanarak seviye uyumlu, pedagojik içerik üretimi.

**Temel Fonksiyonlar**:
- `generate_question()`: Soru üretimi
- `generate_hint()`: İpucu üretimi (Scaffolding)
- `generate_adaptive_feedback()`: Uyarlanabilir geri bildirim

**İçerik Üretim Pipeline**:
```
1. Input Analysis
   ├─ Learner Profile
   ├─ Learning State
   └─ Topic + Difficulty

2. Prompt Engineering
   ├─ System Prompt (Pedagojik rol)
   ├─ Context Injection (Öğrenen bağlamı)
   └─ Template Rendering

3. LLM Generation
   ├─ API Call (OpenAI/Anthropic)
   └─ Temperature: 0.6-0.8

4. Post-processing
   ├─ Content Validation
   ├─ Metadata Addition
   └─ Quality Check

5. Output: Content Object
```

**Bloom Taksonomisi Eşlemesi**:
```python
difficulty_to_bloom = {
    BEGINNER: "remember" | "understand",
    ELEMENTARY: "understand" | "apply",
    INTERMEDIATE: "apply",
    ADVANCED: "analyze",
    EXPERT: "evaluate" | "create"
}
```

---

#### 1.2.4 Uyarlanabilir Yönlendirme Motoru (Adaptive Guidance Engine)

**Görev**: Öğrenme yolu planlama ve dinamik uyarlama.

**Temel Fonksiyonlar**:
- `recommend_next_step()`: Sonraki adım önerisi
- `adjust_difficulty()`: Zorluk ayarlama
- `should_provide_hint()`: İpucu kararı

**Müdahale Karar Ağacı**:
```
IF session_duration > 90 min THEN
    → BREAK müdahalesi
ELSE IF cognitive_load > 0.85 THEN
    → SIMPLIFY müdahalesi
ELSE IF risk_level == CRITICAL THEN
    → REMEDIATE müdahalesi
ELSE IF consecutive_failures ≥ 3 THEN
    → REMEDIATE müdahalesi
ELSE IF self_efficacy < 0.3 THEN
    → ENCOURAGE müdahalesi
ELSE IF mastery > 0.85 AND zpd_match < 0.5 THEN
    → CHALLENGE müdahalesi
ELSE
    → NONE (normal akış)
```

**Zorluk Ayarlama Algoritması**:
```python
success_rate = recent_performance.mean()

IF success_rate > 0.85 AND zpd_match < 0.5:
    difficulty += 1  # Zorlaştır
ELIF success_rate < 0.4:
    difficulty -= 1  # Kolaylaştır
ELIF 0.6 ≤ success_rate ≤ 0.75:
    # Optimal, değiştirme
    pass
```

---

#### 1.2.5 Açıklama ve Geri Bildirim Motoru (Explanation Feedback Engine)

**Görev**: XAI (Explainable AI) prensipleriyle karar açıklama.

**Temel Fonksiyonlar**:
- `explain_content_selection()`: İçerik seçim açıklaması
- `explain_difficulty_change()`: Zorluk değişim açıklaması
- `generate_performance_summary()`: Performans raporu

**Açıklama Şablonu**:
```
Explanation {
    type: WHY_THIS_CONTENT | WHY_DIFFICULTY | ...
    
    summary: "Kısa özet (1-2 cümle)"
    
    detailed_explanation: """
        1. Karar (ne yapıldı?)
        2. Veri (hangi verilere dayandı?)
        3. Mantık (neden bu karar alındı?)
        4. Pedagojik gerekçe (eğitim bilimi açıklaması)
    """
    
    supporting_evidence: [
        "Metrik 1: Değer",
        "Metrik 2: Değer",
        ...
    ]
    
    confidence: 0.0-1.0
}
```

---

## 2. Modüller Arası Veri Akışı {#veri-akışı}

### 2.1 Tam Öğrenme Döngüsü

```
┌─────────────────────────────────────────────────────────┐
│ 1. Oturum Başlatma                                      │
└────────────┬────────────────────────────────────────────┘
             │
             ▼
  ┌──────────────────────┐
  │ LearnerProfileEngine │
  │  • get_profile()     │
  └──────────┬───────────┘
             │
             ▼
  ┌──────────────────────────┐
  │ LearningStatePredictor   │
  │  • predict_state()       │
  │  • assess_risk()         │
  └──────────┬───────────────┘
             │
             ▼
  ┌──────────────────────────┐
  │ AdaptiveGuidanceEngine   │
  │  • recommend_next_step() │
  └──────────┬───────────────┘
             │
             ▼
  ┌──────────────────────────┐
  │ ContentGenerationEngine  │
  │  • generate_question()   │
  └──────────┬───────────────┘
             │
             ▼
  ┌──────────────────────────┐
  │ ExplanationEngine        │
  │  • explain_selection()   │
  └──────────┬───────────────┘
             │
             ▼
     [Content + Explanation]
             │
             │ (Öğrenen cevaplar)
             │
             ▼
  ┌──────────────────────────┐
  │ 2. Cevap İşleme          │
  │  • Değerlendirme         │
  │  • Profil Güncelleme     │
  └──────────┬───────────────┘
             │
             └──────► (Döngü tekrar)
```

### 2.2 Veri Modelleri İlişkileri

```
LearnerProfile
    ├─ knowledge_components: Dict[str, KnowledgeComponent]
    ├─ performance_history: List[PerformanceRecord]
    ├─ error_pattern: Dict[ErrorType, int]
    └─ learning_rate, persistence_score, etc.
           │
           ▼
    LearningState
        ├─ mastery_scores: Dict[str, MasteryScore]
        ├─ risk_assessment: RiskAssessment
        ├─ cognitive_load, zpd_match
        └─ recommended_difficulty
               │
               ▼
        Content
            ├─ question: Question
            ├─ metadata: ContentMetadata
            └─ adaptation_reason
```

---

## 3. Algoritma Detayları {#algoritmalar}

### 3.1 Bayesian Knowledge Tracing (BKT)

**Amaç**: Öğrenenin bir bilgi bileşenine (KC) hakimiyet olasılığını tahmin etme.

**Parametreler**:
- `P(L_0)`: Başlangıç öğrenme olasılığı
- `P(T)`: Transfer (öğrenme) oranı
- `P(S)`: Slip (bildiği halde hata) oranı
- `P(G)`: Guess (bilmediği halde doğru) oranı

**Güncelleme**:
```
Eğer cevap DOĞRU ise:
    P(L|correct) = P(correct|L) × P(L) / P(correct)
    
    P(correct) = P(correct|L)×P(L) + P(correct|¬L)×P(¬L)
               = (1-P(S))×P(L) + P(G)×(1-P(L))

Eğer cevap YANLIŞ ise:
    P(L|incorrect) = P(incorrect|L) × P(L) / P(incorrect)
    
    P(incorrect) = P(S)×P(L) + (1-P(G))×(1-P(L))

Son güncelleme:
    P(L_{t+1}) = P(L_t|obs) + (1 - P(L_t|obs)) × P(T)
```

**Kod İmplementasyonu**: `engines/learner_profile_engine.py:_update_knowledge_component_bayesian()`

---

### 3.2 Cognitive Load Estimation

**Amaç**: Öğrenenin zihinsel yükünü tahmin etme (Sweller'in Bilişsel Yük Teorisi).

**Faktörler**:
```python
cognitive_load = (
    error_rate × 0.3 +           # Yüksek hata = yüksek yük
    attempts_factor × 0.2 +       # Çok deneme = zorluk
    help_factor × 0.2 +           # Çok yardım = zorluk
    time_factor × 0.3             # Uzun süre = zorluk
)

Nerede:
- error_rate = failed / total
- attempts_factor = min(avg_attempts / 5, 1.0)
- help_factor = help_requests / total
- time_factor = min(avg_time / 600, 1.0)  # 10 min = 1.0
```

**Kod**: `engines/learning_state_predictor.py:_estimate_cognitive_load()`

---

### 3.3 Spacing Effect (Aralıklı Tekrar)

**Amaç**: Ebbinghaus'un unutma eğrisine göre optimal tekrar aralığı belirleme.

**Unutma Eğrisi**:
```
R(t) = e^(-t/S)

Nerede:
- R(t): t zamanında hatırlama olasılığı
- S: Dayanıklılık (mastery ile artar)
- t: Son pratikten bu yana geçen zaman

Dayanıklılık Hesaplama:
S = 1 + (mastery_level × 30)  # 1-31 gün arası

Tekrar Önceliği:
priority = 1 - R(t)  # Düşük R = yüksek öncelik
```

**Optimal Aralık Hesaplama (SM-2 benzeri)**:
```python
IF mastery < 0.3:
    base_interval = 1 day
ELIF mastery < 0.5:
    base_interval = 2 days
ELIF mastery < 0.7:
    base_interval = 4 days
ELIF mastery < 0.9:
    base_interval = 7 days
ELSE:
    base_interval = 14 days

Performansa göre ayarlama:
IF recent_success > 0.8:
    interval = base × 1.5  # Uzat
ELIF recent_success < 0.5:
    interval = base × 0.5  # Kısalt
```

**Kod**: `utils/pedagogy.py:calculate_optimal_practice_interval()`

---

## 4. Akademik Temeller {#akademik-temeller}

### 4.1 Teori ve Araştırma Temelleri

#### 4.1.1 Intelligent Tutoring Systems (ITS)

**Kaynak**: Anderson et al. (1995) - Cognitive Tutors

**Uygulanışı**:
- Bilgi izleme (Knowledge Tracing)
- Uyarlanabilir problem seçimi
- Immediate feedback
- Hint generation sistemi

---

#### 4.1.2 Zone of Proximal Development (ZPD)

**Kaynak**: Vygotsky (1978)

**Tanım**: Öğrenenin tek başına yapamadığı ama yardımla yapabildiği alan.

**Uygulanışı**:
```
ZPD uyumu = Optimal başarı oranı (%60-70)

Çok kolay (>%85) → Bağımsız yapabiliyor (ZPD dışı)
Optimal (%60-70) → ZPD içinde, ideal öğrenme
Çok zor  (<% 40) → Yardımla bile zor (ZPD dışı)
```

**Kod**: `engines/learning_state_predictor.py:_calculate_zpd_match()`

---

#### 4.1.3 Scaffolding Theory

**Kaynak**: Wood, Bruner, & Ross (1976)

**Prensip**: Kademeli destek, bağımsızlığa doğru.

**Seviyeleri**:
```
1. MINIMAL: "Hangi metodu kullanmalısın?"
2. GUIDING: "DataFrame metodlarını inceledin mi?"
3. SPECIFIC: "df.head() metodunu dene"
4. DETAILED: "df.head() ilk 5 satırı gösterir"
```

**Kod**: `engines/content_generation_engine.py:generate_hint()`

---

#### 4.1.4 Bloom's Taxonomy

**Kaynak**: Bloom et al. (1956), Anderson & Krathwohl (2001)

**Seviyeler**:
1. **Remember** (Hatırlama): Tanım, liste
2. **Understand** (Anlama): Açıklama, sınıflandırma
3. **Apply** (Uygulama): Kullanma, çözme
4. **Analyze** (Analiz): Ayırma, organize etme
5. **Evaluate** (Değerlendirme): Eleştirme, karar verme
6. **Create** (Yaratma): Tasarlama, üretme

**KAŞİF-AI'de Kullanımı**:
```python
# Zorluk seviyesine göre bilişsel seviye atama
difficulty → cognitive_level mapping
BEGINNER → remember/understand
INTERMEDIATE → apply
ADVANCED → analyze
EXPERT → evaluate/create
```

---

#### 4.1.5 Explainable AI (XAI)

**Kaynak**: Ribeiro et al. (2016) - LIME; Lundberg & Lee (2017) - SHAP

**Prensipler**:
1. **Transparency**: Her karar açıklanabilir
2. **Traceability**: Kararın veri kaynağı izlenebilir
3. **Human-Understandable**: İnsan okunabilir açıklamalar

**KAŞİF-AI'de Uygulanışı**:
```
Her karar için:
1. Summary: Kısa özet
2. Detailed: Detaylı açıklama
   - Karar nedir?
   - Hangi veriye dayandı?
   - Pedagojik gerekçe nedir?
3. Evidence: Destekleyici metrikler
4. Confidence: Güvenilirlik skoru
```

**Kod**: `engines/explanation_feedback_engine.py`

---

### 4.2 Performans Metrikleri (Literatür)

#### 4.2.1 Normalized Learning Gain

**Kaynak**: Hake (1998)

**Formül**:
```
<g> = (post_test - pre_test) / (max_score - pre_test)

Yorumlama:
<g> < 0.3  → Düşük kazanç
0.3 ≤ <g> < 0.7 → Orta kazanç
<g> ≥ 0.7  → Yüksek kazanç
```

**Kod**: `utils/metrics.py:calculate_learning_gain()`

---

#### 4.2.2 Retention Score

**Amaç**: Bilgi tutma (ne kadar akılda kalıyor?)

**Hesaplama**:
```
retention = later_success_rate / initial_success_rate

Nerede:
- initial: İlk öğrenme (ilk 3 kayıt)
- later: N gün sonraki performans
```

**Kod**: `utils/metrics.py:calculate_retention_score()`

---

## 5. Performans Metrikleri {#metrikler}

### 5.1 Öğrenen Metrikleri

| Metrik | Formül | Aralık | Yorumlama |
|--------|--------|--------|-----------|
| **Hakimiyet Skoru** | `correct / total × confidence` | 0-1 | KC'ye hakim olma derecesi |
| **Öğrenme Hızı** | `(recent_success - early_success) / time` | 0-1+ | Öğrenme velocity |
| **Katılım Skoru** | `(activity_score + time_score + help_score) / 3` | 0-1 | Platformla etkileşim |
| **Sebat Skoru** | `continued_after_failure / total_failures` | 0-1 | Zorluklarla baş etme |

### 5.2 Sistem Metrikleri

| Metrik | Açıklama | Hesaplama |
|--------|----------|-----------|
| **Uyarlama Doğruluğu** | Önerilen zorluk vs gerçek performans uyumu | `correct_predictions / total` |
| **Müdahale Zamanlaması** | Risk tespitinin ne kadar erken olduğu | `early_detections / failures` |
| **İçerik Uygunluğu** | Üretilen içeriğin pedagojik uygunluğu | Çok faktörlü skor |

---

## 6. API Referansı {#api}

### 6.1 Ana Motor (KasifAIEngine)

#### `start_learning_session(learner_id, target_topics=None)`

Yeni öğrenme oturumu başlatır.

**Parametreler**:
- `learner_id` (str): Öğrenen kimliği
- `target_topics` (List[str], optional): Hedef konular

**Döner**:
```python
{
    'session_id': str,
    'content': Content,
    'recommendation': NextStepRecommendation,
    'explanation': Explanation,
    'state_summary': {
        'overall_mastery': float,
        'current_difficulty': str,
        'risk_level': str
    }
}
```

---

#### `submit_learner_response(learner_id, content_id, response, time_spent_seconds, attempts_count, hint_used_count)`

Öğrenen cevabını işler.

**Döner**:
```python
{
    'is_correct': bool,
    'feedback': str,
    'next_content': Content,
    'recommendation': NextStepRecommendation,
    'explanation': Explanation,
    'state_update': {...}
}
```

---

### 6.2 Diğer Motorlar

Detaylı API dokümantasyonu için ilgili modül dosyalarına bakınız:
- `engines/learner_profile_engine.py`
- `engines/learning_state_predictor.py`
- `engines/content_generation_engine.py`
- `engines/adaptive_guidance_engine.py`
- `engines/explanation_feedback_engine.py`

---

## 📚 Kaynaklar

1. Anderson, J. R., Corbett, A. T., Koedinger, K. R., & Pelletier, R. (1995). Cognitive tutors: Lessons learned. *The journal of the learning sciences*, 4(2), 167-207.

2. Corbett, A. T., & Anderson, J. R. (1994). Knowledge tracing: Modeling the acquisition of procedural knowledge. *User modeling and user-adapted interaction*, 4(4), 253-278.

3. Vygotsky, L. S. (1978). *Mind in society: The development of higher psychological processes*. Harvard University Press.

4. Wood, D., Bruner, J. S., & Ross, G. (1976). The role of tutoring in problem solving. *Journal of child psychology and psychiatry*, 17(2), 89-100.

5. Bloom, B. S., Engelhart, M. D., Furst, E. J., Hill, W. H., & Krathwohl, D. R. (1956). *Taxonomy of educational objectives: The classification of educational goals*. Handbook 1: Cognitive domain.

6. Sweller, J. (1988). Cognitive load during problem solving: Effects on learning. *Cognitive science*, 12(2), 257-285.

7. Hake, R. R. (1998). Interactive-engagement versus traditional methods: A six-thousand-student survey of mechanics test data for introductory physics courses. *American journal of Physics*, 66(1), 64-74.

8. Ribeiro, M. T., Singh, S., & Guestrin, C. (2016). "Why should I trust you?" Explaining the predictions of any classifier. In *Proceedings of the 22nd ACM SIGKDD international conference on knowledge discovery and data mining* (pp. 1135-1144).

---

**Son Güncelleme**: 11 Ocak 2026  
**Versiyon**: 0.1.0  
**Yazar**: Habip Elis - Yüksek Lisans Tezi Projesi
