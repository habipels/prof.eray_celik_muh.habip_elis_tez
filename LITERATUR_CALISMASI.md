# KAŞİF-AI: Adaptif Yapay Zeka Destekli Öğrenme Sistemi
## Literatür Çalışması

**Yazar:** Habip Elis  
**Tarih:** Ocak 2026  
**Tez Konusu:** Bayesian Knowledge Tracing ve Zone of Proximal Development Tabanlı Adaptif Öğrenme Sistemi

---

## İçindekiler

1. [Giriş](#1-giriş)
2. [Akıllı Öğretim Sistemleri (ITS)](#2-akıllı-öğretim-sistemleri-its)
3. [Öğrenen Modelleme](#3-öğrenen-modelleme)
4. [Bayesian Knowledge Tracing (BKT)](#4-bayesian-knowledge-tracing-bkt)
5. [Zone of Proximal Development (ZPD)](#5-zone-of-proximal-development-zpd)
6. [Adaptif Öğrenme Sistemleri](#6-adaptif-öğrenme-sistemleri)
7. [Pedagojik Teoriler](#7-pedagojik-teoriler)
8. [Explainable AI (XAI) in Education](#8-explainable-ai-xai-in-education)
9. [Large Language Models (LLMs) in Education](#9-large-language-models-llms-in-education)
10. [İlgili Çalışmalar ve Sistemler](#10-i̇lgili-çalışmalar-ve-sistemler)
11. [Araştırma Boşlukları](#11-araştırma-boşlukları)
12. [Kaynakça](#12-kaynakça)

---

## 1. Giriş

Eğitim teknolojilerindeki gelişmeler, geleneksel sınıf ortamlarından kişiselleştirilmiş dijital öğrenme platformlarına doğru bir evrim göstermektedir. Yapay zeka destekli öğrenme sistemleri, her öğrenenin bireysel ihtiyaçlarına, öğrenme hızına ve bilgi seviyesine göre uyarlanabilir içerik sunma potansiyeline sahiptir.

KAŞİF-AI projesi, bu alanda **Bayesian Knowledge Tracing (BKT)** ve **Zone of Proximal Development (ZPD)** teorilerini birleştirerek, veri bilimi eğitimi için adaptif bir öğrenme sistemi geliştirmeyi amaçlamaktadır.

---

## 2. Akıllı Öğretim Sistemleri (ITS)

### 2.1 Tanım ve Tarihçe

**Intelligent Tutoring Systems (ITS)**, öğrenenlere bire bir öğretim deneyimi sunmak için yapay zeka tekniklerini kullanan bilgisayar tabanlı öğrenme ortamlarıdır (VanLehn, 2011).

#### Önemli Kilometre Taşları:
- **1970'ler:** SCHOLAR - İlk ITS prototipi (Carbonell, 1970)
- **1980'ler:** LISP Tutor - Programlama öğretimi için ITS (Anderson et al., 1985)
- **1990'lar:** Cognitive Tutors - Carnegie Learning tarafından matematik ITS'si
- **2000'ler:** AutoTutor - Doğal dil işleme ile diyalog tabanlı öğretim (Graesser et al., 2004)
- **2010'ler:** ASSISTments - Büyük ölçekli veri toplama ve analizi (Heffernan & Heffernan, 2014)
- **2020'ler:** LLM-tabanlı ITS - GPT ve benzeri modellerin entegrasyonu

### 2.2 ITS Bileşenleri

Klasik ITS mimarisi dört ana bileşenden oluşur (Nwana, 1990):

1. **Domain Model (Alan Modeli):** Öğretilecek konunun bilgi temsili
2. **Learner Model (Öğrenen Modeli):** Öğrenenin mevcut bilgi durumu
3. **Pedagogical Model (Pedagojik Model):** Öğretim stratejileri
4. **Interface Model (Arayüz Modeli):** İnsan-bilgisayar etkileşimi

**KAŞİF-AI'daki Karşılıklar:**
- Domain Model → Content Generation Engine + Knowledge Components
- Learner Model → Learner Profile Engine (BKT tabanlı)
- Pedagogical Model → Adaptive Guidance Engine (ZPD tabanlı)
- Interface Model → FastAPI Web Interface

---

## 3. Öğrenen Modelleme

### 3.1 Öğrenen Modelleme Yaklaşımları

Öğrenen modelleme, ITS'nin temel taşlarından biridir ve öğrenenin bilgisini, becerilerini ve özelliklerini temsil eder (Chrysafiadi & Virvou, 2013).

#### Başlıca Yaklaşımlar:

1. **Overlay Models:** Öğrenen bilgisini, uzman bilgisinin bir alt kümesi olarak görür
2. **Differential Models:** Öğrenenin hatalarını ve yanlış kavramalarını modeller
3. **Perturbation Models:** Doğru bilgi + sistematik hatalar
4. **Constraint-Based Models:** Öğrenen çözümlerindeki kısıtlamaları değerlendirir

### 3.2 Bilgi İzleme Yöntemleri

- **Item Response Theory (IRT):** Soru zorluk parametreleri (Lord, 1980)
- **Performance Factor Analysis (PFA):** Başarı, başarısızlık ve geri bildirim sayıları (Pavlik et al., 2009)
- **Bayesian Knowledge Tracing (BKT):** Olasılıksal bilgi durumu tahmini (Corbett & Anderson, 1995)
- **Deep Knowledge Tracing (DKT):** Derin öğrenme ile bilgi izleme (Piech et al., 2015)

**KAŞİF-AI Seçimi:** BKT kullanılmıştır çünkü:
- Yorumlanabilir parametreler (P(L), P(T), P(S), P(G))
- Eğitim araştırmalarında sağlam teorik temel
- Gerçek zamanlı güncelleme imkanı
- XAI için uygun (açıklanabilir parametreler)

---

## 4. Bayesian Knowledge Tracing (BKT)

### 4.1 Teorik Temel

BKT, Hidden Markov Model (HMM) kullanarak öğrenenin bir beceriyi öğrenme durumunu modelleyen bir algoritmadır (Corbett & Anderson, 1995).

#### Temel Varsayım:
Her bilgi bileşeni (Knowledge Component - KC) için öğrenen iki durumdan birindedir:
- **Learned (L):** Beceriyi öğrenmiş
- **Not Learned (~L):** Beceriyi henüz öğrenmemiş

### 4.2 BKT Parametreleri

| Parametre | Açıklama | Tipik Değer |
|-----------|----------|-------------|
| **P(L₀)** | İlk bilgi olasılığı | 0.1 - 0.3 |
| **P(T)** | Öğrenme (transition) olasılığı | 0.1 - 0.3 |
| **P(S)** | Sürçme (slip) olasılığı | 0.1 - 0.2 |
| **P(G)** | Şans eseri doğru (guess) olasılığı | 0.1 - 0.3 |

### 4.3 BKT Güncelleme Formülleri

**Doğru Cevap Durumunda:**
```
P(L_n | correct) = [P(L_n-1) * (1 - P(S))] / 
                   [P(L_n-1) * (1 - P(S)) + (1 - P(L_n-1)) * P(G)]

P(L_n) = P(L_n | correct) + (1 - P(L_n | correct)) * P(T)
```

**Yanlış Cevap Durumunda:**
```
P(L_n | incorrect) = [P(L_n-1) * P(S)] / 
                     [P(L_n-1) * P(S) + (1 - P(L_n-1)) * (1 - P(G))]

P(L_n) = P(L_n | incorrect) + (1 - P(L_n | incorrect)) * P(T)
```

### 4.4 BKT Varyantları ve Geliştirmeler

- **Individualized BKT:** Öğrenen-spesifik parametreler (Yudelson et al., 2013)
- **Contextual BKT:** Bağlamsal özellikler ekleme (Gonzalez-Brenes et al., 2014)
- **BKT+:** İpucu kullanımı ve yardım isteme davranışları (Pardos & Heffernan, 2011)

**KAŞİF-AI Implementasyonu:**
- Standart BKT algoritması
- KC bazında ayrı izleme
- Gerçek zamanlı güncelleme
- XAI için parametre açıklamaları

---

## 5. Zone of Proximal Development (ZPD)

### 5.1 Vygotsky'nin ZPD Teorisi

**Zone of Proximal Development (Yakınsal Gelişim Alanı)**, Lev Vygotsky (1978) tarafından geliştirilmiş, öğrenenin:
- Bağımsız yapabildiği
- Yardımla yapabildiği
- Henüz yapamadığı

görevler arasındaki bölgeyi tanımlar.

```
┌─────────────────────────────────────────┐
│  Yapılamayan (Frustration Zone)         │
│  ↑ Çok Zor                               │
├─────────────────────────────────────────┤
│  ZPD - Yardımla Yapılabilir              │ <- Optimal Öğrenme
│  ↑ Meydan Okuyucu ama Ulaşılabilir       │
├─────────────────────────────────────────┤
│  Bağımsız Yapılabilir (Comfort Zone)     │
│  ↑ Kolay                                 │
└─────────────────────────────────────────┘
```

### 5.2 ZPD ve Scaffolding

**Scaffolding (İskele Kurma):** Öğrenene geçici destek sağlayarak ZPD içinde öğrenmeyi kolaylaştırma (Wood et al., 1976).

#### Scaffolding Prensipleri:
1. **Fading:** Destek kademeli olarak azaltılır
2. **Tailoring:** Öğrenen ihtiyacına göre özelleştirilir
3. **Diagnosis:** Sürekli değerlendirme yapılır
4. **Internalization:** Öğrenen becerileri içselleştirir

### 5.3 ZPD'nin Hesaplamalı Modellenmesi

Modern ITS'lerde ZPD'nin hesaplanması için çeşitli yaklaşımlar:

- **Mastery-Based:** P(L) > 0.4 ve P(L) < 0.8 arası ZPD (Abdi et al., 2020)
- **Difficulty-Based:** Öğrenen seviyesi + 1 zorluk derecesi
- **Success Rate-Based:** %50-80 başarı aralığı (optimal challenge)

**KAŞİF-AI Yaklaşımı:**
```python
def calculate_zpd_bounds(mastery: float) -> Tuple[float, float]:
    """
    ZPD alt ve üst sınırlarını hesapla
    
    Mastery   | ZPD Alt | ZPD Üst | Önerilen Zorluk
    ----------|---------|---------|------------------
    0.0-0.2   | 0.1     | 0.4     | BEGINNER
    0.2-0.4   | 0.3     | 0.6     | ELEMENTARY
    0.4-0.6   | 0.5     | 0.8     | INTERMEDIATE
    0.6-0.8   | 0.7     | 0.9     | ADVANCED
    0.8-1.0   | 0.8     | 1.0     | EXPERT
    """
```

---

## 6. Adaptif Öğrenme Sistemleri

### 6.1 Adaptasyon Türleri

Brusilovsky (1996) adaptif eğitim sistemlerinde iki ana adaptasyon türü tanımlar:

1. **Adaptive Presentation:** İçeriğin öğrenene göre uyarlanması
2. **Adaptive Navigation:** Öğrenme yolunun dinamik belirlenmesi

**KAŞİF-AI'da:**
- Content Generation Engine → Adaptive Presentation
- Adaptive Guidance Engine → Adaptive Navigation

### 6.2 Adaptasyon Kriterleri

Öğrenme sistemleri şu kriterlere göre adapte olabilir:

| Kriter | Açıklama | KAŞİF-AI Kullanımı |
|--------|----------|-------------------|
| **Bilgi Seviyesi** | Öğrenenin mevcut bilgisi | BKT ile P(L) takibi |
| **Öğrenme Tercihi** | Görsel, işitsel vb. | (Gelecek çalışma) |
| **Öğrenme Hızı** | learning_rate parametresi | Profil motorunda |
| **Hatalar** | Hata tipleri ve frekansı | Error pattern tracking |
| **Motivasyon** | persistence_score | Profil motorunda |
| **Bilişsel Yük** | Görev karmaşıklığı | ZPD hesaplamasında |

### 6.3 Adaptif Algoritma Stratejileri

- **Macro-Adaptation:** Kurs ve modül seviyesinde adaptasyon
- **Micro-Adaptation:** Soru ve görev seviyesinde adaptasyon

KAŞİF-AI **micro-adaptation** stratejisi kullanır: Her soru sonrası BKT güncellenir ve bir sonraki soru ZPD'ye göre seçilir.

---

## 7. Pedagojik Teoriler

### 7.1 Bloom's Taxonomy (Bloom Taksonomisi)

Benjamin Bloom (1956) bilişsel öğrenme hedeflerini hiyerarşik olarak sınıflandırmıştır:

```
6. CREATE (Yaratma)           ⬆ Üst Düzey Düşünme
5. EVALUATE (Değerlendirme)   |
4. ANALYZE (Analiz)           |
3. APPLY (Uygulama)           |
2. UNDERSTAND (Anlama)        |
1. REMEMBER (Hatırlama)       ⬇ Alt Düzey Düşünme
```

**KAŞİF-AI Entegrasyonu:**
- BEGINNER → Remember, Understand
- ELEMENTARY → Understand, Apply
- INTERMEDIATE → Apply
- ADVANCED → Analyze
- EXPERT → Evaluate, Create

### 7.2 Constructivism (Yapılandırmacılık)

Piaget ve Vygotsky'nin çalışmalarına dayanan yapılandırmacı öğrenme teorisi, öğrenmenin aktif bir bilgi inşa süreci olduğunu savunur.

**İlkeler:**
- Öğrenen aktif katılımcıdır
- Ön bilgi yeni öğrenmeyi etkiler
- Sosyal etkileşim önemlidir
- Gerçek dünya bağlamı motivasyonu artırır

**KAŞİF-AI'da:**
- Kod yazma görevleri (aktif öğrenme)
- Gerçek dünya senaryoları
- İpucu sistemi (scaffolding)
- Immediate feedback

### 7.3 Mastery Learning (Ustalık Öğrenmesi)

Bloom'un mastery learning modeli, tüm öğrenenlerin yeterli zaman ve uygun öğretimle öğrenebileceğini öne sürer (Bloom, 1968).

**Prensipler:**
- Açık öğrenme hedefleri
- Küçük öğrenme birimleri
- Formatif değerlendirme
- Corrective feedback
- %80-90 mastery kriteri

**KAŞİF-AI'da:**
- Knowledge Component bazlı takip
- P(L) > 0.8 mastery threshold
- Zayıf KC'lerde ek pratik
- Adaptive guidance

---

## 8. Explainable AI (XAI) in Education

### 8.1 XAI Neden Önemlidir?

Eğitim bağlamında açıklanabilir yapay zeka (XAI), sistemin kararlarının öğrenen ve eğitimciler tarafından anlaşılmasını sağlar (Conati et al., 2021).

**Faydaları:**
- **Trust (Güven):** Öğrenenler sisteme güvenir
- **Learning:** Açıklamalar meta-bilişi destekler
- **Debugging:** Eğitimciler sistemi iyileştirebilir
- **Transparency:** Etik ve adil değerlendirme

### 8.2 XAI Yaklaşımları Eğitimde

1. **Model-Specific Explanations:** BKT parametreleri gibi içsel açıklamalar
2. **Post-Hoc Explanations:** LIME, SHAP gibi model-agnostic yöntemler
3. **Example-Based:** Benzer öğrenen performansları
4. **Natural Language:** LLM ile açıklama üretimi

### 8.3 KAŞİF-AI XAI Stratejisi

**Explanation Feedback Engine** şeffaf açıklamalar sağlar:

```python
{
    "decision": "INTERMEDIATE seviye soru önerildi",
    "reasons": [
        "Mevcut hakimiyet: 0.65 (INTERMEDIATE aralığı: 0.4-0.6)",
        "ZPD analizi: 0.5-0.8 arası optimal",
        "Son 3 soruda %66 başarı → devam et"
    ],
    "parameters_used": {
        "P(L)": 0.65,
        "ZPD_lower": 0.5,
        "ZPD_upper": 0.8
    }
}
```

**Açıklama Seviyeleri:**
1. **High-Level:** "Seviyene uygun soru seçildi"
2. **Medium-Level:** "Hakimiyetin %65, orta seviye öneriliyor"
3. **Technical:** BKT parametreleri ve formüller

---

## 9. Large Language Models (LLMs) in Education

### 9.1 LLM'lerin Eğitimdeki Potansiyeli

GPT-4, Claude, Llama gibi büyük dil modelleri eğitimde yeni olanaklar sunar:

- **Content Generation:** Otomatik soru ve senaryo üretimi
- **Personalized Feedback:** Her öğrene özel geri bildirim
- **Conversational Tutoring:** Diyalog tabanlı öğretim
- **Code Explanation:** Kod analizi ve açıklama

### 9.2 LLM Tabanlı ITS Çalışmaları

- **Khanmigo (Khan Academy):** GPT-4 tabanlı AI tutor (2023)
- **GitHub Copilot for Education:** Kod öğretimi için AI asistan
- **Duolingo Max:** GPT-4 ile dil öğrenme (2023)

### 9.3 KAŞİF-AI'da LLM Kullanımı

**Content Generation Engine** LLM entegrasyonuna hazır:

```python
class ContentGenerationEngine:
    def __init__(self, llm_client=None):
        self.llm_client = llm_client  # OpenAI, Anthropic vb.
        
    def generate_question(self, ...):
        if self.llm_client:
            prompt = self._build_question_prompt(...)
            generated = self._call_llm(prompt)
            return self._parse_question_response(generated)
        else:
            # Mock mode: 81 soruluk banka
            return self._generate_mock_question(...)
```

**Şu anki durum:** Mock mode (81 soruluk banka)  
**Gelecek çalışma:** OpenAI API entegrasyonu

### 9.4 LLM Zorlukları Eğitimde

- **Hallucination:** Yanlış bilgi üretimi
- **Consistency:** Tutarsız değerlendirmeler
- **Cost:** API maliyetleri
- **Privacy:** Öğrenci verisi güvenliği
- **Evaluation:** Otomatik değerlendirme zorluğu

**KAŞİF-AI Çözümleri:**
- Mock soru bankası ile test edilebilir temel
- Hybrid yaklaşım: LLM + rule-based evaluation
- `expected_answer` sistemi ile doğruluk kontrolü

---

## 10. İlgili Çalışmalar ve Sistemler

### 10.1 Akademik Çalışmalar

#### BKT Tabanlı Sistemler

**ASSISTments (Heffernan & Heffernan, 2014)**
- Platform: Web-based matematik öğretim sistemi
- Öğrenen Modeli: BKT
- Ölçek: 50,000+ öğrenci, milyonlarca veri noktası
- Katkı: BKT parametrelerinin büyük ölçekte optimizasyonu

**Cognitive Tutor (Anderson et al., 1995)**
- Platform: Carnegie Learning matematik yazılımı
- Öğrenen Modeli: ACT-R cognitive architecture + BKT
- Katkı: Model tracing ve knowledge tracing entegrasyonu
- Etki: ABD'de yüzlerce okulda kullanım

#### Adaptif Sistemler

**ALEKS (Assessment and Learning in Knowledge Spaces)**
- Teorik Temel: Knowledge Space Theory (Falmagne et al., 2006)
- Adaptasyon: Periodic assessment + adaptive practice
- Domain: Matematik, kimya, istatistik

**AutoTutor (Graesser et al., 2004)**
- Özellik: Natural language dialogue
- Pedagojik Strateji: Socratic tutoring
- Öğrenen Modeli: LSA (Latent Semantic Analysis)

### 10.2 Ticari Ürünler

| Sistem | Öğrenen Modeli | Adaptasyon | Domain |
|--------|---------------|------------|--------|
| **Khan Academy** | Item Response Theory | Mastery-based | Genel |
| **Duolingo** | Half-Life Regression | Spaced repetition | Dil |
| **Coursera** | Completion rates | Öneri sistemi | Genel |
| **DataCamp** | Skills tracking | Path-based | Veri Bilimi |
| **Codecademy** | Progress tracking | Linear paths | Programlama |

### 10.3 KAŞİF-AI'nın Farklılıkları

| Özellik | Diğer Sistemler | KAŞİF-AI |
|---------|----------------|----------|
| **Teorik Temel** | Tek teori | BKT + ZPD kombinasyonu |
| **Domain** | Genel veya çok geniş | Veri bilimi odaklı |
| **XAI** | Kısıtlı veya yok | Explanation Feedback Engine |
| **Öğrenen Modeli** | Basit veya black-box | Detaylı BKT parametreleri |
| **Adaptasyon** | Macro-level | Micro-level (soru bazında) |
| **Dil** | İngilizce | Türkçe |
| **Açık Kaynak** | Genellikle kapalı | Akademik proje (paylaşılabilir) |

---

## 11. Araştırma Boşlukları

### 11.1 Mevcut Literatürdeki Eksiklikler

1. **BKT ve ZPD Entegrasyonu**
   - Sorun: BKT ve ZPD genellikle ayrı kullanılır
   - Fırsat: İki teorinin sinerji potansiyeli
   - KAŞİF-AI Katkısı: BKT ile bilgi takibi, ZPD ile zorluk adaptasyonu

2. **Veri Bilimi Eğitiminde ITS**
   - Sorun: Matematik/programlamaya odak, veri bilimi az
   - Fırsat: Büyüyen alan, iş gücü talebi
   - KAŞİF-AI Katkısı: Python, pandas, istatistik, görselleştirme

3. **Türkçe Adaptif Sistemler**
   - Sorun: Çoğu sistem İngilizce
   - Fırsat: Türkiye'de dijital dönüşüm
   - KAŞİF-AI Katkısı: Türkçe doğal dil işleme ve içerik

4. **XAI in Educational AI**
   - Sorun: Black-box sistemler, güven sorunu
   - Fırsat: Açıklanabilir AI talebi artıyor
   - KAŞİF-AI Katkısı: Şeffaf BKT parametreleri, açıklama motoru

5. **Micro-Adaptasyon**
   - Sorun: Kurs seviyesinde adaptasyon yaygın
   - Fırsat: Soru bazında gerçek zamanlı adaptasyon
   - KAŞİF-AI Katkısı: Her soru sonrası BKT güncelleme

### 11.2 Gelecek Araştırma Yönleri

1. **LLM Entegrasyonu:** GPT-4 ile dinamik soru üretimi
2. **Multi-Modal Learning:** Video, kod, metin kombinasyonu
3. **Collaborative Learning:** Grup çalışması desteği
4. **Affective Computing:** Duygusal durum takibi
5. **Long-Term Studies:** Uzun vadeli etki değerlendirmesi

---

## 12. Kaynakça

### Foundational Works

**Bayesian Knowledge Tracing:**
- Corbett, A. T., & Anderson, J. R. (1995). Knowledge tracing: Modeling the acquisition of procedural knowledge. *User Modeling and User-Adapted Interaction*, 4(4), 253-278.
- Yudelson, M. V., Koedinger, K. R., & Gordon, G. J. (2013). Individualized bayesian knowledge tracing models. In *Artificial Intelligence in Education* (pp. 171-180). Springer.
- Pardos, Z. A., & Heffernan, N. T. (2011). KT-IDEM: Introducing item difficulty to the knowledge tracing model. In *User Modeling, Adaption and Personalization* (pp. 243-254).

**Zone of Proximal Development:**
- Vygotsky, L. S. (1978). *Mind in society: The development of higher psychological processes*. Harvard University Press.
- Wood, D., Bruner, J. S., & Ross, G. (1976). The role of tutoring in problem solving. *Journal of Child Psychology and Psychiatry*, 17(2), 89-100.
- Chaiklin, S. (2003). The zone of proximal development in Vygotsky's analysis of learning and instruction. *Vygotsky's Educational Theory in Cultural Context*, 1, 39-64.

### Intelligent Tutoring Systems

- VanLehn, K. (2011). The relative effectiveness of human tutoring, intelligent tutoring systems, and other tutoring systems. *Educational Psychologist*, 46(4), 197-221.
- Anderson, J. R., Corbett, A. T., Koedinger, K. R., & Pelletier, R. (1995). Cognitive tutors: Lessons learned. *The Journal of the Learning Sciences*, 4(2), 167-207.
- Graesser, A. C., Lu, S., Jackson, G. T., Mitchell, H. H., Ventura, M., Olney, A., & Louwerse, M. M. (2004). AutoTutor: A tutor with dialogue in natural language. *Behavior Research Methods, Instruments, & Computers*, 36(2), 180-192.
- Heffernan, N. T., & Heffernan, C. L. (2014). The ASSISTments ecosystem: Building a platform that brings scientists and teachers together for minimally invasive research on human learning and teaching. *International Journal of Artificial Intelligence in Education*, 24(4), 470-497.

### Adaptive Learning

- Brusilovsky, P. (1996). Methods and techniques of adaptive hypermedia. *User Modeling and User-Adapted Interaction*, 6(2-3), 87-129.
- Shute, V. J., & Zapata-Rivera, D. (2012). Adaptive educational systems. *Adaptive Technologies for Training and Education*, 7(27), 1-35.
- Plass, J. L., & Pawar, S. (2020). Toward a taxonomy of adaptivity for learning. *Journal of Research on Technology in Education*, 52(3), 275-300.

### Learner Modeling

- Chrysafiadi, K., & Virvou, M. (2013). Student modeling approaches: A literature review for the last decade. *Expert Systems with Applications*, 40(11), 4715-4729.
- Conati, C., Barral, O., Putnam, V., & Rieger, L. (2021). Toward personalized XAI: A case study in intelligent tutoring systems. *Artificial Intelligence*, 298, 103503.
- Bull, S., & Kay, J. (2016). SMILI: A framework for interfaces to learning data in open learner models, learning analytics and related fields. *International Journal of Artificial Intelligence in Education*, 26(1), 293-331.

### Deep Learning for Education

- Piech, C., Bassen, J., Huang, J., Ganguli, S., Sahami, M., Guibas, L. J., & Sohl-Dickstein, J. (2015). Deep knowledge tracing. In *Advances in Neural Information Processing Systems* (pp. 505-513).
- Liu, Q., Huang, Z., Yin, Y., Chen, E., Xiong, H., Su, Y., & Hu, G. (2019). EKT: Exercise-aware knowledge tracing for student performance prediction. *IEEE Transactions on Knowledge and Data Engineering*, 33(1), 100-115.

### Pedagogical Theories

- Bloom, B. S. (1968). Learning for mastery. *Evaluation Comment*, 1(2), 1-12.
- Bloom, B. S., Engelhart, M. D., Furst, E. J., Hill, W. H., & Krathwohl, D. R. (1956). *Taxonomy of educational objectives: The classification of educational goals*. Handbook 1: Cognitive domain. David McKay.
- Piaget, J. (1970). *Science of education and the psychology of the child*. Viking Press.

### Explainable AI

- Arrieta, A. B., Díaz-Rodríguez, N., Del Ser, J., Bennetot, A., Tabik, S., Barbado, A., ... & Herrera, F. (2020). Explainable Artificial Intelligence (XAI): Concepts, taxonomies, opportunities and challenges toward responsible AI. *Information Fusion*, 58, 82-115.
- Conati, C., Barral, O., Putnam, V., & Rieger, L. (2021). Toward personalized XAI: A case study in intelligent tutoring systems. *Artificial Intelligence*, 298, 103503.
- Khosravi, H., Shum, S. B., Chen, G., Conati, C., Tsai, Y. S., Kay, J., ... & Gašević, D. (2022). Explainable artificial intelligence in education. *Computers and Education: Artificial Intelligence*, 3, 100074.

### LLMs in Education

- OpenAI. (2023). GPT-4 Technical Report. *arXiv preprint arXiv:2303.08774*.
- Kasneci, E., Seßler, K., Küchemann, S., Bannert, M., Dementieva, D., Fischer, F., ... & Kasneci, G. (2023). ChatGPT for good? On opportunities and challenges of large language models for education. *Learning and Individual Differences*, 103, 102274.
- Denny, P., Kumar, V., & Giacaman, N. (2023). Conversing with Copilot: Exploring prompt engineering for solving CS1 problems using natural language. In *Proceedings of the 54th ACM Technical Symposium on Computer Science Education* (pp. 1136-1142).

### Data Science Education

- Adhikari, A., DeNero, J., & Jordan, M. I. (2021). Interleaving computational and inferential thinking: Data science for undergraduates at Berkeley. *Harvard Data Science Review*, 3(2).
- Anderson, P., Bowring, J., McCauley, R., Pothering, G., & Starr, C. (2014). An undergraduate degree in data science: Curriculum and a decade of implementation experience. In *Proceedings of the 45th ACM Technical Symposium on Computer Science Education* (pp. 145-150).

### Assessment and Evaluation

- Pavlik Jr, P. I., Cen, H., & Koedinger, K. R. (2009). Performance Factors Analysis--A new alternative to knowledge tracing. In *Proceedings of the 2009 Conference on Artificial Intelligence in Education* (pp. 531-538).
- Lord, F. M. (1980). *Applications of item response theory to practical testing problems*. Routledge.
- Baker, R. S., & Inventado, P. S. (2014). Educational data mining and learning analytics. In *Learning Analytics* (pp. 61-75). Springer.

### Metacognition and Self-Regulated Learning

- Schraw, G., & Dennison, R. S. (1994). Assessing metacognitive awareness. *Contemporary Educational Psychology*, 19(4), 460-475.
- Zimmerman, B. J. (2002). Becoming a self-regulated learner: An overview. *Theory Into Practice*, 41(2), 64-70.
- Azevedo, R., & Aleven, V. (Eds.). (2013). *International handbook of metacognition and learning technologies*. Springer.

---

## Özet ve Sonuç

KAŞİF-AI projesi, **Bayesian Knowledge Tracing** ve **Zone of Proximal Development** teorilerini birleştirerek veri bilimi eğitimi için adaptif bir öğrenme sistemi geliştirmeyi hedeflemektedir. 

**Temel Katkılar:**
1. BKT ve ZPD'nin entegre kullanımı
2. Micro-level adaptasyon (soru bazında)
3. Explainable AI ile şeffaflık
4. Türkçe veri bilimi eğitimi
5. 5 motor mimarisi (Profil, Durum, İçerik, Yönlendirme, Açıklama)

**Literatürdeki Konumu:**
- ITS geleneğini sürdürür (Cognitive Tutors, ASSISTments)
- Modern AI tekniklerine açık (LLM hazır altyapı)
- Pedagojik teorilere sadık (Vygotsky, Bloom)
- Araştırma boşluklarını doldurur (BKT+ZPD, veri bilimi, Türkçe, XAI)

**Gelecek Potansiyeli:**
Sistem, GPT-4 gibi LLM entegrasyonu, affective computing, ve uzun vadeli değerlendirme çalışmalarıyla geliştirilebilir.

---

*Son Güncelleme: 12 Ocak 2026*  
*Yazar: Habip Elis*  
*Proje: KAŞİF-AI - Adaptif Yapay Zeka Destekli Öğrenme Sistemi*
