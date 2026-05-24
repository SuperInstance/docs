# 20 Novel, Testable Predictions from the Unified Constraint-Theory Framework

**Generated:** 2026-05-24
**Frameworks synthesized:** THREE-HALVES, CONSERVATION-OF-TENSION, ARS-SUBTILIOR-COUNTEREVIDENCE, DISCOVERED-OR-INVENTED, DEEPSEEK-WILD-IDEATION

---

## Category A: HISTORICAL MUSIC (5 predictions — testable by analyzing existing scores)

---

### Prediction A1: The Meantone Syncopation Gradient

**Precise claim:** In keyboard compositions from 1550–1750, the syncopation density (syncopation events per 100 quarter-note beats) correlates negatively with the consonance gradient of the key signature, with Pearson's r ≤ −0.55. Specifically, compositions in "wolf" keys (F♯ major, G♭ major, B major) will have ≥40% higher syncopation density than compositions in "pure" keys (C major, G major, D major, F major) within the same composer's output.

**Mathematical framework:** Conservation of tension (I_vert + I_horiz ≈ T_0). In meantone, keys with low acoustic attractiveness A(K_i) have reduced vertical information. The Boltzmann distribution P(K_i) ∝ exp(β·A(K_i)) predicts these keys are used less often, but WHEN used, the composer must compensate with horizontal complexity to maintain the same expressive tension. The key-to-key consonance gradient ∇_K Φ measures this variation.

**Test methodology:**
1. Select 300+ keyboard works by 10+ composers from 1550–1750 (Frescobaldi, Sweelinck, Froberger, Buxtehude, Bach, Couperin, etc.)
2. Classify each movement by key and estimated tuning (quarter-comma meantone for most, Werckmeister/Valotti for later works)
3. Compute σ_ω(T) for each tuning system and the consonance gradient |∇C|_K for each key
4. Count syncopation events per 100 quarter-note beats using a computational syncopation index S(r, m)
5. Perform linear regression: syncopation density vs. consonance gradient magnitude

**CONFIRM if:** r ≤ −0.55, p < 0.01, and "wolf" keys show ≥40% higher syncopation density.
**REFUTE if:** r > −0.20, or no significant difference between "pure" and "wolf" keys.

---

### Prediction A2: The Ars Subtilior Reversibility Signature

**Precise claim:** Rhythmic complexity in Ars Subtilior compositions (c. 1375–1410) will show a distinct statistical signature from post-ET rhythmic complexity: a **high variance, low persistence** pattern. Specifically, when measured in 4-beat windows across individual works, the Hurst exponent H of the syncopation time-series will be H < 0.5 for Ars Subtilior works (anti-persistent — complexity spikes and reverts) but H > 0.6 for post-1900 complex works (persistent — complexity accumulates). The average H for Ars Subtilior will be ≤0.42; for Nancarrow/Ligeti/Stravinsky, H ≥ 0.62.

**Mathematical framework:** The refinement from the counter-evidence document: pre-ET complexity is "localized and reversible" while post-ET complexity is "persistent and cumulative." This predicts different temporal autocorrelation structures. The Hurst exponent measures long-range dependence in a time series, exactly capturing the reversible-vs-persistent distinction.

**Test methodology:**
1. Digitize rhythmic onset patterns from 30+ Ars Subtilior works (Chantilly Codex, Modena Codex)
2. Digitize rhythmic onset patterns from 30+ post-1900 complex works (Nancarrow Studies, Ligeti Études, Stravinsky ballets)
3. Compute syncopation index S(r, m) in sliding 4-beat windows for each work
4. Calculate the Hurst exponent H of each syncopation time-series using rescaled range analysis
5. Compare distributions via Mann-Whitney U test

**CONFIRM if:** Mean H(Ars Subtilior) ≤ 0.42, mean H(post-ET) ≥ 0.62, p < 0.001.
**REFUTE if:** No significant difference in Hurst exponents, or Ars Subtilior shows H ≥ 0.6.

---

### Prediction A3: The Ockeghem Lattice Collapse

**Precise claim:** In Ockeghem's *Missa Prolationum*, the tempo ratios between voices correspond to small-integer fractions that lie within the first two shells of the Eisenstein integer lattice Z[ω]. Specifically, ≥85% of the prolation ratios used across all movements will have Eisenstein norm ≤ 3, meaning they are expressible as combinations of the generating vectors (1,0) and (0,1) with small coefficients. The average Eisenstein norm of the tempo ratios will be ≤2.5.

**Mathematical framework:** The Eisenstein lattice A_2 from the consonance framework. The lattice vectors (1,0) and (0,1) generate all intervals; their norm gives a "consonance distance." Ockeghem, working without ET but with extraordinary rhythmic sophistication, should naturally gravitate toward lattice-near ratios — not because of tuning, but because the same perceptual machinery that detects harmonic consonance also detects "rhythmic consonance" (tempo ratios that produce early coincidences).

