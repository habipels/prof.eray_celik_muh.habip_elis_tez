# KAŞİF-AI: Akademik Tez İçin Teknik Açıklama ve Pseudocode

Bu doküman, akademik tez yazımında kullanılmak üzere sistem bileşenlerinin pseudocode ve algoritmik açıklamalarını içerir.

---

## 1. ÖĞRENEN PROFİLİ MOTORU

### 1.1 Bayesian Knowledge Tracing (BKT) Algoritması

#### Pseudocode:

```
FUNCTION UpdateKnowledgeComponent(kc, observation):
    INPUT:
        kc: KnowledgeComponent  // Güncellenecek bilgi bileşeni
        observation: Boolean     // true = doğru cevap, false = yanlış
    
    OUTPUT:
        updated_kc: KnowledgeComponent
    
    // Başlangıç parametreleri
    P_L0 ← kc.mastery_level           // Önceki öğrenme olasılığı
    P_T ← 0.1                          // Transfer (öğrenme) oranı
    P_S ← 0.1                          // Slip (bildiği halde hata) oranı
    P_G ← 0.2                          // Guess (bilmediği halde doğru) oranı
    
    // Bayes Teoremi ile posterior hesaplama
    IF observation = TRUE THEN
        // P(L | correct) hesaplama
        P_correct_given_learned ← 1 - P_S
        P_correct_given_not_learned ← P_G
        
        numerator ← P_correct_given_learned × P_L0
        denominator ← numerator + P_correct_given_not_learned × (1 - P_L0)
        
        P_L1 ← numerator / denominator
    ELSE
        // P(L | incorrect) hesaplama
        P_incorrect_given_learned ← P_S
        P_incorrect_given_not_learned ← 1 - P_G
        
        numerator ← P_incorrect_given_learned × P_L0
        denominator ← numerator + P_incorrect_given_not_learned × (1 - P_L0)
        
        P_L1 ← numerator / denominator
    ENDIF
    
    // Transfer olasılığı ile final güncelleme
    P_L_final ← P_L1 + (1 - P_L1) × P_T
    
    kc.mastery_level ← min(P_L_final, 1.0)
    
    RETURN kc
END FUNCTION
```

#### Matematiksel Notasyon:

$$
P(L_t | obs) = \frac{P(obs | L_t) \cdot P(L_t)}{P(obs)}
$$

$$
P(L_{t+1}) = P(L_t | obs) + (1 - P(L_t | obs)) \cdot P(T)
$$

Burada:
- $L_t$: t anında bilgi bileşeninin öğrenilmiş olma durumu
- $P(T)$: Transfer (öğrenme geçişi) olasılığı
- $P(S)$: Slip (hata kayması) olasılığı
- $P(G)$: Guess (tahmin) olasılığı

---

### 1.2 Öğrenme Hızı Hesaplama

#### Pseudocode:

```
FUNCTION CalculateLearningRate(profile):
    INPUT:
        profile: LearnerProfile
    
    OUTPUT:
        learning_rate: Float [0, 1]
    
    IF LENGTH(profile.performance_history) < 2 THEN
        RETURN 0.5  // Varsayılan
    ENDIF
    
    recent_records ← LAST_N(profile.performance_history, 10)
    recent_success ← COUNT(r IN recent_records WHERE r.is_correct) / LENGTH(recent_records)
    
    IF LENGTH(profile.performance_history) > 20 THEN
        early_records ← FIRST_N(profile.performance_history, 10)
        early_success ← COUNT(r IN early_records WHERE r.is_correct) / LENGTH(early_records)
        
        improvement ← recent_success - early_success
        learning_rate ← 0.5 + improvement
        
        RETURN CLAMP(learning_rate, 0, 1)
    ELSE
        RETURN recent_success
    ENDIF
END FUNCTION
```

#### Matematiksel Formül:

