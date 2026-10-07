# KVKK Compliance Guide for IDENUM: Drafting the Aydınlatma Metni and Açık Rıza Metni (September 2026)

## TL;DR
- IDENUM must publish a KVKK Article 10 **Aydınlatma Metni** covering all mandatory elements, and — under the Board's Principle Decision of 18/02/2026 No. 2026/347 (published in the Resmî Gazete 24 March 2026, adopted unanimously under KVKK Arts. 5, 6, 10, 12) — keep the **Açık Rıza Metni** as a physically separate text with separate opt-in boxes; most of IDENUM's core processing (voice-to-text to deliver the assistant, dictated contacts, one-time location, reminders) should rely on **contractual necessity (Art. 5/2-c)**, NOT consent, with explicit consent reserved narrowly for genuinely optional processing.
- The single biggest compliance exposure is the **Supabase EU/Frankfurt backend**: this is a continuous cross-border transfer that, under the post-2024 regime, cannot lawfully rest on explicit consent (consent is now only for one-off "arızi" transfers) — IDENUM must execute a KVKK **Standart Sözleşme** (SCC, Controller→Processor type SS-2) and file it with the Kurum within 5 business days via the Standart Sözleşme Bildirim Modülü.
- Plain speech-to-text is **ordinary personal data (Art. 5)**, not biometric special-category data (Art. 6) — provided IDENUM does not do voiceprint/speaker identification; third-party names the user dictates ("Ahmet'i ara") do trigger a residual Article 10 duty on IDENUM, and push notifications require **granular, separate consent** per the Board's 14 January 2026 announcement.

## Key Findings

### 1. Mandatory elements of an Aydınlatma Metni (KVKK Art. 10)
KVKK Article 10 and the Communiqué on Principles and Procedures for Fulfilling the Disclosure Obligation (Aydınlatma Yükümlülüğü Tebliği, 10 March 2018) require the notice to contain, at minimum:
1. **Identity of the data controller** and its representative if any (title, address, contact/KEP/e-mail).
2. **Purposes** for which personal data will be processed (specific, not vague — "gerekli hallerde"-type wording is prohibited).
3. **To whom and for what purposes** the data may be transferred (recipient groups and the purpose of each transfer must be spelled out separately).
4. **Method of collection and the legal basis** (which of the Art. 5/6 processing conditions each purpose rests on).
5. **The data subject's rights under Art. 11** (learning whether data is processed, requesting information, correction, erasure, objection to automated-only decisions, compensation, etc.), plus an application/complaint channel.

The Communiqué and Board guidance additionally require the **retention period / retention criteria** to be stated — "süresiz" (indefinite) is not acceptable; either the statutory retention period or the purpose-linked period must be given. The disclosure must use plain, clear language, be given at the latest at the moment of collection, and the **burden of proof** that it was made rests on the controller.

**Sanctions (2026, confirmed figures):** Per KVKK's 31 December 2025 announcement applying the 25.49% revaluation rate (Resmî Gazete 27.11.2025), the 2026 Article 10 disclosure-breach fine band (Md. 18/1-a) is **85,437 TL to 1,709,200 TL**; the general data-security fine (Md. 18) runs **256,357–17,092,242 TL**; and failure to notify a Standart Sözleşme carries a fine of **90,308–1,806,377 TL**.

### 2. When Açık Rıza is required vs. other legal bases
Explicit consent (Art. 5/1) is the **exception, not the default**. If any Art. 5/2 condition applies, consent must NOT be sought — asking for unnecessary consent is itself a violation because it undermines "özgür irade" (free will) and converts consent into a precondition of service. Mapping this to IDENUM:
- **Voice input → STT to fulfil the assistant's core function; contacts/names the user dictates; one-time location for the requested task; calendar/time reminders**: all necessary to perform the user-requested service → rely on **Art. 5/2-c (necessity for performance of a contract)**. Consent is not appropriate here.
- **Behavioural/preference profiling (the personal_memory table — temporary, routine, preference, identity layers)**: to the extent this goes beyond what is strictly necessary to deliver the requested action and is used to personalise/learn user habits, it is optional enrichment → **explicit consent (Art. 5/1)**, taken granularly and separably from the core service. The Board's mobile-app guidance states that where data not needed for the app's core function is processed, consent is required, and the user must not be deprived of the core service for refusing.
- **Push notifications**: see item 5 — granular consent.