**Test methodology:**
1. Extract the prolation (tempo ratio) for each voice pair in all four movements of *Missa Prolationum*
2. Express each ratio as a fraction p/q in lowest terms
3. Map each p/q to its position in the Eisenstein lattice Z[ω]
4. Compute the Eisenstein norm ||(p/q)|| = √(a² + ab + b²) where p/q is expressed in terms of the lattice generators
5. Calculate the distribution of norms and the percentage with norm ≤ 3

**CONFIRM if:** ≥85% of prolation ratios have Eisenstein norm ≤ 3, and mean norm ≤ 2.5.
**REFUTE if:** <60% of ratios have norm ≤ 3, or mean norm > 4.

---

### Prediction A4: The Baroque Key-Entropy Collapse

**Precise claim:** The Shannon entropy of the key-choice distribution H(K) for keyboard compositions will decrease monotonically from c. 1550 to c. 1850 as tuning moves from meantone toward ET. Specifically: H(K) for works composed c. 1550–1600 (meantone era) will be ≤3.15 bits (non-uniform key usage); H(K) for works c. 1700–1750 (transition era) will be 3.3–3.5 bits; H(K) for works c. 1800–1850 (ET era) will be ≥3.55 bits (approaching the uniform maximum of log₂12 ≈ 3.585 bits). The KL-divergence D_KL(U || P) from the uniform distribution will decrease from ≥0.35 bits to ≤0.05 bits over this period.

**Mathematical framework:** The information-theoretic foundation from the Conservation paper. In meantone, the Boltzmann distribution P(K_i) ∝ exp(β·A(K_i)) produces non-uniform key choice because A(K_i) varies across keys. As ET approaches, A(K_i) → constant, P(K_i) → 1/12, and H(K) → log₂12. The KL-divergence I_vert^eff = D_KL(U || P) measures the "free" vertical information from tuning — it should collapse to zero.

**Test methodology:**
1. Compile key signatures of all keyboard works in three corpora: (a) 200+ works from 1550–1600 (Frescobaldi, Sweelinck, Byrd, etc.), (b) 200+ from 1700–1750 (Bach, Handel, Scarlatti, Couperin), (c) 200+ from 1800–1850 (Beethoven, Schubert, Chopin, Mendelssohn)
2. Compute the empirical key-choice distribution P(K_i) for each corpus
3. Calculate H(K) and D_KL(U || P) for each
4. Test for monotonic increase in H(K) and decrease in D_KL

**CONFIRM if:** H increases monotonically across the three periods, with D_KL ≥ 0.35 bits (meantone) dropping to ≤ 0.05 bits (ET).
**REFUTE if:** H does not increase, or D_KL does not decrease, or the change is <0.1 bits across all periods.

---

### Prediction A5: The 3/2 Temporal Density Ratio in Medieval Mensural Music

**Precise claim:** In Notre Dame organum and Ars Nova motets (c. 1160–1350), the density of hemiola-like rhythmic figures (3-in-2 groupings) per 100 beats will be inversely proportional to the prevalence of pure 3:2 pitch intervals. Specifically, movements notated in tempus perfectum (triple meter, sacred, "3-ified") will have ≥60% fewer additional 3-against-2 rhythmic displacements than movements in tempus imperfectum (duple meter, secular), because the triple meter already "satisfies" the 3:2 perceptual quota. The ratio of hemiola density (perfectum : imperfectum) will be ≤ 0.4.

**Mathematical framework:** The vertical-horizontal isomorphism from THREE-HALVES. If 3:2 is "the same feeling in different domains," then a culture that saturates the vertical/temporal dimension with the number 3 (triple meter everywhere) should need LESS horizontal 3:2 (hemiola), because the perceptual "need" for 3:2 is already met by the meter itself. This is the conservation principle applied microscopically: within a single dimension, 3-saturation in meter reduces 3-demand in cross-rhythm.

**Test methodology:**
1. Analyze 100+ movements from the Magnus Liber Organi (Léonin/Pérotin) and 100+ Ars Nova motets (Machaut, Vitry)
2. Classify each as tempus perfectum or tempus imperfectum
3. Count hemiola/3-against-2 events per 100 beats using rhythmic onset analysis
4. Compare mean hemiola density between the two groups

**CONFIRM if:** Hemiola density ratio (perfectum : imperfectum) ≤ 0.4, p < 0.01.
**REFUTE if:** No significant difference, or perfectum movements have MORE hemiolas.

---

## Category B: CONTEMPORARY MUSIC (5 predictions — testable by analyzing recent compositions)

---

### Prediction B1: The Jazz Pitch-Bend Compensation Index