$$
LR = 0.5 + \frac{\sum_{i=N-9}^{N} correct_i}{10} - \frac{\sum_{i=0}^{9} correct_i}{10}
$$

---

## 2. ÖĞRENME DURUMU TAHMİNLEYİCİ

### 2.1 Risk Değerlendirme Algoritması

#### Pseudocode:

```
FUNCTION AssessRisk(profile):
    INPUT:
        profile: LearnerProfile
    
    OUTPUT:
        risk: RiskAssessment
    
    recent_records ← LAST_N(profile.performance_history, 10)
    
    // Risk faktörlerini hesapla
    consecutive_failures ← CountConsecutiveFailures(recent_records)
    declining_performance ← IsPerformanceDeclining(profile)
    low_engagement ← IsEngagementLow(profile)
    excessive_time ← IsTimeSpentExcessive(profile)
    help_dependency ← (profile.help_seeking_tendency > 0.7)
    
    // Risk skoru hesaplama (ağırlıklı toplam)
    risk_score ← 0.0
    risk_score ← risk_score + consecutive_failures × 0.1
    risk_score ← risk_score + (declining_performance ? 0.2 : 0.0)
    risk_score ← risk_score + (low_engagement ? 0.15 : 0.0)
    risk_score ← risk_score + (excessive_time ? 0.1 : 0.0)
    risk_score ← risk_score + (help_dependency ? 0.15 : 0.0)
    
    risk_score ← min(risk_score, 1.0)
    
    // Risk seviyesi belirleme
    IF risk_score < 0.25 THEN
        risk_level ← LOW
    ELSE IF risk_score < 0.5 THEN
        risk_level ← MODERATE
    ELSE IF risk_score < 0.75 THEN
        risk_level ← HIGH
    ELSE
        risk_level ← CRITICAL
    ENDIF
    
    risk ← NEW RiskAssessment(
        risk_level = risk_level,
        risk_score = risk_score,
        consecutive_failures = consecutive_failures,
        declining_performance = declining_performance,
        low_engagement = low_engagement,
        excessive_time_spent = excessive_time,
        help_dependency = help_dependency
    )
    
    // Müdahale önerileri
    IF risk_level IN {HIGH, CRITICAL} THEN
        risk.intervention_needed ← TRUE
        risk.recommended_actions ← GenerateInterventions(risk)
    ENDIF
    
    RETURN risk
END FUNCTION
```

#### Matematiksel Notasyon:

$$
R_{score} = \sum_{i} w_i \cdot f_i
$$

Burada:
- $f_i$: i-nci risk faktörü (0 veya 1)
- $w_i$: i-nci faktörün ağırlığı

Risk Faktörleri:
$$
\begin{aligned}
f_1 &= \text{consecutive\_failures} \times 0.1 \\
f_2 &= \text{declining\_performance} \times 0.2 \\
f_3 &= \text{low\_engagement} \times 0.15 \\
f_4 &= \text{excessive\_time} \times 0.1 \\
f_5 &= \text{help\_dependency} \times 0.15
\end{aligned}
$$

---

### 2.2 ZPD (Zone of Proximal Development) Uyum Hesaplama

#### Pseudocode:

```
FUNCTION CalculateZPDMatch(success_rate):
    INPUT:
        success_rate: Float [0, 1]  // Son aktivitelerdeki başarı oranı
    
    OUTPUT:
        zpd_match: Float [0, 1]
    
    CONST OPTIMAL_MIN ← 0.6
    CONST OPTIMAL_MAX ← 0.7
    
    IF OPTIMAL_MIN ≤ success_rate ≤ OPTIMAL_MAX THEN
        // Optimal ZPD bölgesi
        RETURN 1.0
    ELSE IF 0.5 ≤ success_rate < OPTIMAL_MIN OR OPTIMAL_MAX < success_rate ≤ 0.8 THEN
        // İyi ama optimal değil
        RETURN 0.8
    ELSE IF 0.4 ≤ success_rate < 0.5 OR 0.8 < success_rate ≤ 0.9 THEN
        // Orta
        RETURN 0.5
    ELSE
        // Zayıf (çok kolay veya çok zor)
        RETURN 0.3
    ENDIF
END FUNCTION
```