### 3. 2025–2026 KVKK guidance relevant to AI assistants, voice, location & cross-border hosting
- **Cross-border transfer regime (Law 7499, 12 March 2024, effective 1 June 2024; Regulation of 10 July 2024)**: a three-tier system — (i) adequacy decision (yeterlilik kararı); (ii) appropriate safeguards (standart sözleşme / bağlayıcı şirket kuralları); (iii) exceptionally, "arızi" (occasional/one-off) transfers, including consent after being informed of the risks. As of 2026 **the Kurul has NOT issued any adequacy decision** for any country, so IDENUM cannot rely on adequacy for Frankfurt hosting. Continuous cloud hosting is expressly **not "arızi"**, so consent-based transfer is unlawful for this scenario. The practical route is the **Standart Sözleşme (SCC)**, type SS-2 (Controller→Processor), issued by Board decision 2024/959; it may not be modified and must be **notified to the Kurum within 5 business days** of signature (KVKK Art. 9(5) as amended by Law 7499) via the Standart Sözleşme Bildirim Modülü at standartsozlesme.kvkk.gov.tr (module launched by Kurul Decision 17.10.2024 No. 2024/1793, announced 25 October 2024).
- **AI guidance**: KVKK published "Yapay Zekâ Alanında Kişisel Verilerin Korunmasına Dair Tavsiyeler" (recommendations, 2024) and, on **24 November 2025, the "Üretken Yapay Zekâ ve Kişisel Verilerin Korunması Rehberi (15 Soruda)"** (Yayın No. 113) — the Kurum's first comprehensive AI guide, which states that use of AI systems hosted outside Türkiye constitutes a cross-border transfer under KVKK and that publicly accessible data cannot be freely used to train AI models. Core themes across the guidance: privacy by design, data minimisation, DPIA (mahremiyet etki değerlendirmesi) for high-risk processing, local processing where possible, and human-rights-centred design.
- **Voice/AI assistants**: regulators treat voice assistants as higher-risk because they process voice, location, device data and behavioural data together, and emphasise granular consent and adequacy of disclosure notices.

### 4. Voice classification and third-party data ("Ahmet'i ara")
- **Classification**: plain STT transcription of what the user says is **ordinary personal data (Art. 5)**. Voice becomes **special-category biometric data (Art. 6)** only when subjected to a specific technical process that uniquely identifies/verifies the person (voiceprint / ses biyometrisi). As academic commentary (Dr. Mücahit Ünal, Lexpera) puts it: *"Görüntü ve ses gibi kişisel verilerin, biyometrik yöntemler vasıtası ile bir tanımlama veya doğrulama işlemine tabi tutulmadığı sürece özel nitelikli kişisel veri olarak kabul edilmemesi gerekmektedir."* This is consistent with KVKK's own Özel Nitelikli Veri Rehberi, which defines biometric data as arising from "spesifik teknik bir işleme" that identifies the person. **As long as IDENUM does not perform speaker biometric identification, its voice/STT processing stays under the lighter Art. 5 regime** — and IDENUM should state clearly in its notice that it does not process voice for biometric identification.
- **Third-party data**: when a user dictates "Ahmet'i ara", IDENUM processes a third party's personal data. The household/personal-activity full exemption in **Art. 28(1)(a)** — "*Kişisel verilerin, üçüncü kişilere verilmemek ve veri güvenliğine ilişkin yükümlülüklere uyulmak kaydıyla gerçek kişiler tarafından tamamen kendisiyle veya aynı konutta yaşayan aile fertleriyle ilgili faaliyetler kapsamında işlenmesi*" — covers the **end user** as a natural person acting purely personally and not disclosing to third parties. It does **not** cover IDENUM, which is a commercial data controller receiving the data. (Note: the task's reference to "Article 28/ç" is a mis-citation; the relevant provision is Art. 28(1)(a).) IDENUM therefore owes a residual **Article 10 disclosure duty** to the mentioned third party. Because the data is collected indirectly and direct notice is usually impossible, the Aydınlatma Tebliği rule governs: inform within a reasonable time of obtaining the data ("makul süre"), at first contact if the data is used to contact the person, or at latest at first transfer (fiili imkânsızlık / ilgili kişiye ulaşılamaması). This was reinforced by KVKK's 2026 public announcement on third-party-sourced data, which states that obtaining data from a third party does not by itself provide a lawful basis. Practically, IDENUM should: minimise (store only what's needed for the task), address third-party data in its notice, and rely on Art. 5/2-c (contract) / Art. 5/2-f (legitimate interest) for this narrow processing.