**Precise claim:** In jazz recordings (1950–2020), the density of pitch bends, blue notes, and microtonal inflections per minute (measured in cents deviation from ET) will be negatively correlated with syncopation density at the level of individual performances, with r ≤ −0.45. Solo piano jazz (no pitch bends possible on ET piano) will have ≥25% higher syncopation density than guitar/saxophone jazz (where pitch inflection is possible), controlling for tempo and ensemble size.

**Mathematical framework:** The conservation law I_vert + I_horiz ≈ T_0, applied at the micro-level of individual performance choices. A performer who introduces vertical micro-variation (pitch bends) is restoring I_vert information that ET removed. This should reduce the "need" for I_horiz (syncopation). Piano, locked into ET with no pitch flexibility, must compensate entirely through rhythm. The Conservation paper's Prediction 4 (jazz paradox) extends this: blues has more pitch flexibility → should have less rhythmic complexity than piano jazz.

**Test methodology:**
1. Select 200 jazz recordings: 100 piano trio, 100 saxophone/guitar-led groups, matched for decade and tempo
2. For each: extract pitch trajectory using F0 estimation (pYIN/CREPE) and compute pitch inflection density (events >15 cents from ET per minute)
3. Extract rhythmic onset patterns and compute syncopation index per measure
4. Perform correlation analysis and group comparison

**CONFIRM if:** r ≤ −0.45, and piano jazz has ≥25% higher syncopation than non-piano jazz (p < 0.01).
**REFUTE if:** r > −0.15, or no significant difference between piano and non-piano jazz syncopation.

---

### Prediction B2: The Microtonal Rhythmic Simplicity Prediction

**Precise claim:** Contemporary compositions using just intonation or microtonal tuning systems (>12 pitches per octave) will have ≥30% lower syncopation density and ≥20% lower onset entropy H(onset) than matched ET compositions from the same decade (1960–2020). The "rhythmic simplicity" effect will be strongest for compositions using tuning systems with high inter-key variation (σ_ω > 0.15 on our consonance scale).

**Mathematical framework:** Direct application of the conservation law. If I_vert is restored by microtonality (high σ_ω → high effective vertical information), then I_horiz should decrease proportionally. The Conservation paper's Prediction 3 (microtonal renaissance) specifies the direction. The lattice dimensionality argument: microtonal music restores the 2D lattice Λ⁺ (pitch + quality), reducing the "need" to enrich the rhythmic dimension.

**Test methodology:**
1. Select 100 microtonal/JI compositions from 1960–2020 (Partch, Johnston, Tenney, Radulescu, Haas, string quartet microtonal repertoire)
2. Select 100 ET compositions matched by decade, ensemble size, and genre (contemporary classical)
3. Compute syncopation density and onset entropy for all 200 works
4. Compare via paired analysis (matching by decade)
5. Compute σ_ω for each tuning system used and test correlation with rhythmic metrics

**CONFIRM if:** Microtonal works show ≥30% lower syncopation and ≥20% lower onset entropy (p < 0.01), and σ_ω correlates negatively with rhythmic complexity.
**REFUTE if:** No significant difference, or microtonal works are MORE rhythmically complex.

---

### Prediction B3: The EDM Euclidean Field Effect

**Precise claim:** In electronic dance music (EDM) productions from 2000–2025, the Euclidean distance between a track's primary rhythmic pattern and the nearest Euclidean rhythm E(k,n) will be negatively correlated with the track's harmonic complexity (measured as the number of distinct chord types per minute and the chromatic entropy of pitch content). Tracks using only I-IV-V-I progressions will have rhythmic patterns that are ≥85% identical to a canonical Euclidean rhythm (E(3,8), E(4,16), E(5,16), etc.), while tracks with jazz-influenced harmony will deviate from Euclidean patterns by ≥20%.

**Mathematical framework:** The structural isomorphism from the Conservation paper (Theorem 6.2): Euclidean rhythms and the circle of fifths solve the same optimization problem. If a track has "used up" its structural variation budget on harmony (complex chords), it will have less budget for rhythmic variation from the Euclidean ideal. Conversely, harmonically simple tracks must generate interest through rhythmic deviation from the Euclidean template — OR they must be maximally Euclidean (groove-locked) to compensate through pure kinetic energy. The prediction is that EDM, which tends toward harmonic simplicity, will overwhelmingly use Euclidean rhythms, and that harmonic complexity within EDM will trade off against Euclidean conformity.

**Test methodology:**
1. Analyze 500 EDM tracks from 2000–2025 across subgenres (house, techno, trance, dubstep, drum & bass)
2. Extract rhythmic patterns from drum tracks using onset detection
3. Compute Euclidean distance from each pattern to the nearest E(k,n) for k=2..8, n=4..16
4. Compute harmonic complexity: chromatic entropy of pitch classes, number of distinct chord types per minute
5. Perform correlation analysis