#### Matematiksel Formül:

$$
ZPD_{match} = \begin{cases}
1.0, & \text{if } 0.6 \leq SR \leq 0.7 \\
0.8, & \text{if } 0.5 \leq SR < 0.6 \text{ or } 0.7 < SR \leq 0.8 \\
0.5, & \text{if } 0.4 \leq SR < 0.5 \text{ or } 0.8 < SR \leq 0.9 \\
0.3, & \text{otherwise}
\end{cases}
$$

Burada $SR$ = başarı oranı (success rate).

---

### 2.3 Bilişsel Yük Tahmini

#### Pseudocode:

```
FUNCTION EstimateCognitiveLoad(recent_records):
    INPUT:
        recent_records: List<PerformanceRecord>
    
    OUTPUT:
        cognitive_load: Float [0, 1]
    
    IF LENGTH(recent_records) = 0 THEN
        RETURN 0.5  // Varsayılan
    ENDIF
    
    // 1. Hata oranı faktörü
    error_count ← COUNT(r IN recent_records WHERE NOT r.is_correct)
    error_rate ← error_count / LENGTH(recent_records)
    
    // 2. Deneme sayısı faktörü
    avg_attempts ← MEAN(r.attempts_count FOR r IN recent_records)
    attempts_factor ← min(avg_attempts / 5, 1.0)
    
    // 3. Yardım isteme faktörü
    help_count ← COUNT(r IN recent_records WHERE r.help_requested)
    help_factor ← help_count / LENGTH(recent_records)
    
    // 4. Süre faktörü
    avg_time ← MEAN(r.time_spent_seconds FOR r IN recent_records)
    time_factor ← min(avg_time / 600, 1.0)  // 10 dakika üstü = 1.0
    
    // Ağırlıklı toplam
    cognitive_load ← (
        error_rate × 0.3 +
        attempts_factor × 0.2 +
        help_factor × 0.2 +
        time_factor × 0.3
    )
    
    RETURN min(cognitive_load, 1.0)
END FUNCTION
```

#### Matematiksel Notasyon:

$$
CL = 0.3 \cdot \frac{E}{N} + 0.2 \cdot \min\left(\frac{\bar{A}}{5}, 1\right) + 0.2 \cdot \frac{H}{N} + 0.3 \cdot \min\left(\frac{\bar{T}}{600}, 1\right)
$$

Burada:
- $E$: Hata sayısı
- $N$: Toplam aktivite sayısı
- $\bar{A}$: Ortalama deneme sayısı
- $H$: Yardım isteme sayısı
- $\bar{T}$: Ortalama süre (saniye)

---

## 3. UYARLANABILIR YÖNLENDİRME MOTORU

### 3.1 Sonraki Adım Önerisi Algoritması

#### Pseudocode:

```
FUNCTION RecommendNextStep(profile, state, completed_topics, session_duration):
    INPUT:
        profile: LearnerProfile
        state: LearningState
        completed_topics: List<String>
        session_duration: Float (minutes)
    
    OUTPUT:
        recommendation: NextStepRecommendation
    
    // 1. Acil müdahale kontrolü
    intervention ← AssessInterventionNeed(profile, state, session_duration)
    
    IF intervention ≠ NONE THEN
        RETURN CreateInterventionRecommendation(intervention, profile, state)
    ENDIF
    
    // 2. Risk durumu kontrolü
    IF state.risk_assessment.risk_level IN {HIGH, CRITICAL} THEN
        RETURN CreateRemediationRecommendation(profile, state)
    ENDIF
    
    // 3. İlerleme hazırlığı kontrolü
    IF IsReadyForAdvancement(state) THEN
        RETURN RecommendAdvancement(profile, state, completed_topics)
    ENDIF
    
    // 4. Pekiştirme ihtiyacı kontrolü
    IF NeedsRemediation(state) THEN
        RETURN CreateRemediationRecommendation(profile, state)
    ENDIF
    
    // 5. Normal ilerleme
    RETURN RecommendNextTopic(profile, state, completed_topics)
END FUNCTION
```