### 5. Push notifications & children's data
- **Push notifications**: the KVKK's **"Mobil Uygulamalar Üzerinden Gönderilen Anlık Bildirimlere İlişkin Kamuoyu Duyurusu," published 14 January 2026**, requires **"parçalı açık rıza" (granular consent)** — each processing purpose must have a separate, independent opt-in: *"her bir amaç için ayrı ve bağımsız seçim imkânı sunulması gerektiği; aksi halde 'parçalı açık rıza (granularity)' ilkesinin ihlal edilmiş olacağı."* A single consent bundling service notifications with marketing is unlawful because it corrupts "özgür irade". Service-inherent notifications tied to the requested function can rest on contractual necessity, but **marketing/campaign notifications require separate opt-in** (and, for commercial electronic messages, İYS registration).
- **Children's data**: KVKK has no standalone children's regime analogous to GDPR Art. 8, but Board/guidance material treats children's data as warranting special protection, data minimisation and, where consent is the basis, the legal guardian's consent. Since IDENUM is a **general consumer app not targeted at minors**, the practical step is to set a minimum age (e.g. 18+) in the terms and state that the service is not directed at children.

### 6. Model text / official template
The Kurum's **"Aydınlatma Yükümlülüğünün Yerine Getirilmesi Rehberi"** contains official model notices (Örnek 1–4), including layered-notice ("katmanlı aydınlatma") examples. For a mobile/PWA, a **layered approach** is best practice: a short first-layer summary at the point of collection (identity, key purposes, link) plus a full second-layer notice (all Art. 10 elements, retention, transfers, rights). Critically, the Board's Principle Decision 2026/347 requires that the Aydınlatma Metni must NOT end with "okudum, kabul ediyorum / açık rıza veriyorum" tick-boxes — only "okudum ve anladım" is permitted — and consent must be a separate act under separate headings and separate checkboxes.

## Details

### Legal architecture IDENUM should adopt
IDENUM is the **veri sorumlusu** (data controller); Supabase is a **veri işleyen** (processor). Map each data category to a legal basis:

| Data category | Purpose | Legal basis | Consent? |
|---|---|---|---|
| Voice input (audio) + STT text | Deliver the assistant's core function | Art. 5/2-c contractual necessity | No |
| Person names dictated (contacts) | Execute the requested action (call/remind) | Art. 5/2-c; third-party bits Art. 5/2-f | No |
| One-time GPS location | Meeting/travel context for the request | Art. 5/2-c | No |
| Calendar/time reminders | Deliver reminder feature | Art. 5/2-c | No |
| Behavioural/preference profiling (personal_memory) | Personalisation / learning habits | Art. 5/1 explicit consent | Yes (granular) |
| Push notification subscription | Send notifications | Service-inherent: 5/2-c; marketing: consent | Marketing = yes |
| Supabase EU transfer | Hosting/auth/storage | Art. 9 Standart Sözleşme (SS-2) | No (not consent) |

### Cross-border transfer — the critical action
Because Frankfurt hosting is a continuous transfer and no adequacy decision exists, IDENUM must: (1) sign the KVKK **Standart Sözleşme type SS-2** with Supabase (unmodified); (2) file it with the Kurum within **5 business days** via the Bildirim Modülü; (3) disclose the transfer in the Aydınlatma Metni (recipient, country, purpose, that it is hosting-driven, and the safeguard used). The Board has fined controllers where a foreign-hosting transfer disclosure was inadequate or improperly bundled into consent (e.g. the 2022/249 technology-company decision on cloud hosting). Do NOT rely on an "verilerimin yurt dışına aktarılmasına rıza veriyorum" checkbox as the transfer mechanism for continuous hosting — that mechanism is now reserved for one-off "arızi" transfers only.

### What to state about no-ads / no-sale / no third-party training
Not strictly legally mandated, but **strongly recommended**: explicitly stating in the notice that IDENUM does not use the data for advertising, does not sell it, and does not use it to train third-party AI models beyond the app's own operation reinforces the purpose-limitation and transparency principles (Art. 4), narrows the scope of any consent, and pre-empts disputes. Given the 24 November 2025 generative-AI guidance's stance on training data, this is a meaningful trust and compliance signal.