**CONFIRM if:** Harmonic simplicity strongly predicts Euclidean conformity (r ≥ 0.5), and I-IV-V tracks are ≥85% Euclidean.
**REFUTE if:** No correlation, or harmonically simple tracks are LESS Euclidean than harmonically complex ones.

---

### Prediction B4: The Minimalism Lattice Prediction

**Precise claim:** In minimalist compositions (Reich, Glass, Riley, Andriessen, 1965–2000), the rate of phase-shift events (temporal displacements) per minute will be proportional to the reciprocal of the harmonic complexity: PhaseShiftRate ∝ 1/H(chord). Works that are harmonically static (e.g., Reich's *Piano Phase*, based on a single 12-note chromatic cell) will have ≥3× the phase-shift rate of works with richer harmony (e.g., Glass's *Koyaanisqatsi*, which uses functional harmony), when normalized for tempo.

**Mathematical framework:** The conservation law applied to minimalism as a special case. Minimalism strips away most traditional parameters, creating a "controlled experiment" for the tension budget. If the total budget T_0 must be maintained, and harmony is minimal, then ALL tension must come from the temporal domain — phase shifts, additive processes, metric displacement. The lattice framework: minimalism works in a near-zero-dimensional pitch lattice (repeated cells), so the augmented lattice Λ⁺ must be enriched entirely through the rhythmic dimension.

**Test methodology:**
1. Select 60 minimalist works: 20 harmonically static (Reich phase pieces, early Glass), 20 harmonically moderate (later Reich, Glass film scores), 20 harmonically rich (Andriessen, Adams)
2. Count phase-shift events and metric displacements per minute
3. Compute harmonic entropy H(chord) for each work
4. Test the inverse proportionality claim

**CONFIRM if:** Phase-shift rate ≥ 3× higher in harmonically static works, and correlation with 1/H(chord) has r ≥ 0.6.
**REFUTE if:** No correlation, or harmonically rich works have equal or higher phase-shift rates.

---

### Prediction B5: The Cover-Version Tension Equivalence

**Precise claim:** When a song is covered in a different tuning system (e.g., a meantone rendition of an ET pop song, or vice versa), the total information rate I_total = I_vert + I_horiz (measured in bits/second) will be conserved within ±15% of the original. Specifically, a meantone cover of an ET song will show decreased syncopation (ΔI_horiz < 0) but increased pitch variation (ΔI_vert > 0), with |ΔI_vert + ΔI_horiz| / I_total ≤ 0.15.

**Mathematical framework:** The conservation law at the level of individual performances. If a song's expressive content is roughly constant across renditions, then changing the tuning system (which changes I_vert) should produce a compensating change in I_horiz (performance practice: the performer adjusts rhythm to compensate for the new tuning's vertical properties). This is the most direct test of the conservation principle.

**Test methodology:**
1. Record 20 pop/rock songs in two versions: (a) standard ET piano, (b) meantone-tuned keyboard
2. Use the same performer, tempo, and dynamics for both versions
3. Compute I_vert: pitch-class distribution entropy + interval entropy
4. Compute I_horiz: onset entropy + syncopation index per measure
5. Compare I_total between ET and meantone versions

**CONFIRM if:** |ΔI_total| / I_total ≤ 0.15 for ≥75% of songs, with I_vert and I_horiz changing in opposite directions.
**REFUTE if:** I_total varies by >25% across tuning conditions, or both I_vert and I_horiz change in the same direction.

---

## Category C: CROSS-CULTURAL MUSIC (5 predictions — testable by comparing traditions)

---

### Prediction C1: The Sa-Pa / Gong-Zhi Convergence Window

**Precise claim:** When the perfect fifth interval is measured in cents across performances of Indian classical (Sa-Pa drone), Chinese traditional (gōng-zhǐ on gǔqín), Arabic (al-khāmis on ʿūd), and West African (mbira tuning) music, the mean fifth size will fall within a 15-cent window centered on 702 cents (just intonation), with standard deviation ≤8 cents. This window will be significantly narrower than the distribution of other intervals (e.g., major thirds will span >50 cents across these traditions). The coefficient of variation (σ/μ) for the fifth will be ≤1.1%, compared to ≥5% for major thirds and minor thirds.

**Mathematical framework:** The "discovered" argument from DISCOVERED-OR-INVENTED. If 3:2 is a physical discovery (not cultural), its acoustic instantiation should be highly conserved across independently developed traditions. The consonance score C(3/2) ≈ 0.95 produces a steep gradient — any deviation from the pure ratio is heavily penalized. Other intervals (5/4 major third, 6/5 minor third) have flatter gradients, allowing more cultural variation.

**Test methodology:**
1. Collect pitch measurements from 50 performances per tradition (200 total): Indian (tambūrā drone analysis), Chinese (gǔqín pitch extraction), Arabic (ʿūd intonation analysis), African (mbira tuning measurement)
2. Measure the fifth interval in cents for each performance
3. Compute mean, standard deviation, and coefficient of variation
4. Repeat for major third and minor third intervals
5. Compare the coefficient of variation across interval types