---

### 3.2 Zorluk Seviyesi Ayarlama

#### Pseudocode:

```
FUNCTION AdjustDifficulty(current_difficulty, state, recent_performance):
    INPUT:
        current_difficulty: DifficultyLevel (1-5)
        state: LearningState
        recent_performance: List<Boolean>
    
    OUTPUT:
        (new_difficulty: DifficultyLevel, reason: String)
    
    IF LENGTH(recent_performance) < 3 THEN
        RETURN (current_difficulty, "Yetersiz veri")
    ENDIF
    
    success_rate ← SUM(recent_performance) / LENGTH(recent_performance)
    zpd_match ← state.zone_of_proximal_development_match
    
    // Çok kolay (>85% başarı ve düşük ZPD)
    IF success_rate > 0.85 AND zpd_match < 0.5 THEN
        IF current_difficulty < 5 THEN
            new_difficulty ← current_difficulty + 1
            RETURN (new_difficulty, "Performans mükemmel, zorluk artırılıyor")
        ENDIF
    
    // Çok zor (<40% başarı)
    ELSE IF success_rate < 0.4 THEN
        IF current_difficulty > 1 THEN
            new_difficulty ← current_difficulty - 1
            RETURN (new_difficulty, "Zorluk düşürülüyor, pekiştirme gerekli")
        ENDIF
    
    // Optimal aralık (60-75%)
    ELSE IF 0.6 ≤ success_rate ≤ 0.75 THEN
        RETURN (current_difficulty, "Zorluk optimal, değişiklik yok")
    
    // Hafif artış (75-85%)
    ELSE IF success_rate > 0.75 THEN
        IF state.risk_assessment.risk_level = LOW THEN
            IF current_difficulty < 5 THEN
                new_difficulty ← current_difficulty + 1
                RETURN (new_difficulty, "Kademeli zorluk artışı")
            ENDIF
        ENDIF
    ENDIF
    
    RETURN (current_difficulty, "Mevcut seviye korunuyor")
END FUNCTION
```

#### Karar Ağacı:

```
                    ┌─────────────────┐
                    │ Başarı Oranı?   │
                    └────────┬────────┘
                             │
          ┌──────────────────┼──────────────────┐
          │                  │                  │
       > 85%              60-75%              < 40%
          │                  │                  │
          ▼                  ▼                  ▼
    ┌──────────┐      ┌──────────┐      ┌──────────┐
    │ ZPD < 0.5│      │ OPTIMAL  │      │ ZORLUK   │
    │    ?     │      │ KORUN    │      │ AZALT    │
    └────┬─────┘      └──────────┘      └──────────┘
         │
      Evet │ Hayır
         │ │
         ▼ ▼
    ARTIR  KORUN
```

---

### 3.3 İpucu Verme Kararı

#### Pseudocode:

```
FUNCTION ShouldProvideHint(attempt_count, time_spent, profile, state):
    INPUT:
        attempt_count: Integer
        time_spent: Float (seconds)
        profile: LearnerProfile
        state: LearningState
    
    OUTPUT:
        (should_hint: Boolean, reason: String)
    
    // 1. Çok fazla deneme
    IF attempt_count ≥ 3 THEN
        RETURN (TRUE, "Üç başarısız denemeden sonra yönlendirme gerekli")
    ENDIF
    
    // 2. Çok uzun süre
    expected_time ← 300  // 5 dakika
    IF time_spent > expected_time × 1.5 THEN
        RETURN (TRUE, "Beklenen sürenin üzerinde zaman harcanıyor")
    ENDIF
    
    // 3. Yüksek risk + 2+ deneme
    IF state.risk_assessment.risk_level IN {HIGH, CRITICAL} THEN
        IF attempt_count ≥ 2 THEN
            RETURN (TRUE, "Risk yüksek, erken destek gerekli")
        ENDIF
    ENDIF
    
    // 4. Yüksek bilişsel yük + 2+ deneme
    IF state.cognitive_load > 0.8 THEN
        IF attempt_count ≥ 2 THEN
            RETURN (TRUE, "Bilişsel yük yüksek, destek sağlanmalı")
        ENDIF
    ENDIF
    
    // 5. Düşük sebat + 2+ deneme
    IF profile.persistence_score < 0.3 AND attempt_count ≥ 2 THEN
        RETURN (TRUE, "Motivasyonu korumak için destek gerekli")
    ENDIF
    
    RETURN (FALSE, "Öğrenci bağımsız çözmeye teşvik edilmeli")
END FUNCTION
```

#### Karar Matrisi:

| Durum | Deneme | Risk | Bilişsel Yük | Sebat | İpucu? |
|-------|--------|------|--------------|-------|--------|
| Normal | 1-2 | LOW | <0.8 | >0.3 | ❌ |
| Normal | ≥3 | - | - | - | ✅ |
| Risk | ≥2 | HIGH/CRITICAL | - | - | ✅ |
| Yük | ≥2 | - | >0.8 | - | ✅ |
| Düşük Sebat | ≥2 | - | - | <0.3 | ✅ |

---

## 4. İÇERİK ÜRETIM MOTORU

### 4.1 Bloom Taksonomisi Eşleme

#### Pseudocode:

```
FUNCTION MapDifficultyToBloom(difficulty, mastery):
    INPUT:
        difficulty: DifficultyLevel (1-5)
        mastery: Float [0, 1]
    
    OUTPUT:
        bloom_level: String
    
    SWITCH difficulty:
        CASE BEGINNER:
            IF mastery < 0.5 THEN
                RETURN "remember"
            ELSE
                RETURN "understand"
            ENDIF
        
        CASE ELEMENTARY:
            IF mastery < 0.6 THEN
                RETURN "understand"
            ELSE
                RETURN "apply"
            ENDIF
        
        CASE INTERMEDIATE:
            RETURN "apply"
        
        CASE ADVANCED:
            RETURN "analyze"
        
        CASE EXPERT:
            IF mastery < 0.9 THEN
                RETURN "evaluate"
            ELSE
                RETURN "create"
            ENDIF
    END SWITCH
END FUNCTION
```

---

### 4.2 İçerik Üretim Pipeline

#### Pseudocode:

```
FUNCTION GenerateQuestion(learner_profile, learning_state, topic, kc_list):
    INPUT:
        learner_profile: LearnerProfile
        learning_state: LearningState
        topic: String
        kc_list: List<String>  // Knowledge Components
    
    OUTPUT:
        question: Question
    
    // 1. Zorluk ve bilişsel seviye belirleme
    difficulty ← learning_state.recommended_difficulty
    cognitive_level ← MapDifficultyToBloom(difficulty, learning_state.overall_mastery)
    
    // 2. Öğrenen bağlamı hazırlama
    context ← {
        'mastery': learning_state.overall_mastery,
        'strengths': GetStrongComponents(learning_state),
        'weaknesses': GetWeakComponents(learning_state),
        'error_pattern': learner_profile.get_dominant_error_type()
    }
    
    // 3. Prompt oluşturma
    prompt ← BuildQuestionPrompt(
        topic = topic,
        difficulty = difficulty,
        cognitive_level = cognitive_level,
        kc_list = kc_list,
        context = context
    )
    
    // 4. LLM çağrısı
    generated_text ← CallLLM(prompt, temperature=0.7)
    
    // 5. Post-processing
    question_data ← ParseQuestionResponse(generated_text)
    
    // 6. Metadata ekleme
    metadata ← NEW ContentMetadata(
        difficulty_level = difficulty,
        knowledge_components = kc_list,
        cognitive_level = cognitive_level,
        estimated_time = EstimateQuestionTime(difficulty)
    )
    
    // 7. Question objesi oluşturma
    question ← NEW Question(
        question_id = GenerateID("question"),
        question_type = QuestionType.CODE_WRITING,
        content_text = question_data.content,
        metadata = metadata,
        expected_answer = question_data.answer
    )
    
    RETURN question
END FUNCTION
```