### GDPR-adjacent best practice (for later expansion)
If IDENUM expands into the EU: GDPR Arts. 13–14 transparency (categories, legal basis, retention, recipients, transfers, automated decision-making); Art. 5 minimisation; DPIA under Art. 35 for large-scale/behavioural/voice processing; and the **EU AI Act Article 50** transparency obligations, which **apply from 2 August 2026** (Regulation (EU) 2024/1689; European Commission adopted guidelines 20 July 2026). Under Art. 50 users must be told they are interacting with an AI system; non-compliance carries fines up to **€15 million or 3% of worldwide annual turnover**, and this duty was NOT deferred by the Digital Omnibus package. Note that a privacy notice merely mentioning a chatbot does not discharge Art. 50, and an AI label does not discharge GDPR Arts. 13–14. Voiceprint/biometric identification would trigger GDPR Art. 9 explicit consent; plain STT does not.

### VERBİS
As a solo-founder early-stage startup (well under 50 employees and under the 100M TL annual balance-sheet threshold) whose **main activity** is not special-category data processing, IDENUM is likely **exempt from VERBİS registration**. The 2025 Board decision (2025/1572) raised the balance-sheet threshold to 100M TL and added a micro-scale exemption. However, VERBİS exemption is **NOT** exemption from KVKK compliance — the Art. 10 notice, security measures (Art. 12), and transfer rules all still apply.

## Recommendations
**Stage 1 — before launch (must-do):**
1. Draft two separate documents: an Aydınlatma Metni (Art. 10) and an Açık Rıza Metni (only for the optional items), per Principle Decision 2026/347. No "okudum kabul ediyorum" at the end of the notice — use "okudum ve anladım".
2. Execute the Supabase Standart Sözleşme (SS-2), file it with the Kurum within 5 business days via the Bildirim Modülü, and add a clear cross-border transfer clause to the notice.
3. Map every data category to a legal basis (table above); rely on contractual necessity for core features, and reserve consent for behavioural profiling and marketing notifications only.
4. Implement granular, separately-toggleable consents for (a) behavioural profiling and (b) marketing push — never bundled with each other or with the core service.

**Stage 2 — hardening:**
5. Write and maintain a Kişisel Veri Saklama ve İmha Politikası with concrete retention periods per category; reflect them in the notice (no "süresiz").
6. Run a DPIA (mahremiyet etki değerlendirmesi) given the combination of voice + location + behavioural profiling — flagged as high-risk in KVKK's AI guidance.
7. Add third-party-data handling: minimise, and address dictated third-party names ("Ahmet'i ara") in the notice; store only what the task needs.
8. State that the app is 18+ / not directed at children, and that voice is not used for biometric identification.

**Thresholds that change the plan:**
- If IDENUM adds voiceprint/speaker ID → Art. 6 biometric regime + mandatory explicit consent + stricter technical/administrative measures.
- If it uses data for advertising or to train third-party AI models → separate explicit consent and disclosure required.
- If employees reach 50 or the balance sheet exceeds 100M TL → VERBİS registration within 30 days of crossing the threshold.
- If the Kurul publishes an EU adequacy decision → the Standart Sözleşme step for Frankfurt hosting could be simplified.

## Caveats
- The 2026 fine figures are drawn from the KVKK revaluation table as reproduced by legal advisories (cottgroup, erdem-erdem) applying the 25.49% rate; confirm against the current official Kurum table before relying on exact amounts.
- Whether the personal_memory "identity" layer touches special-category data (e.g. health, beliefs, or political/religious views inferable from behaviour) needs case-by-case review; any special-category content pulls in Art. 6's stricter regime (including, generally, explicit consent and enhanced security).
- No Turkish Board decision squarely addresses the exact "app dictating a third party's name" fact pattern; the third-party conclusion is a sound inference from the statutory text (Art. 28(1)(a) limited to natural persons with no third-party disclosure) plus the Aydınlatma Tebliği and 2026 third-party-data announcement — not an on-point ruling.
- The KVKK has publicly stated it is negotiating adequacy with the EU, but as of 2026 no adequacy list is published; monitor for changes.
- This report is a legally-informed compliance briefing to guide drafting, not a substitute for review by a Turkish data-protection lawyer before publication of the actual metinler.