**CONFIRM if:** Fifth CV ≤ 1.1%, third CV ≥ 5%, and the 15-cent window holds for the fifth across all four traditions.
**REFUTE if:** Fifth variation is comparable to third variation, or any single tradition deviates by >20 cents from 702.

---

### Prediction C2: The Polyrhythm Plateau in Non-ET Cultures

**Precise claim:** Musical cultures using just intonation or near-just tuning systems (Indian classical, Javanese gamelan, Japanese gagaku) will show a polyrhythmic complexity ceiling: the maximum rhythmic subdivision ratio used in their traditional repertoire will be ≤7:4 (or equivalently, rhythmic ratios with Eisenstein norm ≤4). Post-ET Western music will show no such ceiling, with ratios up to e:π documented (Nancarrow). Specifically, the 95th percentile of rhythmic ratio complexity (measured as the denominator of the ratio in lowest terms) will be ≤6 for non-ET traditions and ≥12 for post-ET Western music.

**Mathematical framework:** The "ceiling removal" refinement from the counter-evidence document. Non-ET cultures have sufficient vertical information from tuning, so horizontal (rhythmic) complexity never needs to exceed the "natural" ceiling set by the consonance field's gradient. ET cultures, having lost vertical information, push rhythmic complexity beyond this natural ceiling. The Eisenstein lattice provides the complexity metric: higher-norm ratios are further from the lattice origin and thus "more dissonant" in the rhythmic domain.

**Test methodology:**
1. Analyze rhythmic ratios in 500 works from non-ET traditions: 150 Indian classical (konnakol transcription), 150 Javanese gamelan, 100 Japanese gagaku, 100 Arabic iqa'at
2. Analyze 500 works from post-ET Western: 100 jazz, 100 classical (1900–2000), 100 EDM, 100 contemporary art music, 100 Nancarrow/Gann repertoire
3. Express all rhythmic ratios as fractions p/q in lowest terms; compute Eisenstein norm and denominator
4. Compare the 95th percentile of the denominator distribution

**CONFIRM if:** Non-ET 95th percentile ≤ 6, post-ET 95th percentile ≥ 12.
**REFUTE if:** Non-ET traditions show equal or higher rhythmic ratio complexity than post-ET.

---

### Prediction C3: The Tala-Maqam Rhythmic Independence Principle

**Precise claim:** In Arabic maqam music, the correlation between melodic mode (maqām) and rhythmic cycle (iqa') will be statistically indistinguishable from random (Cramér's V < 0.1), because the tuning system (microtonal, non-ET) provides sufficient vertical information that rhythmic and melodic complexity don't need to compensate for each other. In contrast, in Western jazz, the correlation between harmonic complexity (chord type) and rhythmic complexity (syncopation density) will be significantly positive (r ≥ 0.3), because ET forces compensation within each performance.

**Mathematical framework:** The conservation law's cross-cultural prediction. In non-ET systems, the vertical and horizontal dimensions are decoupled — each can be independently varied without affecting the other's information content. In ET systems, the dimensions are coupled: you must compensate in one for what you lack in the other. This predicts that non-ET traditions will show no systematic relationship between melodic and rhythmic parameters, while ET traditions will show positive correlation.

**Test methodology:**
1. Analyze 200 Arabic maqam performances: classify each by maqām (melodic mode) and iqa' (rhythmic cycle)
2. Compute Cramér's V for the maqām × iqa' contingency table
3. Analyze 200 jazz performances: measure harmonic complexity (chord types per measure) and syncopation density
4. Compute Pearson's r between harmonic and rhythmic complexity
5. Compare the two correlation structures

**CONFIRM if:** Arabic Cramér's V < 0.1 (independence), jazz r ≥ 0.3 (positive coupling).
**REFUTE if:** Arabic tradition shows strong mode-rhythm coupling, or jazz shows no correlation.

---

### Prediction C4: The 3:2 Universality Threshold in Infant Perception

**Precise claim:** Infants aged 6–12 months, tested across cultures (minimum 5 cultural groups on 4 continents), will show a statistically significant preference for 3:2 rhythmic patterns over 4:3 patterns (measured as longer looking time or preferential head-turning), with effect size Cohen's d ≥ 0.5. This preference will be present BEFORE any systematic musical enculturation (verified by testing in cultures where 4:3 polyrhythms are more common than 3:2, e.g., certain Pygmy musical traditions). The preference for 3:2 will not be significantly different across cultural groups (between-group variance < within-group variance).

**Mathematical framework:** The "pre-human" argument from DISCOVERED-OR-INVENTED. If 3:2 processing is a feature of nervous system architecture (not cultural learning), then pre-enculturation infants should preferentially process it regardless of culture. The neural resonance theory (Large & Snyder) predicts that 3:2 produces the most stable standing-wave pattern in neural oscillators. The linguistic analogy: like infants' universal preference for /a/, 3:2 preference should precede and transcend culture.