---

## 5. AÇIKLAMA VE GERİ BİLDİRİM MOTORU

### 5.1 İçerik Seçim Açıklama

#### Pseudocode:

```
FUNCTION ExplainContentSelection(content, profile, state, recommendation):
    INPUT:
        content: Content
        profile: LearnerProfile
        state: LearningState
        recommendation: NextStepRecommendation
    
    OUTPUT:
        explanation: Explanation
    
    // Kısa özet
    summary ← FORMAT("Bu içerik, {0} seviyene ve mevcut durumuna göre seçildi.",
                     state.recommended_difficulty)
    
    detailed_parts ← []
    evidence ← []
    
    // 1. Zorluk gerekçesi
    ADD_TO(detailed_parts, FORMAT(
        "**Zorluk ({0})**: Genel hakimiyet {1:.0%}. ",
        state.recommended_difficulty,
        state.overall_mastery
    ))
    
    IF state.overall_mastery < 0.4 THEN
        ADD_TO(detailed_parts, "Temel kavramlara odaklanılıyor.")
    ELSE IF state.overall_mastery > 0.7 THEN
        ADD_TO(detailed_parts, "İyi performans sayesinde ileri konulara geçiliyor.")
    ELSE
        ADD_TO(detailed_parts, "Optimal öğrenme hızı için bu seviyede pratik.")
    ENDIF
    
    ADD_TO(evidence, FORMAT("Hakimiyet: {0:.0%}", state.overall_mastery))
    
    // 2. Konu seçimi gerekçesi
    ADD_TO(detailed_parts, FORMAT(
        "\n**Konu ({0})**: {1}",
        recommendation.topic,
        recommendation.reason
    ))
    
    weak_kcs ← GetWeakComponents(state)
    IF recommendation.topic IN weak_kcs THEN
        ADD_TO(detailed_parts,
               "\nBu konu senin gelişim alanlarından. Pratikle hakimiyet kazanacaksın.")
        ADD_TO(evidence, FORMAT("Gelişim konusu: {0}", recommendation.topic))
    ENDIF
    
    // 3. Pedagojik gerekçe
    IF recommendation.intervention ≠ NONE THEN
        reason ← GetInterventionReason(recommendation.intervention)
        ADD_TO(detailed_parts, FORMAT("\n**Özel Durum**: {0}", reason))
    ENDIF
    
    // 4. ZPD uyumu
    IF state.zpd_match < 0.5 THEN
        direction ← (state.overall_mastery > 0.7) ? "üstünde" : "altında"
        ADD_TO(detailed_parts, FORMAT(
            "\n**Uyarlama**: Başarı oranın optimal aralığın {0} olduğu için zorluk ayarlandı.",
            direction
        ))
        ADD_TO(evidence, FORMAT("ZPD uyumu: {0:.0%}", state.zpd_match))
    ENDIF
    
    // Explanation objesi
    explanation ← NEW Explanation(
        type = WHY_THIS_CONTENT,
        title = "Neden Bu İçerik?",
        summary = summary,
        detailed_explanation = JOIN(detailed_parts),
        supporting_evidence = evidence,
        confidence = 0.9
    )
    
    RETURN explanation
END FUNCTION
```

---

