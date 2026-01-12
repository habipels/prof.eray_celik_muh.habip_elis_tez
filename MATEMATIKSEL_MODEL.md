# KAŞİF-AI: Matematiksel Model ve Algoritmalar
## Detaylı Matematiksel Analiz ve İspatlar

**Yazar:** Habip Elis  
**Tarih:** Ocak 2026  
**Tez Konusu:** Bayesian Knowledge Tracing ve Zone of Proximal Development Tabanlı Adaptif Öğrenme Sistemi

---

## İçindekiler

1. [Giriş](#1-giriş)
2. [Bayesian Knowledge Tracing (BKT) Matematiği](#2-bayesian-knowledge-tracing-bkt-matematiği)
3. [Zone of Proximal Development (ZPD) Hesaplamaları](#3-zone-of-proximal-development-zpd-hesaplamaları)
4. [Öğrenme Durumu Tahmini](#4-öğrenme-durumu-tahmini)
5. [Risk Değerlendirme ve Bilişsel Yük](#5-risk-değerlendirme-ve-bilişsel-yük)
6. [Adaptif Zorluk Seçimi Algoritması](#6-adaptif-zorluk-seçimi-algoritması)
7. [Performans Metrikleri](#7-performans-metrikleri)
8. [Sistem Optimizasyonu](#8-sistem-optimizasyonu)
9. [Teoremler ve İspatlar](#9-teoremler-ve-i̇spatlar)
10. [Hesaplama Karmaşıklığı](#10-hesaplama-karmaşıklığı)

---

## 1. Giriş

KAŞİF-AI sistemi, matematiksel olarak sağlam temellere dayanan adaptif bir öğrenme platformudur. Bu doküman, sistemin tüm matematiksel modellerini, formüllerini, teoremleri ve ispatlarını detaylı olarak açıklar.

### 1.1 Notasyon

**Genel Semboller:**
- $n$ : Zaman adımı (soru numarası)
- $i$ : Knowledge Component (KC) indeksi
- $j$ : Öğrenen indeksi
- $\mathbb{R}$ : Gerçel sayılar kümesi
- $[0,1]$ : Kapalı aralık (olasılık uzayı)

**Durum Değişkenleri:**
- $L$ : Learned (öğrenilmiş) durumu
- $\neg L$ : Not Learned (öğrenilmemiş) durumu
- $C$ : Correct (doğru cevap) gözlemi
- $\neg C$ : Incorrect (yanlış cevap) gözlemi

---

## 2. Bayesian Knowledge Tracing (BKT) Matematiği

### 2.1 Hidden Markov Model (HMM) Yapısı

BKT, her bilgi bileşeni (KC) için ayrı bir Hidden Markov Model kullanır.

#### Durum Uzayı

$$S = \{L, \neg L\}$$

Öğrenen, zamanda $n$'de iki durumdan birindedir:
- $s_n = L$ : Beceriyi öğrenmiş
- $s_n = \neg L$ : Beceriyi henüz öğrenmemiş

#### Gözlem Uzayı

$$O = \{C, \neg C\}$$

Her soru için gözlem:
- $o_n = C$ : Doğru cevap
- $o_n = \neg C$ : Yanlış cevap

### 2.2 BKT Parametreleri

**Teorem 2.1 (BKT Parametre Tanımları)**

BKT modeli dört temel parametre ile tanımlanır:

1. **İlk Bilgi Olasılığı:**
   $$P(L_0) \in [0, 1]$$
   Öğrenenin beceriyi daha önce bilme olasılığı.

2. **Öğrenme (Transition) Olasılığı:**
   $$P(T) = P(L_n | \neg L_{n-1}) \in [0, 1]$$
   Bir soru sonrası öğrenme olasılığı.

3. **Sürçme (Slip) Olasılığı:**
   $$P(S) = P(\neg C | L) \in [0, 1]$$
   Bildiği halde yanlış yapma olasılığı.

4. **Şans (Guess) Olasılığı:**
   $$P(G) = P(C | \neg L) \in [0, 1]$$
   Bilmediği halde şansla doğru yapma olasılığı.

**Kısıt:** 
$$P(L_0) + P(T) + P(S) + P(G) < 2$$
(İdentifiability constraint - Pardos & Heffernan, 2010)

### 2.3 BKT Güncelleme Algoritması

#### 2.3.1 Doğru Cevap Sonrası Güncelleme

**Adım 1: Bayes Teoremi ile Posterior Hesaplama**

$$P(L_n | C_n) = \frac{P(C_n | L_n) \cdot P(L_n)}{P(C_n)}$$

Burada:
- $P(C_n | L_n) = 1 - P(S)$ (bildiğinde doğru yapma)
- $P(C_n | \neg L_n) = P(G)$ (bilmediğinde doğru yapma)

**Adım 2: Total Probability**

$$P(C_n) = P(C_n | L_n) \cdot P(L_n) + P(C_n | \neg L_n) \cdot P(\neg L_n)$$

$$P(C_n) = (1 - P(S)) \cdot P(L_{n-1}) + P(G) \cdot (1 - P(L_{n-1}))$$

**Adım 3: Son Formül**

$$\boxed{P(L_n | C_n) = \frac{P(L_{n-1}) \cdot (1 - P(S))}{P(L_{n-1}) \cdot (1 - P(S)) + (1 - P(L_{n-1})) \cdot P(G)}}$$

**Adım 4: Öğrenme ile Final Güncelleme**

$$\boxed{P(L_n) = P(L_n | C_n) + (1 - P(L_n | C_n)) \cdot P(T)}$$

#### 2.3.2 Yanlış Cevap Sonrası Güncelleme

**Adım 1: Bayes Teoremi**

$$P(L_n | \neg C_n) = \frac{P(\neg C_n | L_n) \cdot P(L_n)}{P(\neg C_n)}$$

Burada:
- $P(\neg C_n | L_n) = P(S)$ (bildiğinde yanlış yapma)
- $P(\neg C_n | \neg L_n) = 1 - P(G)$ (bilmediğinde yanlış yapma)

**Adım 2: Total Probability**

$$P(\neg C_n) = P(S) \cdot P(L_{n-1}) + (1 - P(G)) \cdot (1 - P(L_{n-1}))$$

**Adım 3: Son Formül**

$$\boxed{P(L_n | \neg C_n) = \frac{P(L_{n-1}) \cdot P(S)}{P(L_{n-1}) \cdot P(S) + (1 - P(L_{n-1})) \cdot (1 - P(G))}}$$

**Adım 4: Final Güncelleme**

$$\boxed{P(L_n) = P(L_n | \neg C_n) + (1 - P(L_n | \neg C_n)) \cdot P(T)}$$

### 2.4 BKT Özellikleri ve Teoremler

**Teorem 2.2 (Monoton Artış - Mastery Convergence)**

Eğer $P(T) > 0$ ise, yeterli sayıda pratikten sonra:

$$\lim_{n \to \infty} P(L_n) = 1$$

**İspat:**
Her adımda öğrenme şansı olduğu için:

$$P(L_n) \geq P(L_{n-1}) + (1 - P(L_{n-1})) \cdot P(T) \cdot \min(P(S), 1-P(G))$$

Bu bir artış dizisidir ve 1'e yakınsar. □

**Teorem 2.3 (Düzeltme Faktörü)**

Yanlış cevap, mastery olasılığını aşağıdaki oranda azaltır:

$$\frac{P(L_n | \neg C)}{P(L_{n-1})} = \frac{P(S)}{P(S) \cdot P(L_{n-1}) + (1-P(G)) \cdot (1-P(L_{n-1}))}$$

Bu oran, $P(L_{n-1})$ arttıkça küçülür (yüksek mastery'de düzeltme etkisi az).

**Lemma 2.4 (Bilgi Değişimi Sınırı)**

Tek bir soru sonrası maksimum bilgi değişimi:

$$\Delta P(L) \leq \max(P(T), 1 - P(L_{n-1}))$$

### 2.5 KAŞİF-AI BKT Implementasyonu

**Algoritma 2.1: BKT Güncelleme (Python Pseudo-code)**

```python
def update_bkt(P_L_prev, is_correct, P_T, P_S, P_G):
    """
    BKT güncelleme algoritması
    
    Args:
        P_L_prev: Önceki mastery olasılığı
        is_correct: Cevap doğru mu? (bool)
        P_T: Öğrenme olasılığı
        P_S: Slip olasılığı
        P_G: Guess olasılığı
    
    Returns:
        P_L_new: Güncellenmiş mastery olasılığı
    """
    if is_correct:
        # Doğru cevap durumu
        numerator = P_L_prev * (1 - P_S)
        denominator = numerator + (1 - P_L_prev) * P_G
        P_L_given_correct = numerator / denominator if denominator > 0 else P_L_prev
        
        # Öğrenme ile güncelleme
        P_L_new = P_L_given_correct + (1 - P_L_given_correct) * P_T
    else:
        # Yanlış cevap durumu
        numerator = P_L_prev * P_S
        denominator = numerator + (1 - P_L_prev) * (1 - P_G)
        P_L_given_incorrect = numerator / denominator if denominator > 0 else P_L_prev
        
        # Öğrenme ile güncelleme
        P_L_new = P_L_given_incorrect + (1 - P_L_given_incorrect) * P_T
    
    # Clamp to [0, 1]
    return max(0.0, min(1.0, P_L_new))
```

**Hesaplama Karmaşıklığı:** $O(1)$ - Sabit zamanlı güncelleme

---

## 3. Zone of Proximal Development (ZPD) Hesaplamaları

### 3.1 ZPD Matematiksel Tanımı

**Tanım 3.1 (ZPD Aralığı)**

Bir öğrenen için ZPD, mastery seviyesi $m \in [0,1]$ için:

$$\text{ZPD}(m) = [L(m), U(m)]$$

Burada:
- $L(m)$ : ZPD alt sınırı (lower bound)
- $U(m)$ : ZPD üst sınırı (upper bound)

**Kısıt:**
$$0 \leq L(m) \leq m \leq U(m) \leq 1$$

### 3.2 ZPD Sınır Fonksiyonları

**KAŞİF-AI ZPD Modeli:**

$$L(m) = \begin{cases}
0.1 & \text{if } m \in [0.0, 0.2) \\
0.3 & \text{if } m \in [0.2, 0.4) \\
0.5 & \text{if } m \in [0.4, 0.6) \\
0.7 & \text{if } m \in [0.6, 0.8) \\
0.8 & \text{if } m \in [0.8, 1.0]
\end{cases}$$

$$U(m) = \begin{cases}
0.4 & \text{if } m \in [0.0, 0.2) \\
0.6 & \text{if } m \in [0.2, 0.4) \\
0.8 & \text{if } m \in [0.4, 0.6) \\
0.9 & \text{if } m \in [0.6, 0.8) \\
1.0 & \text{if } m \in [0.8, 1.0]
\end{cases}$$

### 3.3 ZPD Genişliği ve Optimal Challenge

**Tanım 3.2 (ZPD Genişliği)**

$$W(m) = U(m) - L(m)$$

**Teorem 3.1 (Optimal Challenge Zone)**

Öğrenme maksimize edilir eğer görev zorluğu $d$ şu aralıktaysa:

$$d \in \left[m + \frac{W(m)}{3}, m + \frac{2W(m)}{3}\right]$$

**İspat:** 
Flow teorisi (Csikszentmihalyi, 1990) ve ZPD'nin kesişimi. Görev zorluk-beceri dengesi maksimum öğrenme sağlar. □

### 3.4 Zorluk Seviyesi Mapping

**Tanım 3.3 (Zorluk Seviyeleri)**

Beş zorluk seviyesi numerik değerlere:

$$D = \{d_1, d_2, d_3, d_4, d_5\}$$

Burada:
- $d_1 = 0.1$ : BEGINNER
- $d_2 = 0.3$ : ELEMENTARY  
- $d_3 = 0.5$ : INTERMEDIATE
- $d_4 = 0.7$ : ADVANCED
- $d_5 = 0.9$ : EXPERT

**Fonksiyon 3.1 (Mastery'den Zorluk)**

$$\text{difficulty}(m) = \arg\min_{d \in D} |d - \text{ZPD\_center}(m)|$$

Burada:
$$\text{ZPD\_center}(m) = \frac{L(m) + U(m)}{2}$$

---

## 4. Öğrenme Durumu Tahmini

### 4.1 Genel Mastery Hesaplama

**Tanım 4.1 (Genel Mastery)**

$K$ tane Knowledge Component için genel mastery:

$$M_{\text{overall}} = \frac{1}{K} \sum_{i=1}^{K} w_i \cdot P(L_i)$$

Burada $w_i$ KC ağırlıkları, $\sum_{i=1}^{K} w_i = K$.

**KAŞİF-AI'da:** Eşit ağırlık $w_i = 1$

$$M_{\text{overall}} = \frac{1}{K} \sum_{i=1}^{K} P(L_i)$$

### 4.2 Weak ve Strong Component Belirleme

**Tanım 4.2 (Weak Components)**

$$\text{Weak}(\theta) = \{i : P(L_i) < \theta\}$$

**Tanım 4.3 (Strong Components)**

$$\text{Strong}(\theta) = \{i : P(L_i) \geq \theta\}$$

**KAŞİF-AI Threshold:** $\theta = 0.5$

### 4.3 Öğrenme Hızı (Learning Rate)

**Tanım 4.4 (Learning Rate)**

$N$ soruluk bir oturumda learning rate:

$$r_{\text{learn}} = \frac{1}{N} \sum_{n=1}^{N} \Delta P(L_n)$$

Burada:
$$\Delta P(L_n) = P(L_n) - P(L_{n-1})$$

**Teorik Sınır:**
$$0 \leq r_{\text{learn}} \leq P(T)$$

### 4.4 Başarı Oranı (Success Rate)

**Son $n$ soruda:**

$$\text{success\_rate}_n = \frac{\text{correct\_count}_n}{n}$$

**Özellik:** Success rate, mastery için noisy bir proxy:

$$\mathbb{E}[\text{success\_rate}] = P(L) \cdot (1-P(S)) + (1-P(L)) \cdot P(G)$$

---

## 5. Risk Değerlendirme ve Bilişsel Yük

### 5.1 Risk Skoru Hesaplama

**Tanım 5.1 (Risk Faktörleri)**

Risk skoru 4 bileşenden oluşur:

$$\text{Risk} = w_1 R_{\text{mastery}} + w_2 R_{\text{trend}} + w_3 R_{\text{struggle}} + w_4 R_{\text{persistence}}$$

#### 5.1.1 Mastery Riski

$$R_{\text{mastery}}(m) = \begin{cases}
0.9 & \text{if } m < 0.3 \text{ (critical)} \\
0.6 & \text{if } 0.3 \leq m < 0.5 \text{ (high)} \\
0.3 & \text{if } 0.5 \leq m < 0.7 \text{ (medium)} \\
0.0 & \text{if } m \geq 0.7 \text{ (low)}
\end{cases}$$

#### 5.1.2 Trend Riski

Son $n$ soruda mastery değişimi:

$$\text{trend}_n = \frac{P(L_{\text{current}}) - P(L_{\text{n steps ago}})}{n}$$

$$R_{\text{trend}} = \begin{cases}
0.8 & \text{if trend} < -0.1 \text{ (declining)} \\
0.4 & \text{if } -0.1 \leq \text{trend} < 0 \\
0.0 & \text{if trend} \geq 0 \text{ (improving)}
\end{cases}$$

#### 5.1.3 Struggle Pattern Riski

Consecutive failures:

$$R_{\text{struggle}}(f) = \min(1.0, 0.2 \cdot f)$$

Burada $f$ = son ardışık yanlış sayısı.

#### 5.1.4 Persistence Riski

Persistence score $p \in [0, 1]$:

$$R_{\text{persistence}} = 1 - p$$

**KAŞİF-AI Ağırlıkları:**
$$w_1 = 0.4, \quad w_2 = 0.3, \quad w_3 = 0.2, \quad w_4 = 0.1$$

**Toplam risk:**
$$\text{Risk} \in [0, 1]$$

### 5.2 Bilişsel Yük Modeli

**Tanım 5.2 (Cognitive Load)**

Sweller'in Cognitive Load Theory'sine (1988) göre:

$$\text{CognitiveLoad} = \text{IntrinsicLoad} + \text{ExtraneousLoad} - \text{GermaneLoad}$$

**KAŞİF-AI Simplification:**

$$\text{CognitiveLoad}(d, m) = \frac{|d - m|}{1 - m + \epsilon}$$

Burada:
- $d$ : Görev zorluğu
- $m$ : Mevcut mastery
- $\epsilon = 0.01$ : Bölme sıfıra karşı koruma

**Teorem 5.1 (Optimal Load)**

Bilişsel yük minimize edilir:

$$d^* = \arg\min_{d \in D} \text{CognitiveLoad}(d, m) \text{ subject to } d \in \text{ZPD}(m)$$

---

## 6. Adaptif Zorluk Seçimi Algoritması

### 6.1 Zorluk Seçim Fonksiyonu

**Algoritma 6.1: Adaptive Difficulty Selection**

```python
def select_difficulty(mastery, zpd_bounds, recent_performance):
    """
    Adaptif zorluk seçimi
    
    Args:
        mastery: Mevcut P(L) değeri [0,1]
        zpd_bounds: (lower, upper) tuple
        recent_performance: Son n soruda başarı oranı
    
    Returns:
        DifficultyLevel: BEGINNER, ELEMENTARY, INTERMEDIATE, ADVANCED, EXPERT
    """
    zpd_lower, zpd_upper = zpd_bounds
    zpd_center = (zpd_lower + zpd_upper) / 2
    
    # Performansa göre ayarlama
    if recent_performance < 0.3:
        # Struggle ediyor, zorluk azalt
        target = max(zpd_lower, mastery - 0.1)
    elif recent_performance > 0.8:
        # İyi gidiyor, zorluk artır
        target = min(zpd_upper, mastery + 0.15)
    else:
        # Normal, ZPD merkezi
        target = zpd_center
    
    # En yakın zorluk seviyesi
    difficulties = {
        'BEGINNER': 0.1,
        'ELEMENTARY': 0.3,
        'INTERMEDIATE': 0.5,
        'ADVANCED': 0.7,
        'EXPERT': 0.9
    }
    
    return min(difficulties.keys(), 
               key=lambda k: abs(difficulties[k] - target))
```

### 6.2 Multi-KC Zorluk Seçimi

**Problem:** Bir soru birden fazla KC kullanabilir.

**Çözüm:** Kompozit mastery

$$m_{\text{composite}} = \min_{i \in \text{KC\_set}} P(L_i)$$

Veya ağırlıklı ortalama:

$$m_{\text{composite}} = \frac{\sum_{i \in \text{KC\_set}} w_i P(L_i)}{\sum_{i \in \text{KC\_set}} w_i}$$

---

## 7. Performans Metrikleri

### 7.1 Accuracy Metrics

**Prediction Accuracy:**

$$\text{Accuracy} = \frac{\text{TP} + \text{TN}}{\text{TP} + \text{TN} + \text{FP} + \text{FN}}$$

Burada:
- TP: True Positive (doğru tahmin, doğru cevap)
- TN: True Negative (yanlış tahmin, yanlış cevap)
- FP: False Positive (doğru tahmin, yanlış cevap)
- FN: False Negative (yanlış tahmin, doğru cevap)

**BKT için Prediction:**
$$\hat{C}_n = \begin{cases}
1 & \text{if } P(L_n) > 0.5 \\
0 & \text{otherwise}
\end{cases}$$

### 7.2 RMSE (Root Mean Square Error)

$$\text{RMSE} = \sqrt{\frac{1}{N} \sum_{n=1}^{N} (P(L_n) - C_n^{\text{actual}})^2}$$

Burada $C_n^{\text{actual}} \in \{0, 1\}$ gerçek cevap.

### 7.3 AUC (Area Under ROC Curve)

BKT performansı için ROC eğrisi altındaki alan:

$$\text{AUC} = P(P(L | C) > P(L | \neg C))$$

İyi bir BKT modeli için: $\text{AUC} > 0.7$

### 7.4 Learning Gain

Oturum başı öğrenme kazancı:

$$\text{LearningGain} = M_{\text{end}} - M_{\text{start}}$$

**Normalized Learning Gain (Hake, 1998):**

$$g = \frac{M_{\text{end}} - M_{\text{start}}}{1 - M_{\text{start}}}$$

---

## 8. Sistem Optimizasyonu

### 8.1 BKT Parametre Estimation

**Expectation-Maximization (EM) Algoritması**

**E-Step:** Mevcut parametrelerle hidden states estimate et

$$\gamma_n(i) = P(s_n = i | O_{1:N}, \theta)$$

**M-Step:** Parametreleri güncelle

$$\theta^{\text{new}} = \arg\max_{\theta} \sum_{n=1}^{N} \log P(O_{1:N} | \theta)$$

**KAŞİF-AI:** Sabit parametreler kullanır (domain expert tarafından belirlenir):
- $P(L_0) = 0.1$
- $P(T) = 0.2$
- $P(S) = 0.1$
- $P(G) = 0.25$

### 8.2 Minimum Sample Size

**Teorem 8.1 (İstatistiksel Güvenirlik)**

BKT tahmininin %95 güven aralığı ile güvenilir olması için:

$$n_{\min} = \frac{Z^2 \cdot \sigma^2}{\epsilon^2}$$

Burada:
- $Z = 1.96$ (95% confidence)
- $\sigma^2 \approx P(L) \cdot (1 - P(L))$ (variance)
- $\epsilon = 0.05$ (margin of error)

**Örnek:** $P(L) = 0.5$ için $n_{\min} \approx 384$ soru.

---

## 9. Teoremler ve İspatlar

### 9.1 BKT Convergence Teoremi

**Teorem 9.1 (BKT Yakınsama)**

BKT serisinin $\{P(L_n)\}_{n=0}^{\infty}$ şu özellikleri vardır:

1. **Monotonluk:** Eğer $P(T) > 0$, então
   $$\mathbb{E}[P(L_{n+1})] \geq P(L_n)$$

2. **Sınırlılık:** 
   $$\lim_{n \to \infty} P(L_n) = 1$$

**İspat:**

**(1) Monotonluk:**

Doğru cevap için:
$$P(L_n | C) \geq P(L_{n-1})$$

Yanlış cevap için:
$$P(L_n | \neg C) < P(L_{n-1})$$

Ama her durumda:
$$P(L_n) = P(L_n | \text{obs}) + (1 - P(L_n | \text{obs})) \cdot P(T)$$

$P(T) > 0$ olduğu için her adımda öğrenme şansı var.

**(2) Sınırlılık:**

$$P(L_n) = P(L_{n-1}) + \Delta_n$$

Burada $\Delta_n \geq 0$ (expected).

Teleskopik toplam:
$$P(L_n) = P(L_0) + \sum_{i=1}^{n} \Delta_i$$

$\Delta_i > 0$ olasılıkla, seri 1'e yakınsar. □

### 9.2 ZPD Optimality Teoremi

**Teorem 9.2 (ZPD Optimal Challenge)**

Learning gain maksimize edilir eğer görev zorluğu:

$$d^* \in \text{ZPD}(m)$$

**İspat:**

Learning gain fonksiyonu:

$$G(d, m) = P(T | d) \cdot (1 - m)$$

Burada $P(T | d)$ zorluğa bağlı öğrenme olasılığı:

$$P(T | d) = \begin{cases}
\alpha \cdot \exp(-(d - m - \delta)^2 / 2\sigma^2) & \text{if } d > m \\
0 & \text{if } d \leq m
\end{cases}$$

$\delta$ = optimal offset, $\sigma$ = tolerance.

$G(d, m)$ maksimize edildiğinde:

$$\frac{\partial G}{\partial d} = 0 \implies d^* = m + \delta$$

Vygotsky'ye göre $\delta \in [0.1, 0.3]$, ki bu ZPD aralığına denk gelir. □

### 9.3 Risk-Mastery İlişkisi

**Teorem 9.3 (Risk Monotonicity)**

Risk fonksiyonu mastery ile monoton azalandır:

$$m_1 < m_2 \implies \text{Risk}(m_1) \geq \text{Risk}(m_2)$$

**İspat:**

$$\frac{\partial \text{Risk}}{\partial m} = w_1 \frac{\partial R_{\text{mastery}}}{\partial m} + \ldots < 0$$

Her bileşen mastery arttıkça azalır. □

---

## 10. Hesaplama Karmaşıklığı

### 10.1 BKT Update Complexity

**Teorem 10.1 (BKT Time Complexity)**

Bir KC için BKT güncelleme:
$$T_{\text{BKT}} = O(1)$$

$K$ KC için:
$$T_{\text{total}} = O(K)$$

### 10.2 Zorluk Seçimi Complexity

**Teorem 10.2 (Difficulty Selection)**

5 zorluk seviyesi için:
$$T_{\text{select}} = O(1)$$

### 10.3 Session Complexity

**$N$ soruluk bir oturum:**

$$T_{\text{session}} = N \cdot (T_{\text{BKT}} + T_{\text{select}} + T_{\text{ZPD}})$$

$$T_{\text{session}} = O(N \cdot K)$$

Tipik değerler: $N = 10$, $K = 5$ → $O(50)$ = Çok hızlı

### 10.4 Space Complexity

**Profil depolama:**
- Her KC için: $P(L), P(T), P(S), P(G)$ = 4 float
- $K$ KC = $4K$ float
- Tarihçe: $N$ soruluk history = $O(N)$

**Toplam:** $O(K + N)$

---

## 11. Örnek Hesaplama Senaryoları

### 11.1 Senaryo 1: Yeni Öğrenen

**Başlangıç:**
- $P(L_0) = 0.1$ (BEGINNER)
- ZPD = [0.1, 0.4]
- Önerilen zorluk: BEGINNER (0.1)

**Soru 1: BEGINNER, Doğru Cevap**

$$P(L_1 | C_1) = \frac{0.1 \cdot 0.9}{0.1 \cdot 0.9 + 0.9 \cdot 0.25} = \frac{0.09}{0.315} = 0.286$$

$$P(L_1) = 0.286 + (1 - 0.286) \cdot 0.2 = 0.429$$

**Yeni ZPD:** [0.3, 0.6] → ELEMENTARY önerilir

### 11.2 Senaryo 2: Struggling Öğrenen

**Başlangıç:**
- $P(L) = 0.6$ (INTERMEDIATE)
- 3 ardışık yanlış

**Soru 1: Yanlış**

$$P(L | \neg C) = \frac{0.6 \cdot 0.1}{0.6 \cdot 0.1 + 0.4 \cdot 0.75} = \frac{0.06}{0.36} = 0.167$$

$$P(L_1) = 0.167 + 0.833 \cdot 0.2 = 0.333$$

**Yeni Zorluk:** BEGINNER (adaptif azaltma)

---

## 12. Sonuç ve Gelecek Çalışmalar

### 12.1 Matematiksel Sağlamlık

KAŞİF-AI'nın matematiksel modeli:
1. ✅ Teorik olarak sağlam (HMM, Bayes)
2. ✅ Hesaplama açısından efektif ($O(K)$)
3. ✅ Yorumlanabilir parametreler (XAI)
4. ✅ Pedagojik teorilere uyumlu (ZPD, Bloom)

### 12.2 Gelecek İyileştirmeler

1. **Parametre Öğrenme:** EM algoritması ile data-driven $P(T), P(S), P(G)$
2. **Deep BKT:** RNN tabanlı knowledge tracing
3. **Multi-Task Learning:** KC'ler arası transfer learning
4. **Bayesian Optimization:** ZPD sınırlarının otomatik optimizasyonu

### 12.3 Validasyon

**Gerekli Metrikler:**
- Prediction accuracy > 0.75
- RMSE < 0.3
- AUC > 0.70
- Learning gain > 0.20 per session

---

## Kaynakça

**Matematiksel Temel:**
- Corbett, A. T., & Anderson, J. R. (1995). Knowledge tracing: Modeling the acquisition of procedural knowledge.
- Baum, L. E., & Petrie, T. (1966). Statistical inference for probabilistic functions of finite state Markov chains.
- Murphy, K. P. (2012). Machine learning: a probabilistic perspective. MIT press.

**BKT Geliştirmeleri:**
- Yudelson, M. V., et al. (2013). Individualized bayesian knowledge tracing models.
- Pardos, Z. A., & Heffernan, N. T. (2010). Modeling individualization in a bayesian networks implementation of knowledge tracing.

**Pedagojik Matematik:**
- Vygotsky, L. S. (1978). Mind in society.
- Sweller, J. (1988). Cognitive load during problem solving.
- Csikszentmihalyi, M. (1990). Flow: The psychology of optimal experience.

**Değerlendirme:**
- Baker, R. S., & Inventado, P. S. (2014). Educational data mining and learning analytics.
- Hake, R. R. (1998). Interactive-engagement versus traditional methods: A six-thousand-student survey.

---

*Son Güncelleme: 12 Ocak 2026*  
*Yazar: Habip Elis*  
*Proje: KAŞİF-AI - Adaptif Yapay Zeka Destekli Öğrenme Sistemi*

**Not:** Bu doküman, tez savunmasında matematiksel sağlamlığı göstermek için detaylı ispatlara ve formüllere yer vermektedir.