**Test methodology:**
1. Recruit 200 infants (6–12 months) from 5+ cultural groups (e.g., US, Japan, India, Ghana, Brazil)
2. Use head-turn preference or looking-time paradigm
3. Present paired stimuli: 3:2 polyrhythm vs. 4:3 polyrhythm, matched for tempo and timbre
4. Measure preference (looking time / head-turn duration)
5. Compute effect size within each cultural group and compare across groups

**CONFIRM if:** d ≥ 0.5 in ≥4 of 5 cultural groups, with no significant between-group difference (ANOVA p > 0.05).
**REFUTE if:** No preference in ≥3 cultural groups, or strong between-group differences suggesting enculturation.

---

### Prediction C5: The Excitatory-Inhibitory Resonance Prediction

**Precise claim:** The EEG neural entrainment response (measured as inter-trial phase coherence ITPC at the polyrhythmic frequency) to 3:2 polyrhythms will be ≥1.4× stronger than the response to 5:4 polyrhythms, and the response will show a characteristic peak at frequencies corresponding to the 3:2 subharmonic (the GCD frequency). This 3:2 enhancement effect will be present in musicians AND non-musicians (no musician/non-musician interaction, p > 0.10), and the magnitude of the enhancement will correlate with the listener's individual excitatory-inhibitory (E/I) ratio in auditory cortex as measured by MR spectroscopy (r ≥ 0.3).

**Mathematical framework:** DeepSeek's speculative thesis that the brain IS a 3:2 machine, with E/I ratio ≈ 3:2. If cortical E/I architecture determines consonance processing, then individuals with E/I ratios closer to 3:2 should show stronger neural resonance to 3:2 stimuli. The prediction connects the neural level (E/I balance, ITPC) to the music-theoretic level (polyrhythmic consonance) via the mathematical framework of periodic-signal processing.

**Test methodology:**
1. Recruit 50 participants (25 musicians, 25 non-musicians)
2. Present 3:2, 4:3, 5:4, and 7:4 polyrhythms while recording 64-channel EEG
3. Compute ITPC at each subharmonic frequency (GCD of the two rhythmic periods)
4. Obtain MR spectroscopy measures of Glu/GABA ratio (E/I index) in auditory cortex for each participant
5. Correlate ITPC enhancement for 3:2 with individual E/I ratio

**CONFIRM if:** 3:2 ITPC ≥ 1.4× stronger than 5:4, no musician interaction, and E/I ratio correlates with 3:2 enhancement at r ≥ 0.3.
**REFUTE if:** No enhancement for 3:2, or enhancement is musician-specific, or no E/I correlation.

---

## Category D: FUTURE MUSIC (5 predictions — testable by experiment or waiting)

---

### Prediction D1: The AI Composer Tuning-Rhythm Tradeoff

**Precise claim:** A generative AI music model (e.g., MusicLM, Jukebox, or similar transformer-based model) trained exclusively on meantone-tuned music will spontaneously generate compositions with ≥40% lower rhythmic complexity (measured by syncopation density and onset entropy) than the same model trained on ET-tuned music, when both are prompted with identical text descriptions. The effect will be observable within the first 100 generated samples (power > 0.8) and will not require any explicit encoding of tuning-rhythm relationships in the model architecture.

**Mathematical framework:** If the conservation law I_vert + I_horiz ≈ T_0 is a genuine structural feature of music (not just a cultural convention), then a statistical model trained on music that has high I_vert (meantone) should learn to generate music with lower I_horiz, because the training data embodies the tradeoff. This is a strong test of whether the conservation law is a mathematical regularity discoverable by pattern recognition, or merely a post-hoc description of human behavior.

**Test methodology:**
1. Prepare two training corpora: (a) 10,000 MIDI files rendered in quarter-comma meantone, (b) same 10,000 files rendered in 12-TET
2. Train identical model architectures on each corpus
3. Generate 100 compositions from each model using the same prompts
4. Compute syncopation density and onset entropy for all generated compositions
5. Compare distributions via Mann-Whitney U test

**CONFIRM if:** Meantone-trained model produces ≥40% lower rhythmic complexity (p < 0.01).
**REFUTE if:** No significant difference, or meantone model produces MORE rhythmic complexity.

---

### Prediction D2: The Microtonal Pop Explosion of the 2030s

**Precise claim:** By 2035, a measurable fraction (≥5%) of top-100 pop singles (Billboard, Spotify Global) will feature intentional microtonal content (pitches outside 12-TET by >20 cents) as a primary compositional feature (not just ornamentation). This microtonal pop will show a compensating decrease in rhythmic syncopation density of ≥20% relative to contemporaneous ET pop, consistent with the conservation law. The triggering technology will be software synthesizers with accessible microtonal interfaces (e.g., MPE controllers becoming standard).