## 6. ANA ORKESTRASYONCopyright MOTORU

### 6.1 Tam Öğrenme Döngüsü

#### Pseudocode:

```
FUNCTION LearningCycle(learner_id):
    INPUT:
        learner_id: String
    
    OUTPUT:
        session_results: SessionResults
    
    // 1. Profil al/oluştur
    profile ← GetOrCreateProfile(learner_id)
    
    REPEAT UNTIL session_complete:
        
        // 2. Durum tahmini
        state ← PredictLearningState(profile)
        
        // 3. Risk değerlendirmesi
        risk ← AssessRisk(profile, state)
        
        // 4. Sonraki adım önerisi
        recommendation ← RecommendNextStep(profile, state)
        
        // 5. İçerik üretimi
        content ← GenerateContent(recommendation, profile, state)
        
        // 6. Açıklama üretimi
        explanation ← ExplainSelection(content, profile, state, recommendation)
        
        // 7. İçeriği öğrenene sun
        PRESENT_TO_LEARNER(content, explanation)
        
        // 8. Öğrenen cevabını bekle
        response ← WAIT_FOR_RESPONSE()
        
        // 9. Cevabı değerlendir
        is_correct ← EvaluateResponse(response, content)
        
        // 10. Performans kaydı oluştur
        record ← NEW PerformanceRecord(
            learner_id = learner_id,
            content_id = content.id,
            is_correct = is_correct,
            time_spent = response.time_spent,
            attempts = response.attempts,
            hints_used = response.hints_used
        )
        
        // 11. Profili güncelle
        profile ← UpdateProfile(profile, record)
        
        // 12. Geri bildirim üret
        feedback ← GenerateFeedback(is_correct, response, profile, state)
        
        PRESENT_FEEDBACK(feedback)
        
        // 13. Oturum tamamlanma kontrolü
        session_complete ← CheckSessionComplete(profile, state)
        
    END REPEAT
    
    // 14. Oturum özeti
    summary ← GenerateSessionSummary(profile, state)
    
    RETURN summary
END FUNCTION
```

---

## 7. PERFORMANS METRİKLERİ

### 7.1 Normalized Learning Gain

#### Pseudocode:

```
FUNCTION CalculateLearningGain(pre_score, post_score, max_score):
    INPUT:
        pre_score: Float [0, max_score]
        post_score: Float [0, max_score]
        max_score: Float
    
    OUTPUT:
        normalized_gain: Float [-1, 1]
    
    actual_gain ← post_score - pre_score
    possible_gain ← max_score - pre_score
    
    IF possible_gain = 0 THEN
        RETURN 0.0
    ENDIF
    
    normalized_gain ← actual_gain / possible_gain
    
    RETURN normalized_gain
END FUNCTION
```

#### Matematiksel Formül:

$$
\langle g \rangle = \frac{S_{post} - S_{pre}}{S_{max} - S_{pre}}
$$

Yorumlama:
- $\langle g \rangle < 0.3$: Düşük kazanç
- $0.3 \leq \langle g \rangle < 0.7$: Orta kazanç
- $\langle g \rangle \geq 0.7$: Yüksek kazanç

---

### 7.2 Aralıklı Tekrar (Spacing Effect)

#### Pseudocode:

```
FUNCTION CalculateOptimalInterval(mastery, recent_performance):
    INPUT:
        mastery: Float [0, 1]
        recent_performance: List<Boolean>
    
    OUTPUT:
        optimal_days: Integer
    
    // Taban aralık (hakimiyete göre)
    IF mastery < 0.3 THEN
        base_interval ← 1
    ELSE IF mastery < 0.5 THEN
        base_interval ← 2
    ELSE IF mastery < 0.7 THEN
        base_interval ← 4
    ELSE IF mastery < 0.9 THEN
        base_interval ← 7
    ELSE
        base_interval ← 14
    ENDIF
    
    // Son performansa göre ayarlama
    IF LENGTH(recent_performance) > 0 THEN
        recent_success ← SUM(LAST_N(recent_performance, 5)) / 5
        
        IF recent_success > 0.8 THEN
            multiplier ← 1.5  // İyi performans = uzat
        ELSE IF recent_success < 0.5 THEN
            multiplier ← 0.5  // Zayıf = kısalt
        ELSE
            multiplier ← 1.0
        ENDIF
        
        optimal_interval ← base_interval × multiplier
    ELSE
        optimal_interval ← base_interval
    ENDIF
    
    RETURN MAX(1, ROUND(optimal_interval))
END FUNCTION
```