**Mathematical framework:** The conservation law predicts that when vertical information is reintroduced (microtonality), horizontal complexity should decrease. The historical precedent: when ET was adopted, rhythmic complexity increased. The reverse should hold. The microtonal renaissance is already underway in art music; the prediction is that it will reach mainstream pop, and when it does, the conservation law will be visible in the rhythmic simplification.

**Test methodology:**
1. Monitor Billboard Hot 100 / Spotify Global Top 100 annually from 2026–2035
2. Use pitch analysis (F0 estimation on vocal + instrumental stems) to detect microtonal content (>20 cents deviation from nearest ET pitch sustained for >100ms)
3. Compute syncopation density for microtonal vs. ET pop songs
4. Track the percentage of microtonal pop over time

**CONFIRM if:** By 2035, ≥5% of top-100 singles feature microtonality, and these have ≥20% lower syncopation than ET counterparts.
**REFUTE if:** Microtonality remains <1% of mainstream pop through 2035, or microtonal pop shows equal/higher syncopation.

---

### Prediction D3: The Lattice Canon Perceptual Convergence Point

**Precise claim:** In a Nancarrow-style Study No. 50 (lattice canon, as proposed in DEEPSEEK-WILD-IDEATION) where multiple voices converge from different Eisenstein lattice points toward the origin, listeners will exhibit a measurable "perceptual convergence response" — a significant decrease in galvanic skin response (GSR) and a shift toward parasympathetic dominance (increased HRV) — beginning exactly when ≥50% of voices enter the first shell of the Eisenstein lattice (norm ≤ 1). This response will occur regardless of musical training and will be stronger than the response to a standard V-I cadence in Western music.

**Mathematical framework:** The Eisenstein lattice encodes consonance as proximity to the origin. A lattice canon where voices converge toward the origin is creating a continuously increasing consonance field — the musical equivalent of a potential well. The listener's nervous system, tracking the consonance gradient, should show a relaxation response (decreased arousal) as the gradient steepens toward maximum consonance. This is the neurobiological correlate of the "resolution" feeling, but operating on the full lattice structure rather than just a V-I chord progression.

**Test methodology:**
1. Compose a 12-voice lattice canon (Study No. 50) where voices start at various Eisenstein lattice points and converge to the origin over 3 minutes
2. Recruit 40 participants (20 musicians, 20 non-musicians)
3. Record GSR, ECG (for HRV), and EEG during listening
4. Mark the temporal points where 25%, 50%, 75%, and 100% of voices enter each lattice shell
5. Test for significant physiological changes at each convergence point, and compare to a control condition (standard V-I cadence in Bach chorale)

**CONFIRM if:** GSR decrease and HRV increase at ≥50% convergence point (p < 0.01), with effect larger than V-I cadence response.
**REFUTE if:** No physiological change at convergence points, or changes are smaller than standard cadence response.

---

### Prediction D4: The Non-Human Primate 3:2 Discrimination Threshold

**Precise claim:** Chimpanzees (Pan troglodytes) and bonobos (Pan paniscus) will successfully discriminate 3:2 polyrhythms from 4:3 polyrhythms at a significantly higher rate than chance (≥70% correct in a two-alternative forced choice task, chance = 50%), but will fail to discriminate 4:3 from 5:4 at above-chance levels (<55% correct). The 3:2 discrimination advantage (accuracy for 3:2 vs 4:3 minus accuracy for 4:3 vs 5:4) will be ≥20 percentage points. Neural recordings (if available) will show enhanced phase-locking in the inferior colliculus for 3:2 compared to 5:4.

**Mathematical framework:** The "pre-human" hypothesis from DISCOVERED-OR-INVENTED. If 3:2 is the fingerprint of a nervous system that processes periodic signals, then the simplest prediction is that non-human primates — who share our basic auditory brainstem architecture — will show enhanced discrimination for 3:2 but not for more complex ratios. The Eisenstein norm of 3:2 is 1 (origin-adjacent), while 4:3 has norm 2 and 5:4 has norm 3 — the discrimination threshold should correspond to a lattice-shell boundary.

**Test methodology:**
1. Train 10 chimpanzees/bonobos on a touch-screen discrimination task: hear two polyrhythmic patterns, indicate "same" or "different"
2. Test on three pairs: 3:2 vs 4:3, 4:3 vs 5:4, 3:2 vs 5:4
3. Measure accuracy and reaction time for each pair
4. Compute the discrimination advantage for 3:2
5. Optionally: record auditory brainstem responses (ABR) to the different polyrhythms

**CONFIRM if:** 3:2 vs 4:3 accuracy ≥70%, 4:3 vs 5:4 accuracy <55%, advantage ≥20 points.
**REFUTE if:** No difference between ratio pairs, or all ratios discriminated equally well.

---

### Prediction D5: The Alien Signal 3:2 Signature

**Precise claim:** If an extraterrestrial civilization transmits a signal intended to convey "aesthetic" or "structured" information (as opposed to raw data), the signal will contain statistically detectable 3:2 periodicity relationships at multiple timescales simultaneously. Specifically, a spectrotemporal analysis of the signal using a wavelet decomposition will reveal that the ratio of dominant frequencies (spectral 3:2) correlates with the ratio of dominant temporal periods (temporal 3:2), producing a statistically significant (p < 0.001) spatiotemporal correlation that does not appear in natural astrophysical signals. The signal's structure will be consistent with a Nancarrow-type isomorphism between pitch and rhythm.

**Mathematical framework:** The alien music prediction from DISCOVERED-OR-INVENTED. If 3:2 is a feature of wave physics (not human culture), and if the vertical-horizontal isomorphism is a mathematical inevitability (not a Western invention), then any civilization encoding aesthetic information in a wave medium will produce signals with correlated 3:2 structure in both frequency and time domains. The wavelet decomposition is the natural tool for detecting simultaneous frequency and temporal structure. The Nancarrow isomorphism (Study No. 37) provides the template: if we find a signal where spectral ratios mirror temporal ratios at 3:2, it is evidence of intentional aesthetic encoding.

**Test methodology:**
1. Apply continuous wavelet transform (CWT) to candidate SETI signals
2. Extract dominant frequencies f_1, f_2 and dominant temporal periods T_1, T_2
3. Compute spectral ratio R_f = max(f)/min(f) and temporal ratio R_T = max(T)/min(T)
4. Test for correlation between R_f and R_T across timescales
5. Compare to null distribution from natural astrophysical signals (pulsars, quasars, FRBs)

**CONFIRM if:** A signal shows correlated 3:2 structure in frequency AND time domains at p < 0.001, not present in natural signal null distribution.
**REFUTE if:** All candidate signals show independent spectral and temporal structure, or natural signals also show correlated 3:2.

---

## Summary Table

| ID | Category | Core Metric | Threshold | Framework Source |
|----|----------|-------------|-----------|-----------------|
| A1 | Historical | Syncopation vs. key gradient | r ≤ −0.55 | Conservation Law |
| A2 | Historical | Hurst exponent (Ars Subtilior vs. modern) | H ≤ 0.42 vs ≥ 0.62 | Counter-evidence (reversibility) |
| A3 | Historical | Eisenstein norm of Ockeghem prolation ratios | 85% with norm ≤ 3 | Lattice geometry |
| A4 | Historical | Key-choice entropy over 1550–1850 | H increases, D_KL collapses | Information theory |
| A5 | Historical | Hemiola density: perfectum vs imperfectum | Ratio ≤ 0.4 | 3/2 isomorphism |
| B1 | Contemporary | Pitch bends vs. syncopation in jazz | r ≤ −0.45 | Conservation Law |
| B2 | Contemporary | Microtonal rhythmic simplicity | ≥30% lower syncopation | Conservation Law |
| B3 | Contemporary | EDM Euclidean conformity vs. harmony | r ≥ 0.5 | Euclidean isomorphism |
| B4 | Contemporary | Phase-shift rate vs. harmonic complexity | ≥3× ratio | Conservation Law |
| B5 | Contemporary | Cover-version tension equivalence | ±15% conservation | Conservation Law |
| C1 | Cross-cultural | Fifth interval convergence across traditions | CV ≤ 1.1%, 15-cent window | Discovery thesis |
| C2 | Cross-cultural | Polyrhythmic ceiling in non-ET cultures | 95th pctile ≤ 6 | Ceiling-removal thesis |
| C3 | Cross-cultural | Mode-rhythm decoupling in maqam vs jazz | V < 0.1 vs r ≥ 0.3 | Conservation Law |
| C4 | Cross-cultural | Infant 3:2 preference across cultures | d ≥ 0.5, no between-group diff | Discovery / neural resonance |
| C5 | Cross-cultural | E/I ratio predicts 3:2 neural entrainment | r ≥ 0.3 | Neural architecture thesis |
| D1 | Future | AI model learns conservation law from data | ≥40% lower rhythm | Conservation Law |
| D2 | Future | Microtonal pop by 2035 | ≥5% of top-100 | Conservation Law |
| D3 | Future | Lattice canon convergence physiology | GSR↓ HRV↑ at 50% convergence | Lattice geometry |
| D4 | Future | Primate 3:2 discrimination advantage | ≥20 points over 4:3:5:4 | Discovery / evolution |
| D5 | Future | Alien signal 3:2 spatiotemporal correlation | p < 0.001 | Physics of waves |

---

*"The best framework is the one that sticks its neck out. These 20 predictions are the neck. Let the data do the cutting."*