#### Unutma Eğrisi Formülü:

$$
R(t) = e^{-\frac{t}{S}}
$$

Burada:
- $R(t)$: t zamanında hatırlama olasılığı
- $S$: Dayanıklılık = $1 + (m \times 30)$ (m = mastery)
- $t$: Son pratikten bu yana geçen zaman (gün)

Tekrar önceliği:
$$
P_{priority} = 1 - R(t)
$$

---

## 8. SİSTEM AKIŞ DİYAGRAMLARI

### 8.1 Yüksek Seviye Sistem Akışı

```
┌─────────────┐
│  START      │
└──────┬──────┘
       │
       ▼
┌──────────────────┐
│ Profil Var Mı?   │
└───┬──────────┬───┘
    │Hayır Evet│
    ▼          ▼
┌─────────┐ ┌─────────┐
│ Oluştur │ │   Al    │
└────┬────┘ └────┬────┘
     │           │
     └─────┬─────┘
           │
           ▼
    ┌──────────────┐
    │ Durum Tahmini│
    └──────┬───────┘
           │
           ▼
    ┌──────────────┐
    │ Risk Analizi │
    └──────┬───────┘
           │
      ┌────┴────┐
      │Risk Var?│
      └──┬───┬──┘
    Evet │   │ Hayır
         │   │
         ▼   ▼
    ┌────────┐ ┌────────────┐
    │Müdahale│ │Sonraki Adım│
    └────┬───┘ └─────┬──────┘
         │           │
         └─────┬─────┘
               │
               ▼
        ┌──────────────┐
        │İçerik Üretimi│
        └──────┬───────┘
               │
               ▼
        ┌──────────────┐
        │   Açıklama   │
        └──────┬───────┘
               │
               ▼
        ┌──────────────┐
        │ Öğrenene Sun │
        └──────┬───────┘
               │
               ▼
        ┌──────────────┐
        │ Cevap Bekle  │
        └──────┬───────┘
               │
               ▼
        ┌──────────────┐
        │ Değerlendirme│
        └──────┬───────┘
               │
               ▼
        ┌──────────────┐
        │Profil Güncelle│
        └──────┬───────┘
               │
          ┌────┴────┐
          │Devam Et?│
          └──┬───┬──┘
        Evet │   │ Hayır
             │   │
             └──┐│
                ││
                ▼▼
          ┌──────────┐
          │   BITIŞ  │
          └──────────┘
```

---

## SONUÇ

Bu pseudocode ve algoritmik açıklamalar, KAŞİF-AI sisteminin tüm kritik bileşenlerini kapsar. Akademik tez yazımında:

1. **Algoritma açıklamaları** için pseudocode'ları kullanın
2. **Matematiksel temeller** için formülleri dahil edin
3. **Akış diyagramları** ile sistem davranışını görselleştirin
4. **Akademik referanslar** ile teorik temelleri destekleyin

Her algoritma, ilgili eğitim bilimi teorisi ve ampirik araştırma bulgularıyla desteklenmiştir.

---

**Yazar**: Habip Elis  
**Tez**: Yapay Zekâ Destekli Uyarlanabilir Öğrenme Platformu  
**Kurum**: [Üniversite Adı]  
**Tarih**: Ocak 2026
